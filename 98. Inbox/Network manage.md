# Linux Network Management and Troubleshooting

[[linux]] [[network]] [[troubleshooting]]
## Overview

When a service becomes unreachable, I follow a simple top-down checklist:

1. Check network interfaces and IP addresses.
2. Test connectivity to a remote host.
3. Inspect open ports and listening services.
4. Check the firewall rules.

This note walks through that exact sequence using a real lab scenario.

---

## 1. Checking Network Interfaces

First, I need basic interface details.

```bash
ip addr
```

```text
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 8500 qdisc mq state UP group default qlen 1000
    link/ether 00:16:3e:10:c2:87 brd ff:ff:ff:ff:ff:ff
    altname enp0s5
    altname ens5
    inet 10.0.1.40/24 metric 100 brd 10.0.1.255 scope global dynamic eth0
       valid_lft 1892159913sec preferred_lft 1892159913sec
    inet6 fe80::216:3eff:fe10:c287/64 scope link
       valid_lft forever preferred_lft forever
3: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default
    link/ether 02:42:c7:92:ba:1a brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
       valid_lft forever preferred_lft forever
```

For a double check, I also use `ifconfig`.

```bash
ifconfig
```

```text
docker0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        inet 172.17.0.1  netmask 255.255.0.0  broadcast 172.17.255.255
        ether 02:42:c7:92:ba:1a  txqueuelen 0  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 8500
        inet 10.0.1.40  netmask 255.255.255.0  broadcast 10.0.1.255
        inet6 fe80::216:3eff:fe10:c287  prefixlen 64  scopeid 0x20<link>
        ether 00:16:3e:10:c2:87  txqueuelen 1000  (Ethernet)
        RX packets 44550  bytes 64583373 (64.5 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 13258  bytes 2529873 (2.5 MB)
        TX errors 0  dropped 0  overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 382  bytes 40155 (40.1 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 382  bytes 40155 (40.1 KB)
        TX errors 0  dropped 0  overruns 0  carrier 0  collisions 0
```

At this point, all interfaces are `UP` and every interface has an IP address assigned.

### What I look for

| Field | Why it matters |
|---|---|
| `state UP` / `flags=UP` | The interface is enabled |
| `inet` line | IPv4 address is assigned |
| `inet6` line | IPv6 address is assigned |
| `link/ether` | MAC address |

---

## 2. Testing Connectivity

Now I want to test connectivity to a remote host. The best first tool is `ping`.

```bash
ping -c 3 8.8.8.8
```

```text
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=119 time=1.69 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=119 time=1.69 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=119 time=1.71 ms

--- 8.8.8.8 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2002ms
rtt min/avg/max/mdev = 1.686/1.697/1.713/0.011 ms
```

**What this tells me:**

- `0% packet loss` means the host is reachable.
- Low `rtt` (round-trip time) means the connection is fast.

If `ping` fails, I check the interface state, routing, and firewall before going further.

---

## 3. Checking Open Ports

Next, I check which ports are listening on the machine.

```bash
ss -tuln
```

```text
Netid  State   Recv-Q  Send-Q  Local Address:Port     Peer Address:Port  Process
udp    UNCONN  0       0       0.0.0.0:5353           0.0.0.0:*
udp    UNCONN  0       0       0.0.0.0:34185          0.0.0.0:*
udp    UNCONN  0       0       127.0.0.53%lo:53       0.0.0.0:*
udp    UNCONN  0       0       10.0.1.40%eth0:68      0.0.0.0:*
udp    UNCONN  0       0       127.0.0.1:323          0.0.0.0:*
udp    UNCONN  0       0       0.0.0.0:3001           0.0.0.0:*
udp    UNCONN  0       0       [::]:5353              [::]:*
udp    UNCONN  0       0       [::]:60726             [::]:*
udp    UNCONN  0       0       [::1]:323              [::]:*
tcp    LISTEN  0       4096    127.0.0.1:39469        0.0.0.0:*
tcp    LISTEN  0       4096    127.0.0.53%lo:53       0.0.0.0:*
tcp    LISTEN  0       128     0.0.0.0:22             0.0.0.0:*
tcp    LISTEN  0       511     0.0.0.0:3000           0.0.0.0:*
tcp    LISTEN  0       5       0.0.0.0:3001           0.0.0.0:*
tcp    LISTEN  0       128     0.0.0.0:3002           0.0.0.0:*
tcp    LISTEN  0       5       0.0.0.0:8000           0.0.0.0:*
```

### What the flags mean

| Flag | Meaning |
|---|---|
| `-t` | TCP sockets |
| `-u` | UDP sockets |
| `-l` | Listening sockets only |
| `-n` | Show numeric addresses (no DNS lookup) |

### Filtering the output

To check one specific port:

```bash
ss -tuln | grep 8000
```

```text
tcp   LISTEN 0      5             0.0.0.0:8000       0.0.0.0:*
```

This confirms a TCP listener on port `8000`.

**Note:** `0.0.0.0:8000` means the service is listening on all IPv4 interfaces. If it showed `127.0.0.1:8000`, it would only accept local connections.

---

## 4. Checking the Firewall

The last thing I check is the firewall. On Ubuntu, that's usually `ufw`.

```bash
sudo ufw status
```

```text
Status: inactive
```

The firewall is not blocking anything yet. I add rules for the ports I want reachable:

```bash
sudo ufw allow 22/tcp
```

```text
Rules updated
Rules updated (v6)
```

```bash
sudo ufw allow 8000/tcp
```

```text
Rules updated
Rules updated (v6)
```

Now I enable the firewall:

```bash
sudo ufw enable
```

```text
Firewall is active and enabled on system startup
```

> **Gotcha:** `ufw enable` needs root. Running it without `sudo` gives:
> `ERROR: You need to be root to run this script`.

Final check:

```bash
sudo ufw status
```

```text
Status: active

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW       Anywhere
8000/tcp                   ALLOW       Anywhere
22/tcp (v6)                ALLOW       Anywhere (v6)
8000/tcp (v6)              ALLOW       Anywhere (v6)
```

### Important ufw notes

- `ufw allow` adds rules **without** enabling the firewall. `ufw enable` actually turns it on.
- Rules are added for both IPv4 and IPv6 automatically.
- Always allow SSH (`22/tcp`) **before** enabling, or I can lock myself out of a remote server.

---

## 5. Troubleshooting Flow (Summary)

```
ip addr / ifconfig      →   Is the interface UP? Does it have an IP?
        │
        ▼
ping <remote>           →   Can I reach the outside world?
        │
        ▼
ss -tuln                →   Is the service listening? On which interface?
        │
        ▼
sudo ufw status         →   Is the firewall blocking the port?
```

By checking these four layers in order, I can usually pinpoint the problem quickly.

---

## 6. Quick Command Reference

| What I want to do | Command |
|---|---|
| Show IP addresses | `ip addr` |
| Show interfaces (legacy) | `ifconfig` |
| Show only UP interfaces | `ip link show up` |
| Show routing table | `ip route` |
| Test reachability | `ping -c 3 8.8.8.8` |
| Trace the path to a host | `traceroute 8.8.8.8` |
| List listening TCP/UDP ports | `ss -tuln` |
| Filter a specific port | `ss -tuln \| grep 8000` |
| Show which process owns a port | `sudo ss -tulnp` |
| Check firewall status | `sudo ufw status` |
| Allow a port | `sudo ufw allow 8000/tcp` |
| Enable the firewall | `sudo ufw enable` |
| Disable the firewall | `sudo ufw disable` |
| Delete a rule | `sudo ufw delete allow 8000/tcp` |
