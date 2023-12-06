---
layout: post
title: "[Webhacking.kr] Child toctou 🚲 writeup part 1"
categories: hacking writeup web
---

## Introduction
Link: [http://webhacking.kr:10020/](http://webhacking.kr:10020/)

This was the least solved challenge on this site, why not giving myself some real challenge!

## Into the detail

![shell](/assets/images/child-toctou/shell.png)

It is pretty much like its predecessor [baby toctou]({{ site.baseurl }}{% link _posts/2023-06-09-web-hacking-baby-toctou.md %}). It has a shell, all you can do on that shell is to list and cat some source code, but not the flag. Let's look at the `api.php` code:

```php
<?php
  // system($_GET['q']);
  $cmd = $_GET['q'];
  if($cmd){
    if(count(explode(":",$_SERVER['HTTP_HOST'])) > 1)
        $_SERVER['HTTP_HOST'] = explode(":",$_SERVER['HTTP_HOST'])[0];
    if(!preg_match("/^[a-z0-9-.]+$/",$_SERVER['HTTP_HOST']))
        exit("ahh nice try");
    if(($_SERVER['HTTP_HOST'] !== "webhacking.kr") 
        && (gethostbyname($_SERVER['HTTP_HOST']) !== "202.182.106.159")) 
        exit("Something wrong");
    sleep(1); // my server is small and weak
    $ch = curl_init();
    curl_setopt($ch, CURLOPT_URL, "http://{$_SERVER['HTTP_HOST']}:10020/cmd/".rawurlencode($cmd).".txt");
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    curl_setopt($ch, CURLOPT_CONNECTTIMEOUT, 4);
    curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, false);
    $response = curl_exec($ch);
    if(curl_getinfo($ch, CURLINFO_HTTP_CODE) !== 200){
      echo <<<HELP
only "ls", "cat api.php", "cat index.php" allowed
HELP;
    }
    else{ system($response); }
    curl_close($ch);
  }
?>
```

![cmd](/assets/images/child-toctou/cmd.png)

In short, this code checks the `Host` HTTP header, makes sure it's either "webhacking.kr" or resolved to a fixed IP address, then gets a text file from that server and run the shell command inside. What could go wrong with this code?

It's very likely that we can trick the server to read the text file from somewhere we have control of. All the `if`s make sure that you can't do that, but there is a catch:

```php
if(($_SERVER['HTTP_HOST'] !== "webhacking.kr") && (gethostbyname($_SERVER['HTTP_HOST']) !== "202.182.106.159")) exit("Something wrong");
```
This line suggests that we can use anything different than `webhacking.kr`, as long as the IP address is still `202.182.106.159` or vise versa.

Now, what if we have a DNS record that points to that IP address, so we can pass all the checks, and just before the `curl_setopt` call, we take advantage of the `sleep(1)` to change it back to our server address? BOOM! There's a vulnerability there.

## And how exactly can we do that?
Idk, will be updated.
