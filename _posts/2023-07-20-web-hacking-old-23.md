---
layout: post
title: "[Webhacking.kr] Old-23 writeup"
categories: hacking writeup web
---

## Introduction

This is a straightforward XSS challenge. However, this one is a blackbox: You can not see anything in the code.

## Initial testing

I did a look around the prompt to understand how the filter works.

| Test       | Result     |
| ---------- | ---------- |
| `<script>` | no hack 🚫 |
| `<sc`      | no hack 🚫 |
| `<c`       | worked ✅  |
| `<!--`     | worked ✅  |
| `1234`     | worked ✅  |
| `abc`      | no hack 🚫 |
| `a b c`    | worked ✅  |
| `<<<`      | worked ✅  |

So far, we know that it doesn't accept a sequence of characters. How about we escape the characters using HTML escape code?
Let's try
`&#60&#115&#99&#114&#105&#112&#116&#62&#97&#108&#101&#114&#116&#40&#49&#41&#59&#60&#47&#115&#99&#114&#105&#112&#116&#62`

![Test1](/assets/images/old-23/test1.png)

Yay, we did it! Except, that was just text. And of course, the script was not executed.

Let's look at the inspection to see why

![Test1 Inspection](/assets/images/old-23/test1_inspect.png)

I was naive enough, although Firefox rendered the whole thing in text, those characters were escaped. We can make sure by using curl

![Test1 Curl](/assets/images/old-23/test1_curl.png)

## NULL byte in the URL

I did a little more research on the URL escape since the text we enter in the prompt is a parameter in the URL.
I wrote a small script

```python
script = '<script>alert(1);</script>'
print('%00'.join(script))
```

And we'll use it on the URL, because if we use it in the prompt, the NULL bytes will get escaped again and it will not work.

![Pwned](/assets/images/old-23/pwned.png)

And the challenge is solved!
