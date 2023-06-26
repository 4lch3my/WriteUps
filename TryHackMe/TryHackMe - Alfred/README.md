# Alfred
> 4lch3my | June 26, 2023
-------------------
#### Exploit Jenkins to gain an initial shell, then escalate your privileges by exploiting Windows authentication tokens.
#### [Room Link](https://tryhackme.com/room/alfred)

## Basic Enumeration 
  Deploy the machine with the Start Machine button, also either deploy your [Attack Box](https://tryhackme.com/access) OR Connect to [OpenVPN](https://tryhackme.com/access) and deploy your personal KALI machine.
<br>

Now is where the basics of recon comes into play. To start, I use [nmap] which is default option on the personal Kali install I have. We start with a basic command
  `nmap -sC -sV -A MACHINE_IP `
The -sC flag runs scripts against open ports as well to determine if there are external/common vulnerabilities that we can use outright. The -sV probes all open ports it finds to determine if we can get the service/version information. -A outputs in agressive mode, so we can follow along with the commands and enumeration.

```
```