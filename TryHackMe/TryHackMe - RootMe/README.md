This is where the basics of recon come into play. To start, I use [nmap] which is default option on the perosnal Kali install I have. We start with a basic command
  ` nmap -sC -sV -A -v MACHINE_IP `
```
Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-06 18:34 CET
NSE: Loaded 155 scripts for scanning.
NSE: Script Pre-scanning.
Initiating NSE at 18:34
Completed NSE at 18:34, 0.00s elapsed
Initiating NSE at 18:34
Completed NSE at 18:34, 0.00s elapsed
Initiating NSE at 18:34
Completed NSE at 18:34, 0.00s elapsed
Initiating Ping Scan at 18:34
Scanning 10.10.13.131 [2 ports]
Completed Ping Scan at 18:34, 0.06s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 18:34
Completed Parallel DNS resolution of 1 host. at 18:34, 1.03s elapsed
Initiating Connect Scan at 18:34
Scanning 10.10.13.131 [1000 ports]
Discovered open port 22/tcp on 10.10.13.131
Completed Connect Scan at 18:34, 2.88s elapsed (1000 total ports)
Initiating Service scan at 18:34
Scanning 1 service on 10.10.13.131
Completed Service scan at 18:34, 0.59s elapsed (1 service on 1 host)
NSE: Script scanning 10.10.13.131.
Initiating NSE at 18:34
Completed NSE at 18:34, 3.97s elapsed
Initiating NSE at 18:34
Completed NSE at 18:34, 0.00s elapsed
Initiating NSE at 18:34
Completed NSE at 18:34, 0.00s elapsed
Nmap scan report for 10.10.13.131
Host is up (0.078s latency).
Not shown: 999 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 4ab9160884c25448ba5cfd3f225f2214 (RSA)
|   256 a9a686e8ec96c3f003cd16d54973d082 (ECDSA)
|_  256 22f6b5a654d9787c26035a95f3f9dfcd (ED25519)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

NSE: Script Post-scanning.
Initiating NSE at 18:34
Completed NSE at 18:34, 0.00s elapsed
Initiating NSE at 18:34
Completed NSE at 18:34, 0.00s elapsed
Initiating NSE at 18:34
Completed NSE at 18:34, 0.00s elapsed
Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.82 seconds
  ```