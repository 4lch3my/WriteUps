# Anonforce
> 4lch3my | January 13, 2024
-------------------
#### boot2root machine for FIT and bsides guatemala CTF
#### [Room Link](https://tryhackme.com/room/bsidesgtanonforce)

## Tasks

##### 1.  Anonforce Machine
  To start, I use [nmap] which is default option on the personal Kali install I have to scan the machine. We start with a basic command:  ` nmap -sC -sV -A -v MACHINE_IP `
  The `-sC` flag runs scripts against open ports as well to determine if there are external/common vulnerabilities that we can use outright. The `-sV` probes all open ports it finds to determine if we can get the service/version information. `-v` outputs in verbose mode, so we can follow along with the commands and enumeration.
  In our NMAP results we get:

  ```

  Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-01-13 18:18 CET
Nmap scan report for 10.10.29.16
Host is up (0.051s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
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
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| drwxr-xr-x    2 0        0            4096 Aug 11  2019 bin
...
| dr-xr-xr-x   13 0        0               0 Jan 13 08:20 sys
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 8a:f9:48:3e:11:a1:aa:fc:b7:86:71:d0:2a:f6:24:e7 (RSA)
|   256 73:5d:de:9a:88:6e:64:7a:e1:87:ec:65:ae:11:93:e3 (ECDSA)
|_  256 56:f9:9f:24:f1:52:fc:16:b7:7b:a3:e2:4f:17:b4:ea (ED25519)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

```

We can see that an FTP servce running on port 21 is accessibble with Anonymous FTP login, meaning no password is required. Let's log in to see what we can see.

```

┌──(kali㉿kali)-[~]
└─$ ftp 10.10.29.16
Connected to 10.10.29.16.
220 (vsFTPd 3.0.3)
Name (10.10.29.16:elliot): Anonymous
331 Please specify the password.
Password:
230 Login successful.

```

Time to list the files:

```

ftp> ls
229 Entering Extended Passive Mode (|||49031|)
150 Here comes the directory listing.
drwxr-xr-x    2 0        0            4096 Aug 11  2019 bin
drwxr-xr-x    3 0        0            4096 Aug 11  2019 boot
drwxr-xr-x   17 0        0            3700 Jan 13 08:20 dev
drwxr-xr-x   85 0        0            4096 Aug 13  2019 etc
drwxr-xr-x    3 0        0            4096 Aug 11  2019 home
lrwxrwxrwx    1 0        0              33 Aug 11  2019 initrd.img -> boot/initrd.img-4.4.0-157-generic
lrwxrwxrwx    1 0        0              33 Aug 11  2019 initrd.img.old -> boot/initrd.img-4.4.0-142-generic
drwxr-xr-x   19 0        0            4096 Aug 11  2019 lib
drwxr-xr-x    2 0        0            4096 Aug 11  2019 lib64
drwx------    2 0        0           16384 Aug 11  2019 lost+found
drwxr-xr-x    4 0        0            4096 Aug 11  2019 media
drwxr-xr-x    2 0        0            4096 Feb 26  2019 mnt
drwxrwxrwx    2 1000     1000         4096 Aug 11  2019 notread
drwxr-xr-x    2 0        0            4096 Aug 11  2019 opt
dr-xr-xr-x   96 0        0               0 Jan 13 08:20 proc
drwx------    4 0        0            4096 Jan 13 09:18 root
drwxr-xr-x   18 0        0             560 Jan 13 09:17 run
drwxr-xr-x    2 0        0           12288 Aug 11  2019 sbin
drwxr-xr-x    3 0        0            4096 Aug 11  2019 srv
dr-xr-xr-x   13 0        0               0 Jan 13 08:20 sys
drwxrwxrwt    9 0        0            4096 Jan 13 09:17 tmp
drwxr-xr-x   10 0        0            4096 Aug 11  2019 usr
drwxr-xr-x   11 0        0            4096 Aug 11  2019 var
lrwxrwxrwx    1 0        0              30 Aug 11  2019 vmlinuz -> boot/vmlinuz-4.4.0-157-generic
lrwxrwxrwx    1 0        0              30 Aug 11  2019 vmlinuz.old -> boot/vmlinuz-4.4.0-142-generic
226 Directory send OK.

```

