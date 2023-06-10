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

##### Task 4. What is the full path to the file on a Linux computer that holds a local list of domain name to IP address pairs?

As we now have the `MACHINE_IP` and the `domain`, we are ready to do some further enumeration. But before we do that, we need to add the `.htb` domain to our local DNS records of our OS. If you are using a local version of Linux (like a VM, Hosted, Desktop or CLI) you can simple run `echo "MACHINE_IP ignition.htb" | sudo tee -a /etc/hosts` to add it. If you are using [Linux WSL](https://www.kali.org/docs/wsl/wsl-preparations/#install-wsl-1) instead, you will need to add the domain to your host files in Windows and **NOT** in the WSL client itself what can be found at `c:\Windows\System32\Drivers\etc\hosts`. 
But, as I'm not a fan of editing my personal machines local DNS services, I'll be using my Pi-Hole setup to forward the traffic to HTB instead over [oVPN](https://openvpn.net/).

This can be done in the following way:

1, Log in to your PiHole and go to `Local DNS`
2, Select `DNS records`
3, Add the `Domain` you want to add
4, Add the `MACHINE_IP` you want to add and click `ADD`
5, You will see your `location DNS` updated.

![alt text](https://github.com/4lch3my/WriteUps/blob/main/HackTheBox/Starting%20Point/HackTheBox%20-%20Ignition/images/pi_hole.png?raw=true)

To test this you can try to `ping` your domain with `ping domain_name.htb` and if you get a ping back, you are all set.

![alt text](https://github.com/4lch3my/WriteUps/blob/main/HackTheBox/Starting%20Point/HackTheBox%20-%20Ignition/images/home_page.png?raw=true)

##### Task 5. Use a tool to brute force directories on the webserver. What is the full URL to the Magento login page?

Time to get out the good old [GoBuster]() with: `gobuster dir --url http://ignition.htb/ --wordlist /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt`:

```
===============================================================
Gobuster v3.5
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://ignition.htb/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.5
[+] Timeout:                 10s
===============================================================
2023/06/10 20:58:36 Starting gobuster in directory enumeration mode
===============================================================
/contact              (Status: 200) [Size: 28673]
/home                 (Status: 200) [Size: 25802]
/media                (Status: 301) [Size: 185] [--> http://ignition.htb/media/]
/0                    (Status: 200) [Size: 25803]
/catalog              (Status: 302) [Size: 0] [--> http://ignition.htb/]
/static               (Status: 301) [Size: 185] [--> http://ignition.htb/static/]
/admin                (Status: 200) [Size: 7095]
/Home                 (Status: 301) [Size: 0] [--> http://ignition.htb/home]
Progress: 300 / 220561 (0.14%)^C
[!] Keyboard interrupt detected, terminating.

===============================================================
2023/06/10 20:59:10 Finished
===============================================================
```

That was easy, we already have our `Magento login page`!

##### Task 5. Use a tool to brute force directories on the webserver. What is the full URL to the Magento login page?

Time to visit the login page for the site:

![alt text](https://github.com/4lch3my/WriteUps/blob/main/HackTheBox/Starting%20Point/HackTheBox%20-%20Ignition/images/admin_page.png?raw=true)

As requested by the task, we need to look for the most popular passwords for this page in 2023. As suspected this was mainly the most common passwords anyway:

```
123456
123456789
qwerty
password
12345
qwerty123
1q2w3e
12345678
111111
1234567890
```
Time to log-in with one of these to find our flag:

![alt text](https://github.com/4lch3my/WriteUps/blob/main/HackTheBox/Starting%20Point/HackTheBox%20-%20Ignition/images/flag.png?raw=true)


#### Thank you for checking out my write-up about the HTB room: Ignition! If you are interested in other write-ups of mine or interetsed in some of my codeing work, please feel free to look around my GitHub page! Happy hacking! - 4lch3my