# Introduction

Welcome, Junior System Administrator! It's a busy Monday morning at "LabEx," and a critical alert just came in: the main application server is experiencing a significant slowdown, affecting all users. The senior administrators are tied up in an emergency meeting, and it's up to you to investigate and stabilize the system.

This is your moment to shine. Your mission is to dive into the server's command line, diagnose the issue by inspecting the running processes, neutralize any resource-hogging culprits, and ensure essential services remain operational. By the end of this challenge, you'll have proven your ability to manage a live Linux environment under pressure, a core skill for any system administrator.

# Listing Active System Processes

Your first step as the Process Overseer is to get a complete picture of what's currently running on the server. A static snapshot of all active processes will help you begin your investigation and identify anything unusual.

```bash
ps -aux
```
i can get all deatils use it,But i want do more things.
![[../99. Attachments/Process/ps_aux.png]]

```bash
ps -ef
```

![[../99. Attachments/Process/ps_ef.png]]

Both commands list all processes, but the default columns differ:

|Command|Best for|Notable columns|
|---|---|---|
|`ps aux`|Resource checks and quick process inspection|`%CPU`, `%MEM`, `VSZ`, `RSS`, `STAT`, `COMMAND`|
|`ps -ef`|Parent-child context and traditional UNIX output|`UID`, `PID`, `PPID`, `STIME`, `CMD`|

```bash
watch -n 2 'ps -eo pid,ppid,user,stat,%cpu,%mem,etime,time,comm --sort=-%cpu | head -15'
```

![[../99. Attachments/Process/watch.png]]

---

in top commnd once i want use sort=cpu i can use
```bash
top -o %CPU
```

![[../99. Attachments/Process/top_CPU.png]]

Watch CPU utilization, memory, load average, process states, and the changing process list.

Useful keys while `top` is open:

- `P` — sort by CPU usage.
- `M` — sort by memory usage.
- `1` — show individual CPU core usage.
- `c` — show full command lines.
- `H` — toggle thread display.
- `q` — quit.

---

All these things i see.
```bash
ps aux --sort=-%cpu | head
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
labex        219 99.5  0.0   4364  2976 ?        R    10:44  16:18 /bin/bash /home/labex/project/resource_hog.sh
root           1  0.0  0.0  11212  3768 ?        Ss   10:44   0:00 /bin/bash /etc/shiyanlou/sbin/init.sh
root          21  0.0  0.1  40832 28464 ?        S    10:44   0:00 /usr/bin/python3 /usr/bin/supervisord -n
root          22  0.0  0.0  15432  9412 ?        S    10:44   0:00 sshd: /usr/sbin/sshd -D [listener] 0 of 10-100 startups
root          23  0.0  0.0  14048  5136 ?        S    10:44   0:00 su labex -c vncserver -fg -disableBasicAuth -publicIp 0.0.0.0
labex         24  0.0  0.1  40312 30908 ?        Ss   10:44   0:00 /usr/bin/perl /usr/bin/vncserver -fg -disableBasicAuth -publicIp 0.0.0.0
labex         36  0.0  0.5 313948 83072 ?        Sl   10:44   0:00 /usr/bin/Xvnc :1 -disableBasicAuth -publicIp 0.0.0.0 -ImprovedHextile 1 -QueryConnect 0 -fp /usr/share/fonts/X11//misc,/usr/share/fonts/X11//Type1 -VideoTime 5 -IgnoreClientSettingsKasm 1 -WebpVideoQuality -1 -interface 0.0.0.0 -depth 24 -AcceptSetDesktopSize 1 -AcceptKeyEvents 1 -udpFullFrameFrequency 0 -MaxDisconnectionTime 0 -httpd /usr/share/kasmvnc/custom_www -AcceptPointerEvents 1 -AcceptCutText 1 -DLP_Log off -RawKeyboard 0 -DLP_ClipSendMax 0 -AllowOverride AcceptPointerEvents,SendCutText,AcceptCutText,SendPrimary -UseIPv6 0 -SendPrimary 0 -auth /home/labex/.Xauthority -KasmPasswordFile /home/labex/.kasmpasswd -MaxVideoResolution 1920x1080 -DLP_ClipAcceptMax 0 -sslOnly 0 -DLP_ClipTypes chromium/x-web-custom-data,text/html,image/png -websocketPort 3001 -DynamicQualityMax 8 -SendCutText 1 -MaxIdleTime 0 -DLP_KeyRateLimit 0 -BlacklistTimeout 10 -AvoidShiftNumLock 0 -PrintVideoArea 0 -TreatLossless 10 -desktop 6add6d1bd37ba9a2e8ab7848:1 (labex) -DLP_RegionAllowRelease 0 -IdleTimeout 0 -drinode /dev/dri/renderD128 -DLP_RegionAllowClick 0 -key /etc/ssl/private/ssl-cert-snakeoil.key -Log *:stdout:100 -JpegVideoQuality -1 -CompareFB 2 -VideoScaling 2 -http-header Cross-Origin-Embedder-Policy=require-corp -http-header Cross-Origin-Opener-Policy=same-origin -DynamicQualityMin 7 -QueryConnectTimeout 10 -VideoArea 45 -DLP_ClipDelay 0 -DisconnectClients 0 -VideoOutTime 3 -BlacklistThreshold 5 -FrameRate 60 -geometry 1024x768 -MaxConnectionTime 0 -UseIPv4 1 -RectThreads 0 -cert /etc/ssl/certs/ssl-cert-snakeoil.pem -rfbwait 30000 -rfbport 5901 -rfbauth /home/labex/.vnc/passwd
labex         46  0.0  0.0  11212  3628 ?        S    10:44   0:00 sh -c { echo 'Running /home/labex/.vnc/xstartup'; /home/labex/.vnc/xstartup; } >> '/home/labex/.vnc/6add6d1bd37ba9a2e8ab7848:1.log' 2>&1
labex         47  0.0  0.0  11212  2248 ?        S    10:44   0:00 sh -c { echo 'Running /home/labex/.vnc/xstartup'; /home/labex/.vnc/xstartup; } >> '/home/labex/.vnc/6add6d1bd37ba9a2e8ab7848:1.log' 2>&1

```

i most importtand part is `PID=219`, So get more deatils about that.

```bash
labex:project/ $ ps -fp 219
UID          PID    PPID  C STIME TTY          TIME CMD
labex        219       1 99 10:44 ?        00:17:29 /bin/bash /home/labex/project/resource_hog.sh
```


Useful format specifiers include:

|Specifier|Description|
|---|---|
|`pid`|Process ID|
|`ppid`|Parent process ID|
|`user`|Effective user name|
|`%cpu`|CPU usage|
|`%mem`|Memory usage|
|`vsz`|Virtual memory size in KiB|
|`rss`|Resident memory in KiB|
|`stat`|Process state|
|`tty`|Controlling terminal|
|`lstart`|Full process start time|
|`etime`|Elapsed time since start|
|`comm`|Executable name|
|`args`|Full command with arguments|

| Task                                   | Command                          |
| -------------------------------------- | -------------------------------- |
| Show current terminal processes        | `ps`                             |
| List all processes with CPU and memory | `ps aux`                         |
| List all processes in UNIX full format | `ps -ef`                         |
| Show full command lines                | `ps auxww`                       |
| Find by command name                   | `ps -f -C nginx`                 |
| Find by PID                            | `ps -fp 1234`                    |
| Find by parent PID                     | `ps -f --ppid 1234`              |
| Show one user’s processes              | `ps -fu username`                |
| Sort by memory                         | `ps aux --sort=-%mem \| head`    |
| Sort by CPU                            | `ps aux --sort=-%cpu \| head`    |
| Custom columns                         | `ps -eo pid,user,%cpu,%mem,args` |
| Show process tree                      | `ps auxf`                        |
| Show threads                           | `ps -fL -C process`              |
| Count processes                        | `ps -e --no-headers \| wc -l`    |

`/home/labex/project/resource_hog.sh`

```bash
#!/bin/bash
echo "Starting resource hog..."
while true; do
    :
done
```

```bash
labex:project/ $ pgrep -af critical_service

220 /bin/bash /home/labex/project/critical_service.sh
```

```
labex:project/ $ ps -p 220 -o pid,comm,args
    PID COMMAND         COMMAND
    220 critical_servic /bin/bash /home/labex/project/critical_service.sh
```

```
labex:project/ $ ps -p 219 -o pid,comm,args
    PID COMMAND         COMMAND
    219 resource_hog.sh /bin/bash /home/labex/project/resource_hog.sh
```

```
abex:project/ $ pkill 219
labex:project/ $ pkill 220
labex:project/ $ ps aux | grep resource_hog
labex        219 99.5  0.0   4364  2976 ?        R    10:44  26:36 /bin/bash /home/labex/project/resource_hog.sh
labex       1659  0.0  0.0   3472  1880 pts/3    S+   11:11   0:00 grep --color=auto --exclude-dir=.bzr --exclude-dir=CVS --exclude-dir=.git --exclude-dir=.hg --exclude-dir=.svn --exclude-dir=.idea --exclude-dir=.tox resource_hog
labex:project/ $ pkill -9 219
labex:project/ $ ps aux | grep resource_hog
labex        219 99.6  0.0   4364  2976 ?        R    10:44  26:53 /bin/bash /home/labex/project/resource_hog.sh
labex       1680  0.0  0.0   3472  1884 pts/3    S+   11:11   0:00 grep --color=auto --exclude-dir=.bzr --exclude-dir=CVS --exclude-dir=.git --exclude-dir=.hg --exclude-dir=.svn --exclude-dir=.idea --exclude-dir=.tox resource_hog
labex:project/ $ man pkill                 
labex:project/ $ pkill resource_hog.sh
labex:project/ $ man pkill            
labex:project/ $ ps aux | grep resource_hog
labex       1806  0.0  0.0   3472  1832 pts/3    S+   11:12   0:00 grep --color=auto --exclude-dir=.bzr --exclude-dir=CVS --exclude-dir=.git --exclude-dir=.hg --exclude-dir=.svn --exclude-dir=.idea --exclude-dir=.tox resource_hog
labex:project/ $ pkill critical_service.sh 
labex:project/ $ 
```

---

```bash
labex:project/ $ ps -eo pid,ppid,user,stat,%cpu,%mem,etime,time,comm --sort=-%cpu | head -15    
    PID    PPID USER     STAT %CPU %MEM     ELAPSED     TIME COMMAND
      1       0 root     Ss    0.0  0.0       29:04 00:00:00 init.sh
     21       1 root     S     0.0  0.1       29:03 00:00:00 supervisord
     22      21 root     S     0.0  0.0       29:02 00:00:00 sshd
     23      21 root     S     0.0  0.0       29:02 00:00:00 su
     24      23 labex    Ss    0.0  0.1       29:02 00:00:00 vncserver
     36       1 labex    Sl    0.0  0.5       29:02 00:00:00 Xvnc
     46      24 labex    S     0.0  0.0       29:02 00:00:00 sh
     47      46 labex    S     0.0  0.0       29:02 00:00:00 sh
     48      47 labex    Sl    0.0  0.4       29:02 00:00:00 xfce4-session
     57       1 labex    S     0.0  0.0       29:02 00:00:00 dbus-launch
     58       1 labex    Ss    0.0  0.0       29:02 00:00:00 dbus-daemon
     60       1 labex    Sl    0.0  0.0       29:02 00:00:00 at-spi-bus-laun
     65      60 labex    S     0.0  0.0       29:02 00:00:00 dbus-daemon
     69       1 labex    Sl    0.0  0.0       29:02 00:00:00 xfconfd
```

```
labex:project/ $ nohup ./data_processor.sh &
[1] 1938
nohup: ignoring input and appending output to 'nohup.out'                                                                                                                                                                              
labex:project/ $ 
[1]  + 1938 done       nohup ./data_processor.sh
labex:project/ $ 
labex:project/ $ ls
critical_service.sh  data_processor.sh  nohup.out  resource_hog.sh
labex:project/ $ cat nohup.out 
Starting data processing at Sun Oct 25 11:14:51 CST 2026
Data processing complete at Sun Oct 25 11:14:56 CST 2026
labex:project/ $ ps aux | grep data_processor
labex       2036  0.0  0.0   3472  1828 pts/3    S+   11:15   0:00 grep --color=auto --exclude-dir=.bzr --exclude-dir=CVS --exclude-dir=.git --exclude-dir=.hg --exclude-dir=.svn --exclude-dir=.idea --exclude-dir=.tox data_processor
labex:project/ $ 
```

```
abex:project/ $ nohup ./data_processor.sh > processor.log 2>&1 & 
[1] 2272
labex:project/ $ cat processor.log 
nohup: ignoring input
Starting data processing at Sun Oct 25 11:19:04 CST 2026
labex:project/ $ 
[1]  + 2272 done       nohup ./data_processor.sh > processor.log 2>&1
labex:project/ $ cat processor.log 
nohup: ignoring input
Starting data processing at Sun Oct 25 11:19:04 CST 2026
Data processing complete at Sun Oct 25 11:19:09 CST 2026
labex:project/ $ 
```

