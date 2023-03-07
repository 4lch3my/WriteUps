# Easy Peasy
> 4lch3my | March 07, 2023
-------------------
#### Practice using tools such as Nmap and GoBuster to locate a hidden directory to get initial access to a vulnerable machine. Then escalate your privileges through a vulnerable cronjob.
#### [Room Link](https://tryhackme.com/room/easypeasyctf)

## Tasks
##### 1.  Enumeration through Nmap
  Deploy the machine with the Start Machine button, also either deploy your [Attack Box](https://tryhackme.com/access) OR Connect to [OpenVPN](https://tryhackme.com/access) and deploy your personal KALI machine.
<br>

Now is where the basics of recon comes into play. To start, I use [nmap] which is default option on the personal Kali install I have. We start with a basic command
  `nmap -sC -sV -A MACHINE_IP `
The -sC flag runs scripts against open ports as well to determine if there are external/common vulnerabilities that we can use outright. The -sV probes all open ports it finds to determine if we can get the service/version information. -A outputs in agressive mode, so we can follow along with the commands and enumeration.
  In our NMAP results we get:
```
Starting Nmap 7.60 ( https://nmap.org ) at 2023-03-06 23:30 GMT
Nmap scan report for ip-MACHINE_IP.eu-west-1.compute.internal (MACHINE_IP)
Host is up (0.014s latency).
All 1000 scanned ports on ip-MACHINE_IP.eu-west-1.compute.internal (MACHINE_IP) are closed
MAC Address: 02:58:26:7D:CB:A5 (Unknown)
Too many fingerprints match this host to give specific OS details
Network Distance: 1 hop

ttps://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 4.34 seconds
```
Hmmm, it seems like this machine is making it harder for us to hunt for the flags then usual. Though this should not be an issue for what so ever. We could scan all the ports with the `-p-` flag in [nmap], but that would take way to long. Let me introduce you to: [masscan]. This is a way faster tool to get over all ports on a machine, so lets get starter. The command will be: `sudo masscan MACHINE_IP -p0-65535 --rate 250000`
```
Starting masscan 1.0.3 (http://bit.ly/14GZzcT) at 2023-03-07 00:52:53 GMT
 -- forced options: -sS -Pn -n --randomize-hosts -v --send-eth
Initiating SYN Stealth Scan
Scanning 1 hosts [65536 ports/host]
Discovered open port 80/tcp on MACHINE_IP                                    
Discovered open port 65524/tcp on MACHINE_IP                                
Discovered open port 6498/tcp on MACHINE_IP 
```
WOW! Now THAT was fast! In under 10 seconds we where able to scan the whole machine for open ports! Impressive!
<br>

OK, so we see there are 3 ports open 80, 65524 & 6498. To get further details, we can scan all of these with [nmap]: `nmap -sC -sV -A -p 80,65524,6498 MACHINE_IP` for further details:
```
Starting Nmap 7.60 ( https://nmap.org ) at 2023-03-07 01:01 GMT
Nmap scan report for ip-MACHINE_IP.eu-west-1.compute.internal (MACHINE_IP)
Host is up (0.00040s latency).

PORT      STATE SERVICE VERSION
80/tcp    open  http    nginx 1.16.1
| http-robots.txt: 1 disallowed entry 
|_/
|_http-server-header: nginx/1.16.1
|_http-title: Welcome to nginx!
6498/tcp  open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 30:4a:2b:22:ac:d9:56:09:f2:da:12:20:57:f4:6c:d4 (RSA)
|   256 bf:86:c9:c7:b7:ef:8c:8b:b9:94:ae:01:88:c0:85:4d (ECDSA)
|_  256 a1:72:ef:6c:81:29:13:ef:5a:6c:24:03:4c:fe:3d:0b (EdDSA)
65524/tcp open  http    Apache httpd 2.4.43 ((Ubuntu))
| http-robots.txt: 1 disallowed entry 
|_/
|_http-server-header: Apache/2.4.43 (Ubuntu)
|_http-title: Apache2 Debian Default Page: It works
MAC Address: 02:58:26:7D:CB:A5 (Unknown)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 3.8 (95%), Linux 3.1 (94%), Linux 3.2 (94%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (94%), ASUS RT-N56U WAP (Linux 3.4) (93%), Linux 3.16 (93%), Linux 2.6.32 (92%), Linux 2.6.39 - 3.2 (92%), Linux 3.1 - 3.2 (92%), Linux 3.2 - 4.8 (92%)
No exact OS matches for host (test conditions non-ideal).
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```
Much better! Now we can see all the service running `80 - http - nginx 1.16.1`, `6498 - SSH` & `65524 - http - Apache/2.4.43`

##### 2. Compromising the machine
Before we do anything else, let's inspect both pages to see if we find anything interesting. Starting with port `80`:

![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Easy%20Peasy/images/simple_nginx.PNG?raw=true)

Hmmm, it seems like there is nothing here. Let's look at the source code of the page:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>

```
Welp, it seems like this is a bust, nothing on the first page. Time to move on to the other one over at port `65524`:

![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Easy%20Peasy/images/simple_apache.png?raw=true)

Seems like the generic Apache 2 setup page, but with a twist. If we read over it very carefully, we will see the default text has been modified a bit. It has the 3rd flag hidden in plain sight!

![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Easy%20Peasy/images/3rd.png?raw=true)

Let's look at the source code of this page while we are at it:
```
  </style>
  </head>
  <body>
    <div class="main_page">
      <div class="page_header floating_element">
        <img src="/icons/openlogo-75.png" alt="Debian Logo" class="floating_element"/>
        <span class="floating_element">
          Apache 2 It Works For Me
	<p hidden>its encoded with ba....:Ob[REDACTED]Vu</p>
        </span>
      </div>
<!--      <div class="table_of_contents floating_element">
        <div class="section_header section_header_grey">
          TABLE OF CONTENTS
        </div>
        <div class="table_of_contents_item floating_element">
          <a href="#about">About</a>
        </div>
        <div class="table_of_contents_item floating_element">
          <a href="#flag">hi</a>
        </div>
        <div class="table_of_contents_item floating_element">
          <a href="#scope">Scope</a>
        </div>
        <div class="table_of_contents_item floating_element">
          <a href="#files">Config files</a>
        </div>
      </div>
-->
      <div class="content_section floating_element">
```
Would you look at that! We have found `Ob[REDACTED]Vu` what is a string that seems to be encoded. Time to decode. One of my favourite tools is [dcode's cipher identifier](https://www.dcode.fr/cipher-identifier) for this kind of task. After adding the string, we can see most likely it is Base62 encoded.

![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Easy%20Peasy/images/base62.png?raw=true)

And wow, we actually get a new hidden directory:

![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Easy%20Peasy/images/nothing.png?raw=true)

On this page, we only see an image, lets look at the source-code again:
```
<body>
<center>
<img src="binarycodepixabay.jpg" width="140px" height="140px"/>
<p>940d71e8655ac41efb5f8ab850668[REDACTED]66e57d1483e7f5fe6fd81</p>
</center>
</body>
```
Looking at the encoded text and the hint provided by TryHackMe, it seems this is a GOST MD5 hash. The first google for this sends us to `md5hashing.net` where we can enter the hash.
![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Easy%20Peasy/images/gost.png?raw=true)

Success!
<br>

We know from the `hint` provided by TryHackMe that this might be a steganography challange. So lets dowload the linked image `binarycodepixabay.jpg` and use a new tool to decode it. We are using [steghide] to see what the image is really hiding: `steghide extract -sf image_name.jpg`
![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Easy%20Peasy/images/steg1.png?raw=true)
When propted for the passphrase, paste the one what we just decoded above. This should generate a `secrettext.txt` file
![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Easy%20Peasy/images/steg2.png?raw=true)
The file contains the following:
![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Easy%20Peasy/images/secrettext.png?raw=true)

A quick binary to text search on [CyberChef] will reveal the password for the user `boring`:

![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Easy%20Peasy/images/password_ssh.png?raw=true)
<br>

Time to fire up good ol' [GoBuster] to look for some more hidden directorys. We will run 2 scans for the 2 http ports, you never know what can be hiding where in CTF's... 
iconvertedmypasswordtobinary

`gobuster dir -u http://MACHINE_IP/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt`

```
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://MACHINE_IP/
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Timeout:        10s
===============================================================
2023/03/07 01:47:54 Starting gobuster
===============================================================
/hidden (Status: 301)
===============================================================
2023/03/07 01:48:19 Finished
===============================================================
```

```
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://MACHINE_IP/
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Timeout:        10s
===============================================================
2023/03/07 01:47:54 Starting gobuster
===============================================================
/hidden/whatever (Status: 301)
===============================================================
2023/03/07 01:48:19 Finished
===============================================================
```
AND 
```
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://MACHINE_IP:65524/
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Timeout:        10s
===============================================================
2023/03/07 01:48:16 Starting gobuster
===============================================================
/server-status (Status: 403)
===============================================================
2023/03/07 01:48:48 Finished
===============================================================
```
It seems like we have 2 directorys found by gobuster `/hidden` and `/hidden/whatever`. Let's look at them.
<br>

`/hidden` has nothing interesting only an image and the source code does not reveal anything intersting either. Moving on to `/hidden/whatever`.
<br>

Again, we only see an image on the page, but when looking at the source code, we see a most likely base64 encoded string:
```
</head>
<body>
<center>
<p hidden>ZmxhZ3[REDACTED]9mbDRnfQ==</p>
</center>
</body>
</html>
```
After decoding in [CyberChef] using base64, we get the 1st flag:

![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Easy%20Peasy/images/1st.png?raw=true)
