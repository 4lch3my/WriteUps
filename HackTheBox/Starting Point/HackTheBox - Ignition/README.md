# Ignition
> 4lch3my | June 10, 2023
-------------------
#### Starting-Point: Learn the basics of Penetration Testing
#### [Room Link](https://app.hackthebox.com/starting-point)

## Tasks
##### Task 1. Which service version is found to be running on port 80?

*Deploy the machine with the Spawn Machine button, also either deploy your [PWNBox](https://help.hackthebox.com/en/articles/5185608-introduction-to-pwnbox) OR Connect to [OpenVPN](https://help.hackthebox.com/en/articles/5185687-introduction-to-lab-access) and deploy your personal KALI machine.*

To start, I will use [nmap] which is default option on the personal Kali install I have. We start with a basic command
  `nmap -sC -sV -A MACHINE_IP`
The -sC flag runs scripts against open ports as well to determine if there are external/common vulnerabilities that we can use outright. The -sV probes all open ports it finds to determine if we can get the service/version information. -A uses agressive mode, so we can follow along with the commands and enumeration.
  In our NMAP results we get:

```
Starting Nmap 7.93 ( https://nmap.org ) at 2023-06-10 19:48 CEST
Nmap scan report for MACHINE_IP
Host is up (0.040s latency).
Not shown: 999 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
80/tcp open  http    nginx 1.14.2
|_http-server-header: nginx/1.14.2
|_http-title: Did not follow redirect to http://ignition.htb/
```

As we can see there is only one port open `80 - HTTP` running [nginx](https://www.nginx.com/) a popular website manager and load balancer.


##### Task 2 & 3. What is the 3-digit HTTP status code returned when you visit http://{machine IP}/?

We can do a simple `curl -v http://MACHINE_IP` command to see the websites content and responce, where the `-v` options "makes the operation more talkative", giving us all the information what we need for this task:

```
*   Trying MACHINE_IP...
* Connected to MACHINE_IP (MACHINE_IP) port 80 (#0)
> GET / HTTP/1.1
> Host: MACHINE_IP
> User-Agent: curl/7.88.1
> Accept: */*
>
< HTTP/1.1 302 Found
< Server: nginx/1.14.2
< Date: Sat, 10 Jun 2023 17:50:23 GMT
< Content-Type: text/html; charset=UTF-8
< Transfer-Encoding: chunked
< Connection: keep-alive
< Set-Cookie: PHPSESSID=hidden_cookie; expires=Sat, 10-Jun-2023 18:50:23 GMT; Max-Age=3600; path=/; domain=10.MACHINE_IP; HttpOnly; SameSite=Lax
< Location: http://ignition.htb/
< Pragma: no-cache
< Cache-Control: max-age=0, must-revalidate, no-cache, no-store
< Expires: Fri, 10 Jun 2022 17:50:23 GMT
...
< X-Content-Type-Options: nosniff
< X-XSS-Protection: 1; mode=block
< X-Frame-Options: SAMEORIGIN
* Connection #0 to host MACHINE_IP left intact
```
##### Task 4 & 5. What is the full path to the file on a Linux computer that holds a local list of domain name to IP address pairs?

As we now have the `MACHINE_IP` and the `domain`, we are ready to do some further enumeration. But before we do that, we need to add the `.htb` domain to our local DNS records of our OS. If you are using a local version of Linux (like a VM, Hosted, Desktop or CLI) you can simple run `echo "MACHINE_IP ignition.htb" | sudo tee -a /etc/hosts` to add it. If you are using [Linux WSL](https://www.kali.org/docs/wsl/wsl-preparations/#install-wsl-1) instead, you will need to add the domain to your host files in Windows and **NOT** in the WSL client itself what can be found at `c:\Windows\System32\Drivers\etc\hosts`. 
But, as I'm not a fan of editing my personal machines local DNS services, I'll be using my Pi-Hole setup to forward the traffic to HTB instead over [oVPN](https://openvpn.net/).

![alt text](https://github.com/4lch3my/WriteUps/blob/main/HackTheBox/HackTheBox%20-%20Ignition/images/pi_hole.png?raw=true)

Add host to PiHole or /etc/hosts

Testing admin page: http://ignition.htb/admin

