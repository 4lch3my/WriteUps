# Main command (with flag) to use for GoBuster: gobuster dir -u http://10.10.10.10 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
===============--GOBUSTER--===============
///Task 6///
Q: Run a directory scan on the host. Other than the standard css, images and js directories, what other directories are available?
** gobuster dir -u http://10.10.10.10 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
A: public,Changes,VIDEO
===
Q: Run a directory scan on the host. In the "C******" directory, what file extensions exist?
** gobuster dir -u http://webenum.thm/Changes -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -x xhtml,html,pl,css,htm,js,php,rss,jsp,part,py,conf
A: conf,js
===
Q: There are some virtual hosts running on this server. What are they?
** gobuster vhost -u http://webenum.thm -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt
A:learning,products
===
Q: There's another flag to be found in one of the virtual hosts! Find it!
** gobuster dir -u http://learning.webenum.thm/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -x php,txt
---
** gobuster dir -u http://products.webenum.thm/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -x php,txt
---
Navigated to http://products.webenum.thm/flag.txt
A: thm{gobuster_is_fun}
===============--WPSCAN--===============
///Task8///
Q: What would be the full URL for the theme "twentynineteen" installed on the WordPress site: "http://cmnatics.playground"
A: http://cmnatics.playground/wp-content/themes/twentynineteen
===
Q: What argument would we provide to enumerate a WordPress site?
A: enumerate
===
Q: What is the name of the other aggressiveness profile that we can use in our WPScan command?
A: passive
///Task9///
Q: Enumerate the site, what is the name of the theme that is detected as running?
** wpscan --url http://wpscan.thm --enumerate t
A: twentynineteen
===
Q: WPScan says that this theme is out of date, what does it suggest is the number of the latest version?
A: 2.3
===
Q: Enumerate the site, what is the name of the plugin that WPScan has found?
** wpscan --url http://wpscan.thm --enumerate p
A: nextgen-gallery
===
Q: Enumerate the site, what username can WPScan find?
** wpscan --url http://wpscan.thm --enumerate u
A: phreakazoid
===
Q:  What is the password to this user? 
** wpscan --url http://wpscan.thm --passwords /usr/share/wordlists/rockyou.txt  --usernames phreakazoid
A: linkinpark
===============--NIKTO--===============
nikto -h vuln_ip -p 80,8000,other_ports
///Task11///
Q: What argument would we use if we wanted to scan port 80 and 8080 on a host?
A: -p 80,8080
===
Q: What argument would we use if we wanted to see any cookies given by the web server? 
A: -Display 5
///Task12///
Q: What is the name & version of the web server that  Nikto has determined running on port 80?
** nikto -h 10.10.56.123 -p 80
A: Apache/2.4.7
===
Q: There is another web server running on another port. What is the name & version of this web server?
** nmap -sC -sV 10.10.56.123 >> 8080
** nikto -h 10.10.56.123 -p 8080
A: Apache-Coyote/1.1
===
Q: What is the name of the Cookie that this JBoss server gives?
** nikto -h 10.10.56.123 -p 8080 -Display 5
A: JSESSIONID








