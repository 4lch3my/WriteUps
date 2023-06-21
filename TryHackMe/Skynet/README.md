nmap -sC -sV -A


Starting Nmap 7.60 ( https://nmap.org ) at 2023-06-21 01:26 BST
Not shown: 994 closed ports
PORT    STATE SERVICE     VERSION
22/tcp  open  ssh         OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 99:23:31:bb:b1:e9:43:b7:56:94:4c:b9:e8:21:46:c5 (RSA)
|   256 57:c0:75:02:71:2d:19:31:83:db:e4:fe:67:96:68:cf (ECDSA)
|_  256 46:fa:4e:fc:10:a5:4f:57:57:d0:6d:54:f6:c3:4d:fe (EdDSA)
80/tcp  open  http        Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Skynet
110/tcp open  pop3        Dovecot pop3d
|_pop3-capabilities: SASL CAPA AUTH-RESP-CODE UIDL RESP-CODES TOP PIPELINING
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
143/tcp open  imap        Dovecot imapd
|_imap-capabilities: LITERAL+ OK Pre-login post-login more LOGIN-REFERRALS capabilities ENABLE have listed IMAP4rev1 IDLE SASL-IR LOGINDISABLEDA0001 ID
445/tcp open  netbios-ssn Samba smbd 4.3.11-Ubuntu (workgroup: WORKGROUP)
MAC Address: 02:91:B8:12:4E:D3 (Unknown)
Device type: general purpose
Running: Linux 3.X
OS CPE: cpe:/o:linux:linux_kernel:3.13
OS details: Linux 3.13
Network Distance: 1 hop
Service Info: Host: SKYNET; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_nbstat: NetBIOS name: SKYNET, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.3.11-Ubuntu)
|   Computer name: skynet
|   NetBIOS computer name: SKYNET\x00
|   Domain name: \x00
|   FQDN: skynet
|_  System time: 2023-06-20T19:26:56-05:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2023-06-21 01:26:56
|_  start_date: 1600-12-31 23:58:45

Nmap done: 1 IP address (1 host up) scanned in 16.65 seconds





port 80 -  website - add image
then enumerate:

gobuster dir -u http://10.10.2.173/ -w /usr/share/wordlists/dirb/big.txt -x php,html,txt

===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://10.10.213.240/
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirb/big.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Extensions:     php,html,txt
[+] Timeout:        10s
===============================================================
2023/06/21 02:30:12 Starting gobuster
===============================================================
/.htpasswd (Status: 403)
/.htpasswd.php (Status: 403)
/.htpasswd.html (Status: 403)
/.htpasswd.txt (Status: 403)
/.htaccess (Status: 403)
/.htaccess.php (Status: 403)
/.htaccess.html (Status: 403)
/.htaccess.txt (Status: 403)
/admin (Status: 301)
/ai (Status: 301)
/config (Status: 301)
/css (Status: 301)
/index.html (Status: 200)
/js (Status: 301)
/server-status (Status: 403)
/squirrelmail (Status: 301)
===============================================================
2023/06/21 02:30:18 Finished
===============================================================

Let's check index.html and /squirrelmail

index.html image

/squirrelmail image >> time to look for some login credentials

Time for port 445 Samba:


Map samba: smbmap -H 10.10.213.240 (if not installed: pip install smbmap )



    ________  ___      ___  _______   ___      ___       __         _______
   /"       )|"  \    /"  ||   _  "\ |"  \    /"  |     /""\       |   __ "\
  (:   \___/  \   \  //   |(. |_)  :) \   \  //   |    /    \      (. |__) :)
   \___  \    /\  \/.    ||:     \/   /\   \/.    |   /' /\  \     |:  ____/
    __/  \   |: \.        |(|  _  \  |: \.        |  //  __'  \    (|  /
   /" \   :) |.  \    /:  ||: |_)  :)|.  \    /:  | /   /  \   \  /|__/ \
  (_______/  |___|\__/|___|(_______/ |___|\__/|___|(___/    \___)(_______)
 -----------------------------------------------------------------------------
     SMBMap - Samba Share Enumerator | Shawn Evans - ShawnDEvans@gmail.com
                     https://github.com/ShawnDEvans/smbmap

                                                                                                    
[+] IP: 10.10.213.240:445	Name: ip-10-10-213-240.eu-west-1.compute.internal	Status: Guest session   	
        Disk                                                  	Permissions	Comment
	----                                                  	-----------	-------
	print$                                            	NO ACCESS	Printer Drivers
	anonymous                                         	READ ONLY	Skynet Anonymous Share
	milesdyson                                        	NO ACCESS	Miles Dyson Personal Share
	IPC$                                              	NO ACCESS	IPC Service (skynet server (Samba, Ubuntu))

Time to read anonymous share

connect to SMB: smbclient //10.10.213.240/anonymous -U

smb: \> ls
  .                                   D        0  Thu Nov 26 16:04:00 2020
  ..                                  D        0  Tue Sep 17 08:20:17 2019
  attention.txt                       N      163  Wed Sep 18 04:04:59 2019
  logs                                D        0  Wed Sep 18 05:42:16 2019

		9204224 blocks of size 1024. 5806728 blocks available
smb: \> 


download file: get attention.txt
Lets look at logs

smb: \> cd logs\
smb: \logs\> ls
  .                                   D        0  Wed Sep 18 05:42:16 2019
  ..                                  D        0  Thu Nov 26 16:04:00 2020
  log2.txt                            N        0  Wed Sep 18 05:42:13 2019
  log1.txt                            N      471  Wed Sep 18 05:41:59 2019
  log3.txt                            N        0  Wed Sep 18 05:42:16 2019

		9204224 blocks of size 1024. 5806728 blocks available
smb: \logs\> 


download files: log1.txt log2.txt log3.txt

Image of log


Time to login to  web/email:

Login to email with username: milesdyson
Password:cyborg007haloterminator

IMAGE of webmail

Look at emails: IMAGE of emails

IMAGE of main email

Log in to SMB with password:

smbclient //10.10.213.240/milesdyson -U milesdyson

smb: \> ls
  .                                   D        0  Tue Sep 17 10:05:47 2019
  ..                                  D        0  Wed Sep 18 04:51:03 2019
  Improving Deep Neural Networks.pdf      N  5743095  Tue Sep 17 10:05:14 2019
  Natural Language Processing-Building Sequence Models.pdf      N 12927230  Tue Sep 17 10:05:14 2019
  Convolutional Neural Networks-CNN.pdf      N 19655446  Tue Sep 17 10:05:14 2019
  notes                               D        0  Tue Sep 17 10:18:40 2019
  Neural Networks and Deep Learning.pdf      N  4304586  Tue Sep 17 10:05:14 2019
  Structuring your Machine Learning Project.pdf      N  3531427  Tue Sep 17 10:05:14 2019

		9204224 blocks of size 1024. 5806728 blocks available
smb: \> 


list notes

smb: \> cd notes\
smb: \notes\> ls
  .                                   D        0  Tue Sep 17 10:18:40 2019
  ..                                  D        0  Tue Sep 17 10:05:47 2019
  3.01 Search.md                      N    65601  Tue Sep 17 10:01:29 2019
  4.01 Agent-Based Models.md          N     5683  Tue Sep 17 10:01:29 2019
  2.08 In Practice.md                 N     7949  Tue Sep 17 10:01:29 2019
  0.00 Cover.md                       N     3114  Tue Sep 17 10:01:29 2019
  1.02 Linear Algebra.md              N    70314  Tue Sep 17 10:01:29 2019
  important.txt                       N      117  Tue Sep 17 10:18:39 2019
  6.01 pandas.md                      N     9221  Tue Sep 17 10:01:29 2019
...
1.00 Foundations.md                 N       22  Tue Sep 17 10:01:29 2019

get file importatnt.txt >> we have the hidden directory/page

Run gobuster again:
gobuster dir -u http://10.10.213.240/45kra24zxs28v3yd/ -w /usr/share/wordlists/dirb/big.txt

===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://10.10.213.240/45kra24zxs28v3yd/
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirb/big.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Timeout:        10s
===============================================================
2023/06/21 02:39:19 Starting gobuster
===============================================================
/.htpasswd (Status: 403)
/.htaccess (Status: 403)
/administrator (Status: 301)
===============================================================
2023/06/21 02:39:20 Finished
===============================================================

Time to visit and look at webpage

Cuppa CMS >> look for vuln: https://www.exploit-db.com/exploits/25971
IMAGE