---
layout: post
title: "[Network] How we bypassed Meganet's anti-hotspoting protection"
categories: network
---

## Raspberry Pi, NAT, IP forwarding
Recently, I've bought a Raspberry Pi to craft some stuff, the first thing I've though of is to make a simple wifi hotspot, sharing my internet to my roommates. I found this, which is a very nice tutorial, I set up everything as written in the tutorial and everything should work the way it was supposed to be but there was one thing I couldn't figure out: The internet was inaccessible, whenever I connect to the Raspberry Pi, it redirects me to the login page although I've logged in to Meganet.

![Meganet graph](/assets/images/meganet/g.png)

Meganet is a company which provides wifi internet solution in campus infrastructures like university and dormitory. They have a database which maintains user info, whenever you connect to Meganet's network, a luxury HTTP login page (shrugs) appears and tells you to fill-in your account to use the internet. But since we can use any wifi-hotspot program to share our internet with only one account (this hurts Meganet's benefit), they used some techniques to prevent this as much as they could.

## And TTL?

I was stuck until my friend came over and helped me to figure it out. First, we though it was because of MAC filtering, but it was wrong since all the packets which are forwarded using NAT carry the IP addresses and the MAC addresses of the Raspberry Pi. This could be explained with ease by this brilliant graph:

![Network](/assets/images/meganet/nat.png)

So the packets from Raspberry Pi and the packets which are forwarded by Raspberry Pi must had some differences, said my friend. By using tcpdump, we figured out that only one value changed: the TTL value. One more Google query to know that the TTL value is different based on the OS, on GNU/Linux system, it's usually 64 and on Windows system, it's usually 128. The TTL value of the packet is subtracted by 1 on each node it passed over, so assume the packet from our Client has the TTL value of 64, it will be 63 after being translated by NAT.

![Network](/assets/images/meganet/re.png)

By then we knew the trick, it led us to 2 options: to change the TTL value of the Client (sounds bad) and to change the TTL value of packet on Raspberry Pi. Of course we chose the latter option, thanks to iptables, this could be done with only one simple command:

```sh
iptables -t mangle -A PREROUTING -i wlan0 -j TTL --ttl-set 65
```

And hooray, it worked as expected!
## Conclusion

Although I know that they (Meganet) prevents us from sharing internet not only because of it hurts theirs benefit, it's also because many hotspots may cause wifi signal distorsion. But anyway, thanks to Meganet, we've learnt something about networking and had fun together ;)