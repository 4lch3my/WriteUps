# UltraTech
> 4lch3my | October 23, 2023
-------------------
#### The basics of Penetration Testing, Enumeration, Privilege Escalation and WebApp testing
#### [Room Link](https://tryhackme.com/room/ultratech1)

## Tasks
##### 1. Deploy the machine

Deploy the machine with the Start Machine button, also either deploy your [Attack Box](https://tryhackme.com/access) OR Connect to [OpenVPN](https://tryhackme.com/access) and deploy your personal KALI machine.
<br>

##### 2. It's enumeration time!

To start, I use [nmap] which is default option on the personal Kali install I have. We start with a basic command `nmap -sC -sV -A MACHINE_IP -p- `.
<br>

The `-sC` flag runs scripts against open ports as well to determine if there are external/common vulnerabilities that we can use outright. The `-sV` probes all open ports it finds to determine if we can get the service/version information. `-A `outputs in aggressive mode, so we can follow along with the commands and enumeration. The `-p-` flag is set to scan all ports instead of just the default ones.
In our NMAP results we get:

```
Starting Nmap 7.94 ( https://nmap.org ) at 2023-10-23 17:34 CEST
Nmap scan report for MACHINE_IP
Host is up (0.050s latency).
Not shown: 997 closed tcp ports (conn-refused)
PORT     STATE SERVICE VERSION
21/tcp   open  ftp     vsftpd 3.0.3
22/tcp   open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 dc:66:89:85:e7:05:c2:a5:da:7f:01:20:3a:13:fc:27 (RSA)
|   256 c3:67:dd:26:fa:0c:56:92:f3:5b:a0:b3:8d:6d:20:ab (ECDSA)
|_  256 11:9b:5a:d6:ff:2f:e4:49:d2:b5:17:36:0e:2f:1d:2f (ED25519)
8081/tcp open  http    Node.js Express framework
|_http-title: Site doesn't have a title (text/html; charset=utf-8).
|_http-cors: HEAD GET POST PUT DELETE PATCH
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
31331/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: UltraTech - The best of technology (AI, FinTech, Big Data)

Nmap done: 1 IP address (1 host up) scanned in 12.45 seconds
```

We see 4 open ports: `21 - FTP`, `22 - SSH`, `8081 - HTTP` & `31331 - HTTP`.

##### 3. Let the fun begin

Before we visit the webpages let's run a quick and dirty `GoBuster` scan with: `gobuster dir -u http://MACHINE_IP/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt` on port 80:

