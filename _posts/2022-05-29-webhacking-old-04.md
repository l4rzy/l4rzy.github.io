---
layout: post
title: "[Webhacking.kr] Old-04 writeup"
categories: hacking
---

## Introduction
We start with a very compact php code:

```php
<?php
  sleep(1); // anti brute force
  if((isset($_SESSION['chall4'])) && ($_POST['key'] == $_SESSION['chall4'])) solve(4);
  $hash = rand(10000000,99999999)."salt_for_you";
  $_SESSION['chall4'] = $hash;
  for($i=0;$i<500;$i++) $hash = sha1($hash);
?>
```

So basically, the challenge is solved only when the `key` from POST request is the same as `$_SESSION['chall4']`. But the `$_SESSION['chall4']` is generated randomly and hashed 500 times with `sha1` algorithm.

## Solution
The solution is simple: making a pre-computed hash table
```python
import time
import hashlib
import sys
import time

def hash500(og):
    h = og
    for i in range(500):
        h = hashlib.sha1(h.encode()).hexdigest()
    return h

def generate():
    with open('dict.txt', 'w+') as f:
        for i in range(10000000, 99999999):
            if i % 10000 == 0:
                print(f'i = {i}')
            s = f"{i}salt_for_you"
            f.write(f'{i} {hash500(s)}\n')


generate()
```
This one will take a while on a modern PC, it took many hours with my Intel Core i5 6300U, but took roughly an hour with my MacBook Air M1. One small trick is you don't have to wait for the generation to complete to check. Since the hash is generated randomly, we can reload the page to get a new one and search on the file, the chance to get the answer is fairly high.

## Conclusion
This one is not hard, all you need is a bit patience to bruteforce.