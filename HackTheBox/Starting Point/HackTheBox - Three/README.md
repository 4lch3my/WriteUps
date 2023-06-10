# Three
> 4lch3my | June 11, 2023
-------------------
#### Starting-Point: Learn the basics of Penetration Testing
#### [Room Link](https://app.hackthebox.com/starting-point)

## Tasks
##### Task 1. How many TCP ports are open?

*Deploy the machine with the Spawn Machine button, also either deploy your [PWNBox](https://help.hackthebox.com/en/articles/5185608-introduction-to-pwnbox) OR Connect to [OpenVPN](https://help.hackthebox.com/en/articles/5185687-introduction-to-lab-access) and deploy your personal KALI machine.*

To start, I will use [nmap] which is default option on the personal Kali install I have. We start with a basic command
  `nmap -sC -sV -A MACHINE_IP`
The -sC flag runs scripts against open ports as well to determine if there are external/common vulnerabilities that we can use outright. The -sV probes all open ports it finds to determine if we can get the service/version information. -A uses agressive mode, so we can follow along with the commands and enumeration.
  In our NMAP results we get:

```
Starting Nmap 7.93 ( https://nmap.org ) at 2023-06-10 21:45 CEST 
Nmap scan report for thetoppers.htb (10.129.125.146)
Host is up (0.040s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.7 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 178bd425452a20b879f8e258d78e79f4 (RSA)
|   256 e60f1af6328a40ef2da73b22d1c714fa (ECDSA)
|_  256 2de1874175f391544116b72b80c68f05 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: The Toppers
|_http-server-header: Apache/2.4.29 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 10.92 seconds
```
