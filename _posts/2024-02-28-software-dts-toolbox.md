---
layout: post
title: "[Software] I wrote a small tool for automating the boring SOC tasks"
categories: python soc software automation
---

## Introduction
SOC work is not always boring, but it involves lots of repetitive tasks. Looking at the logs, IPs, hashes, URLs are things that we can do better. For that reason I spent some time to write a [small tool](https://github.com/l4rzy/toolbox) to automate those tasks.

My idea was to write a tool that automatically looks up for patterns in my clipboard. If the pattern is known, it does the job for me. No more switching tabs or waiting for virustotal to load.

This post is to share the troubles I had when developing it, as well as to introduce the tool.

![toolbox](/assets/images/dtstoolbox/s1.png)

## Connect to the internet
Almost all big corporate networks use some kind of proxy to inspect inbound and outbound network traffics of their employees. This acts as a layer of protection for antivirus/content analysis and url/ip filtering. One problem with it is it isn't always easy to connect to the internet for apps that are not proxy-aware.

Furthermore, they usually have authentication on the proxy. This authentication is in most case against Active Directory. I don't know exactly how proxy-aware apps authenticate with the proxy but NTLM and Kerberos surely require extra steps. So I decided to choose Basic Authentication.

Another problem remains, corporate's CA is not always working well with openssl. Since I am using Python, both `http.client` and `requests` use openssl bundled with Python. To overcome this, I decided to use pycurl, which is based on libcurl. Libcurl on Windows use SCHANNEL by default, so it makes everything easier.

## Non-blocking UI
Since Tkinter doesn't have native support for `asyncio`, I achieved a non-blocking UI by using python threads and shared memory. When the program needs to connect to the internet, it spawns a thread with a pointer to the main UI as a parameter. When the network task is done, it calls back to the UI to render the result. 

## DNS problem
Another small problem is with the DNS lookup functionality. System DNS via `socket.gethostbyname` takes too long to resolve a non-existing domain/address. Somehow the timeout did not work at all, so for non-exsiting domain/ip, it took so long to wait for the result from socket. I then tried dnspython, which was a fantasic library but unfortunately had problem with DNS authentication.

I reluctantly switched back to `socket` and tried to find a way to cancel the thread when timed out. But then I found one trick of Tkinter, which was `after` function. So I solved the probem without using any extra thread. The dns resolver worker will return `None` when timed out, the `socket` thread is still running, but the result of it will be discarded.

## Tunnel server
Since the tool is designed to use by the whole team, this feature is for sharing the same set of api tokens for the whole team. The idea is to have a server that will supply api tokens to user requests before making them. Furthermore, requests of the same content can be cached for better latency.

## OCR
Another thing I noticed was team collaboration on call. Threat hunting on Teams calls involves copy and paste, or inspect IPs, URLs on the sharing screen. For that reason I added OCR functionality with tesseract library. Now when somebody's sharing something, I can just screenshot and paste to my toolbox.

![ocr](/assets/images/dtstoolbox/ocr.png)

And analyze the content too

![ocr2](/assets/images/dtstoolbox/analyze.png)


## Conclusion
It was a fun journey. It's been quite some time since the last time I wrote something not trivial. Although my software is not perfect, I'm working on it in my free time to improve it even more.

Checkout the source code: [https://github.com/l4rzy/toolbox](https://github.com/l4rzy/toolbox)