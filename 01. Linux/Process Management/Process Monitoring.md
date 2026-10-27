# Linux Process Management and Monitoring
#linux #moniter
## 1. Listing Processes
When a Linux system becomes slow, the first thing I want to check is which processes are running and how much CPU or memory they are using.

### 1.1 Using `ps aux`

```bash
ps aux
```
This shows the running processes with their CPU usage, memory usage, PID, state, and command.
![[../../99 .Attachments/Process/ps_aux.png]]

If I want to find the processes using the most CPU:
```bash
ps aux --sort=-%cpu | head
```

For example:
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

If I want to find the processes using the most memory:
```bash
ps aux --sort=-%mem | head
```

Important columns I need to understand:

| Column | What I learned |
|---|---|
| `USER` | User who owns the process |
| `PID` | Process ID |
| `%CPU` | CPU usage |
| `%MEM` | Percentage of physical memory used |
| `VSZ` | Virtual memory size in KiB |
| `RSS` | Physical memory currently resident in RAM, in KiB |
| `STAT` | Current process state |
| `START` | Process start time |
| `TIME` | Total CPU time used by the process |
| `COMMAND` | Command used to run the process |

### 1.2 Using `ps -ef`

```bash
ps -ef
```
This also lists processes, but the output format is different from `ps aux`.
The columns I want to remember:

| Column | What I learned |
|---|---|
| `UID` | User who owns the process |
| `PID` | Process ID |
| `PPID` | Parent process ID |
| `C` | CPU utilization factor used by this format |
| `STIME` | Process start time |
| `TTY` | Controlling terminal |
| `TIME` | Total CPU time |
| `CMD` | Command and its arguments |

**Difference between `ps aux` and `ps -ef`:**

- `ps aux` is useful when I want to check CPU and memory usage.
- `ps -ef` is useful when I want to inspect PIDs and parent-child relationships.

Both commands list processes across the system, subject to visibility and permissions. The main difference is their output format.

---

## 2. Monitoring Processes in Real Time

### 2.1 Using `top`

```bash
top
```
Unlike `ps`, which gives me a snapshot, `top` continuously updates the process list and system information.
Useful keys inside `top`:

| Key | What it does |
|---|---|
| `P` | Sort processes by CPU usage |
| `M` | Sort processes by memory usage |
| `1` | Show individual CPU core statistics |
| `c` | Toggle full command-line display |
| `H` | Toggle thread display |
| `q` | Quit |

If I want `top` to start with the highest CPU users at the top:
```bash
top -o %CPU
```

To sort by memory usage:
```bash
top -o %MEM
```

### 2.2 Using `watch` with `ps`

I don't always need to keep `top` open. I can use `watch` to run a command repeatedly.
```bash
watch -n 2 'ps -eo pid,ppid,user,stat,%cpu,%mem,etime,time,comm --sort=-%cpu | head -15'
```

This refreshes the output every two seconds and shows the processes with the highest CPU usage.
What the options mean:

| Option | Meaning |
|---|---|
| `-e` | Select all processes |
| `-o` | Choose the columns I want |
| `--sort=-%cpu` | Sort by CPU usage, highest first |
| `head -15` | Show the first 15 lines |
| `watch -n 2` | Refresh every two seconds |

Because the output includes a header, `head -15` displays the header and up to 14 process rows.

---

## 3. Finding a Process That Uses Too Much CPU

I used this command to find the processes consuming the most CPU:
```bash
ps aux --sort=-%cpu | head
```

Example output from my lab:
```text
USER     PID  %CPU %MEM    VSZ   RSS TTY  STAT START TIME COMMAND
labex    219  99.5  0.0   4364  2976 ?    R    10:44 16:18 /bin/bash /home/labex/project/resource_hog.sh
```

Here, PID `219` is using almost 100% CPU. The command shows that it is running `resource_hog.sh`.
I want to investigate this process before trying to stop it.

### 3.1 Getting Details About a PID

```bash
ps -fp 219
```
My output:

```text
UID    PID  PPID  C STIME TTY TIME     CMD
labex  219     1 99 10:44 ?   00:17:29 /bin/bash /home/labex/project/resource_hog.sh
```

Now I can see more details about the process.

- `PID 219` identifies the process.
- `PPID 1` means its current parent is PID 1.
- `C 99` indicates high CPU activity in this output.
- `CMD` shows the script being executed.

A process can become a child of PID 1 if its original parent exits and it is reparented.

### 3.2 Choosing My Own Output Columns
Instead of displaying every column, I can choose the information I need.

```bash
ps -p 219 -o pid,ppid,user,stat,%cpu,%mem,etime,time,args
```

Useful format specifiers:

| Specifier | Meaning                                |
| --------- | -------------------------------------- |
| `pid`     | Process ID                             |
| `ppid`    | Parent process ID                      |
| `user`    | Effective user name                    |
| `%cpu`    | CPU usage                              |
| `%mem`    | Memory usage                           |
| `vsz`     | Virtual memory size                    |
| `rss`     | Resident memory size                   |
| `stat`    | Process state                          |
| `etime`   | Elapsed time since the process started |
| `time`    | Accumulated CPU time                   |
| `args`    | Full command and arguments             |

### 3.3 Checking Process Relationships

To display a process tree:
```bash
ps auxf
```

To display the children of a specific process:
```bash
ps -f --ppid 219
```

To inspect PID 219 again:
```bash
ps -fp 219
```

Understanding `PID` and `PPID` helps me figure out how processes are related and where they came from.

---

## 4. Finding Processes by Name

### 4.1 Using `pgrep`
I can search for a process without manually checking the entire output of `ps`.

```bash
pgrep -af critical_service
```

My lab output:
```text
220 /bin/bash /home/labex/project/critical_service.sh
```

The options mean:

- `-a` shows the full command line.
- `-f` searches the full command line instead of only the process name.

### 4.2 Inspecting the Process

```bash
ps -p 220 -o pid,comm,args
```

Example:
```text
PID  COMMAND          COMMAND
220  critical_servic  /bin/bash /home/labex/project/critical_service.sh
```

Here, `comm` shows the executable name, which may be truncated. `args` shows the command and its arguments.

### 4.3 Finding a Process by Executable Name

```bash
ps -f -C bash
```

This finds processes whose executable name is `bash`.

**Remember:** A shell script and its interpreter are not necessarily identified in the same way. For example, a script may appear as a Bash process, even though the script's filename is `resource_hog.sh`.

---

## 5. Terminating Processes
After identifying an unwanted process, I need to know how to stop it safely.

### 5.1 Using `kill`
I can request termination by PID:

```bash
kill 219
```

By default, this sends `SIGTERM` (signal 15). It gives the process a chance to exit normally and perform cleanup.
I can check whether it is still running:
```bash
ps -p 219
```

If there is no output, the process is no longer visible under that PID.

### 5.2 Using `SIGKILL`
If the process does not respond to `SIGTERM`, I can use:

```bash
kill -KILL 219
```

Or:

```bash
kill -9 219
```

`SIGKILL` cannot be caught or ignored, so the process cannot perform normal cleanup.
I should use this only when necessary. A process stuck in an uninterruptible kernel state may not disappear immediately, even after receiving `SIGKILL`.

### 5.3 Using `pkill`
I also tried using `pkill` in my lab.
My first attempt was:

```bash
pkill 219
```

But the process was still running.
I then tried:

```bash
pkill -9 219
```

It still appeared in the process list.

**What I learned:** `pkill` matches process names or patterns. It does not treat `219` as a PID just because it is a number.
To terminate a specific PID, I should use `kill 219`.
To terminate a process by its command-line pattern, I can use:

```bash
pkill -f resource_hog.sh
```

Then check the result:
```bash
pgrep -af resource_hog.sh
```

I can also stop my test service using:
```bash
pkill -f critical_service.sh
```

I should always check the target before using `pkill`, especially when the pattern could match multiple processes.

### 5.4 Useful Signals

```bash
kill -l
```

This lists the signals supported by my system.

| Signal | Number | Meaning |
|---|---|---|
| `SIGHUP` | 1 | Hangup; some applications use it to reload configuration |
| `SIGINT` | 2 | Interrupt |
| `SIGTERM` | 15 | Request graceful termination |
| `SIGKILL` | 9 | Force termination |

My usual order is to try `SIGTERM` first and use `SIGKILL` only if necessary.

---

## 6. Understanding Process States
The `STAT` column tells me the current state of a process.

| State | Meaning |
|---|---|
| `R` | Running or ready to run |
| `S` | Interruptible sleep |
| `D` | Uninterruptible sleep, often waiting for I/O |
| `T` | Stopped or traced |
| `Z` | Zombie process |

Some additional characters can appear alongside the main state:

- `s` — session leader.
- `l` — multithreaded process.
- `+` — foreground process group.

A high CPU percentage does not automatically mean a process is malicious or broken. I should investigate what it does, how long it has been consuming resources, and whether it is affecting other services.

---

## 7. Quick Command Reference

| What I want to do                        | Command                                           |
| ---------------------------------------- | ------------------------------------------------- |
| List processes                           | `ps aux`                                          |
| List processes in UNIX format            | `ps -ef`                                          |
| Find the highest CPU users               | `ps aux --sort=-%cpu \| head`                     |
| Find the highest memory users            | `ps aux --sort=-%mem \| head`                     |
| Monitor processes in real time           | `top`                                             |
| Sort `top` by CPU                        | `top -o %CPU`                                     |
| Sort `top` by memory                     | `top -o %MEM`                                     |
| Refresh a process list every two seconds | `watch -n 2 'ps aux --sort=-%cpu \| head'`        |
| Inspect a PID                            | `ps -fp 1234`                                     |
| Choose specific columns                  | `ps -p 1234 -o pid,ppid,user,stat,%cpu,%mem,args` |
| Search by command line                   | `pgrep -af process_name`                          |
| Find an executable by name               | `ps -f -C nginx`                                  |
| Show a process tree                      | `ps auxf`                                         |
| Find a process's children                | `ps -f --ppid 1234`                               |
| Show one user's processes                | `ps -fu username`                                 |
| Count processes                          | `ps -e --no-headers \| wc -l`                     |
| Request termination                      | `kill 1234`                                       |
| Force termination                        | `kill -9 1234`                                    |
| Terminate by command-line pattern        | `pkill -f pattern`                                |
| List background jobs                     | `jobs -l`                                         |
| Redirect output and errors               | `command > output.log 2>&1`                       |
| Follow a log file                        | `tail -f output.log`                              |
