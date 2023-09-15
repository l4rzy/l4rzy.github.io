---
layout: post
title: "[CNPen] CNPen Mock Exam Writeup"
categories: hacking writeup network samba windows
---

# Introduction
I encountered those "challenges" when doing the mock exam for CNPen. They were not hard at all, but I did learn something interesting to share.

# First part
In the first part of the exam, they provided a leaked hash `94643dacaa6b1b9cd9724ddb050b5f01` for a user named `Peter`.
After successfully cracking the hash on [https://crackstation.net](https://crackstation.net/) I retried the password
I also knew that the type of the hash was NTLM

![Crack station](/assets/images/cnpen/cs1.png)

I did an nmap scan to look for running services
```
rzy@L4SPC % nmap -Pn -sV -p1-10000 s1.cnpen-mock.secops.group                                                         ~
Starting Nmap 7.94 ( https://nmap.org ) at 2023-09-14 19:58 CDT
Nmap scan report for s1.cnpen-mock.secops.group (54.172.231.84)
Host is up (0.055s latency).
rDNS record for 54.172.231.84: ec2-54-172-231-84.compute-1.amazonaws.com
Not shown: 9998 filtered tcp ports (no-response)
PORT     STATE SERVICE       VERSION
3389/tcp open  ms-wbt-server Microsoft Terminal Services
4455/tcp open  microsoft-ds  Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 34.44 seconds
```

Next, I'll try the RDP one on port 3389

![RDP1](/assets/images/cnpen/rdp1.png)

Let's try the other one, I know that `microsoft-ds` service is for file sharing, so let's install Samba and connect to the service

```
rzy@L4SPC % smbclient -U peter -p 4455 -L s1.cnpen-mock.secops.group                                                  ~
Password for [WORKGROUP\peter]:

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
        secret          Disk
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to s1.cnpen-mock.secops.group failed (Error NT_STATUS_IO_TIMEOUT)
Unable to connect with SMB1 -- no workgroup available
```
It didn't connect but it showed me a list of shared objects. Let's try smbclient with different arguments.

```
rzy@L4SPC % smbclient -U peter -p 4455 //s1.cnpen-mock.secops.group/secret                                            ~
Password for [WORKGROUP\peter]:
Try "help" to get a list of possible commands.
smb: \> get Secret.txt
getting file \Secret.txt of size 66 as Secret.txt (0.3 KiloBytes/sec) (average 0.3 KiloBytes/sec)
smb: \>
rzy@L4SPC % cat Secret.txt                                                                                            ~
flag{HbzBNrndXmTG3d6XwXdwchNpGBbUCRtLzeHrZaTvrQBpexpAZU7tuChxXGv7}%
```
# Second part
The second part of the exam was about OSINT. My objective is to find the leaked SSH key with one clue: `www.certharvest.com`
I used [WHOIS](https://www.whois.com/whois/certharvest.com) and `nslookup` to look for more info. Interestingly, it points to `localhost`.
```
rzy@L4SPC % nslookup www.certharvest.com                                                                              ~
Server:         172.22.208.1
Address:        172.22.208.1#53

Non-authoritative answer:
www.certharvest.com     canonical name = certharvest.com.
Name:   certharvest.com
Address: 127.0.0.1
```
I tried Duckduckgo to search for certharvest (because Google returned not much interesting information) and I found something intriguing: a Twitter (X) account that has posts about OSINT. Coincidence? I don't think so
![x1](/assets/images/cnpen/x1.png)

![x2](/assets/images/cnpen/x2.png)

One more clue: the email address `certharvest@proton.me`. I tried to look it up on different OSINT sites, no relevance found.

I tried to search on Github, and there was a repo named `certharvest`, for a moment I thought that was it, but no sensitive information found!

Let's try search Pastebin instead. And right of the bat:

![Pastebin](/assets/images/cnpen/pb1.png)

Now the last challenge is to use the ssh key to get to somewhere, specifically to read the flag on `s2.cnpen-mock.secops.group`

After setting up the key, let's go get the flag:

```
rzy@L4SPC % sftp -P 222 alice@s2.cnpen-mock.secops.group                                                              ~
Connected to s2.cnpen-mock.secops.group.
sftp> ls
alice
sftp> cd alice/
sftp> ls
flag.txt
sftp> get flag.txt
Fetching /alice/flag.txt to flag.txt
flag.txt                                                                              100%   67     0.7KB/s   00:00
sftp> ^D
rzy@L4SPC % cat flag.txt                                                                                              ~
flag{j7j9wTek9Mevc48NfNJD4eAWhi6FQRfaW22irWAcu2NBiVTcuHJ7qHA9g6tH}
```
And done!

# Conclusion
For an intermediate certificate, this is not hard. But this exam requires certain practical skills! 