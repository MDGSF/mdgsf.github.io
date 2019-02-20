---
layout: post
title: "[Linux][信号] 发送信号给进程"
date: 2019-02-16
author: mdgsf
comments: true
categories: Linux
tags: [Linux,Signal]
description: ""
published: true #default true
---

[https://bash.cyberciti.biz/guide/Sending_signal_to_Processes](https://bash.cyberciti.biz/guide/Sending_signal_to_Processes)


### kill - send a signal to a process

The default signal for kill is TERM. To list available signals, enter:

```
kill -l
```

Sample outputs:

```
 1) SIGHUP	 2) SIGINT	 3) SIGQUIT	 4) SIGILL
 5) SIGTRAP	 6) SIGABRT	 7) SIGBUS	 8) SIGFPE
 9) SIGKILL	10) SIGUSR1	11) SIGSEGV	12) SIGUSR2
13) SIGPIPE	14) SIGALRM	15) SIGTERM	16) SIGSTKFLT
17) SIGCHLD	18) SIGCONT	19) SIGSTOP	20) SIGTSTP
21) SIGTTIN	22) SIGTTOU	23) SIGURG	24) SIGXCPU
25) SIGXFSZ	26) SIGVTALRM	27) SIGPROF	28) SIGWINCH
29) SIGIO	30) SIGPWR	31) SIGSYS	34) SIGRTMIN
35) SIGRTMIN+1	36) SIGRTMIN+2	37) SIGRTMIN+3	38) SIGRTMIN+4
39) SIGRTMIN+5	40) SIGRTMIN+6	41) SIGRTMIN+7	42) SIGRTMIN+8
43) SIGRTMIN+9	44) SIGRTMIN+10	45) SIGRTMIN+11	46) SIGRTMIN+12
47) SIGRTMIN+13	48) SIGRTMIN+14	49) SIGRTMIN+15	50) SIGRTMAX-14
51) SIGRTMAX-13	52) SIGRTMAX-12	53) SIGRTMAX-11	54) SIGRTMAX-10
55) SIGRTMAX-9	56) SIGRTMAX-8	57) SIGRTMAX-7	58) SIGRTMAX-6
59) SIGRTMAX-5	60) SIGRTMAX-4	61) SIGRTMAX-3	62) SIGRTMAX-2
63) SIGRTMAX-1	64) SIGRTMAX	
```

### kill command Examples

The kill command can send all of the above signals to commands and process. However, commands only give response if they are programmed to recognize those signals. Particularly useful signals include:

1. SIGHUP (1) - Hangup detected on controlling terminal or death of controlling process.
2. SIGINT (2) - Interrupt from keyboard.
3. SIGKILL (9) - Kill signal i.e. kill running process.
4. SIGSTOP (19) - Stop process.
5. SIGCONT (18) - Continue process if stopped.

To send a kill signal to PID # 1234 use:

```
kill -9 1234
```

OR

```
kill -KILL 1234
```

OR

```
kill -SIGKILL 1234
```

### killall - kill processes by name

killall sends a signal to all processes running any of the specified commands . If no signal name is specified, SIGTERM is sent. To terminate all firefox process (child and parent), enter:

```
killall processName
killall firefox-bin
```

To send a KILL signal to firefox, enter:

```
killall -s SIGKILL firefox-bin
```

### pkill - kill process

The pkill command is another command with additional options to kill process by its name, user name, group name, terminal, UID, EUID, and GID. It will send the specified signal (by default SIGTERM) to each process instead of listing them on stdout. To send a kill signal to php-cgi process, enter:

```
pkill -KILL php-cgi
```

The above example will kill all users php-cgi process. However, -u option will kill only processes whose effective user ID is set to vivek:

```
pkill -KILL -u vivek php-cgi
```

Make sshd reread its configuration file, enter:

```
pkill -HUP sshd
```

