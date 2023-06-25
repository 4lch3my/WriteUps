# Chocolate Factory
> 4lch3my | June 25, 2023
-------------------
#### A Charlie And The Chocolate Factory themed room, revisit Willy Wonka's chocolate factory!
#### [Room Link](https://tryhackme.com/room/chocolatefactory)

## Enumeration 
  Deploy the machine with the Start Machine button, also either deploy your [Attack Box](https://tryhackme.com/access) OR Connect to [OpenVPN](https://tryhackme.com/access) and deploy your personal KALI machine.
<br>

Now is where the basics of recon comes into play. To start, I use [nmap] which is default option on the personal Kali install I have. We start with a basic command
  `nmap -sC -sV -A MACHINE_IP `
The -sC flag runs scripts against open ports as well to determine if there are external/common vulnerabilities that we can use outright. The -sV probes all open ports it finds to determine if we can get the service/version information. -A outputs in agressive mode, so we can follow along with the commands and enumeration.
The command was suspiciously taking a lot of time on my machine, so I did a quick [masscan](https://www.kalilinux.in/2020/09/masscan-1000-times-faster-than-nmap.html#:~:text=Masscan%20is%20the%20fastest%20network,per%20second%20data%20transmitting%20speed.) in the background just to verify that it was actually working. I used the following command: `sudo masscan MACHINE_IP -p0-65535 --rate 2500` This found 29 open ports, so I knew I would need to wait a little longer for the nmap results, but after a few minutes I received this:

![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Chocolate%20Factory/images/nmap_1.png?raw=true)

Wow that is a lot of ports open. Time to dig.

## FTP

First port on the list is 21 - FTP, as listed in the `ftp-anon` section: `Anonymous FTP login allowed` what means we do, not need a password. Let's see what we can find:

```
ftp MACHINE_IP
Connected to MACHINE_IP.
220 (vsFTPd 3.0.3)
Name (MACHINE_IP:username): Anonymous
331 Please specify the password.
Password: 
230 Login successful.
```

List visible files:

```
ftp> ls
150 Here comes the directory listing.
-rw-rw-r--    1 1000     1000       208838 Sep 30  2020 gum_room.jpg
226 Directory send OK.
```

Copy files to personal instance:

```
ftp> get gum_room.jpg
100% |*************************************************|   203 KiB  870.66 KiB/s    00:00 ETA
226 Transfer complete.
208838 bytes received in 00:00 (695.76 KiB/s)
ftp>
ftp> quit
221 Goodbye.
```
![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Chocolate%20Factory/images/gumroad.png?raw=true)

Taking a look at this image we do not see anything interesting, but some data might be hidden inside, so let's get `steghide` fired up: `steghide extract -sf gum_room.jpg`. As we don't have a passphrase, we can hit Enter and hope it was not encrypted.

![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Chocolate%20Factory/images/steghide.png?raw=true)

We can now view what the `.txt` file contains:

![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Chocolate%20Factory/images/b64.png?raw=true)


We can decode this with [CyberChef](https://gchq.github.io/CyberChef/) and we get:

![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Chocolate%20Factory/images/password.png?raw=true)

Time to crack it! After a quick Google we can see the [HashType](https://hashcat.net/wiki/doku.php?id=example_hashes) is most likely `sha512crypt`. It's time for hashcat!
Grab your has, add it to a `.txt` file and run the following command on it: `hashcat -a 0 -m 1800 password.txt /usr/share/wordlists/rockyou.txt`.
The overall purpose of this command is to attempt to crack the hashed passwords contained in the `password.tx`t file using the `dictionary attack mode (-a 0)` with the `SHA-512(Unix) hash type (-m 1800)`. The tool will try passwords from the `rockyou.txt` wordlist file and compare them against the hashed passwords to find matches.

![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Chocolate%20Factory/images/cracked.png?raw=true)

And there we have charlies password.

## SSH

As we saw in our [nmap] scan, we have SSH enabled. After testing with the above credenrtials, it does not seem we are able to log in to SSH. Moving forward

## HTTP

On `port 80` we have a webserver running. Before we visit it, let us run a good old [GoBuster] website scan for login pages:
`gobuster dir -u http://10.10.238.240/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x txt,html,php `

```
===============================================================
Gobuster v3.5
===============================================================
[+] Url:                     MACHINE_IP
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.5
[+] Extensions:              txt,html,php
[+] Timeout:                 10s
===============================================================
2023/06/25 21:29:58 Starting gobuster in directory enumeration mode
===============================================================
/.php                 (Status: 403) [Size: 278]
/index.html           (Status: 200) [Size: 1466]
/.html                (Status: 403) [Size: 278]
/home.php             (Status: 200) [Size: 569]
===============================================================
2023/06/25 21:33:38 Finished
===============================================================
```

Log in

What to add?


113/tcp open  ident?
| fingerprint-strings:
|   GetRequest, NotesRPC:
|_    http://localhost/key_rev_key <- You will find the key here!!!


Enter your name:  laksdhfas

![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Chocolate%20Factory/images/new_password.png?raw=true)