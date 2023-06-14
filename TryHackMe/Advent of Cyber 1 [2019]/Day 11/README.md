# Advent of Cyber  [2019] - Day 11
> 4lch3my | June 14, 2023
-------------------
#### Elf Applications
#### [Room Link](https://tryhackme.com/room/25daysofchristmas)

##### Deploy the machine and connect to our network
  Deploy the machine with the Start Machine button, also either deploy your Attack Box OR Connect to OpenVPN and deploy your personal KALI machine. <br>

  To start, I use [nmap] for scanning the machine, which is default option on the personal Kali install I have. We start with a basic command
  ` nmap -sC -sV -A -v MACHINE_IP `
  The -sC flag runs scripts against open ports as well to determine if there are external/common vulnerabilities that we can use outright. The -sV probes all open ports it finds to determine if we can get the service/version information. -v outputs in verbose mode, so we can follow along with the commands and enumeration.
  In our NMAP results we get:
```
Starting Nmap 7.60 ( https://nmap.org ) at 2023-06-13 01:23 BST
Nmap scan report for ip-10-10-35-99.eu-west-1.compute.internal (10.10.35.99)
Host is up (0.00038s latency).
Not shown: 995 closed ports
PORT     STATE SERVICE VERSION
21/tcp   open  ftp     vsftpd 3.0.2
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_Can't get directory listing: PASV failed: 500 OOPS: invalid pasv_address
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to MACHINE_IP
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 5
|      vsFTPd 3.0.2 - secure, fast, stable
|_End of status
22/tcp   open  ssh     OpenSSH 7.4 (protocol 2.0)
| ssh-hostkey: 
|   2048 14:6f:fc:4d:82:43:eb:e9:6e:f3:0e:01:38:f0:cb:23 (RSA)
|   256 83:33:03:d0:b4:1d:cb:8e:59:6f:13:14:c5:a2:75:b3 (ECDSA)
|_  256 ec:b1:63:c0:6d:98:fd:be:76:31:cd:b9:78:35:2a:bf (EdDSA)
111/tcp  open  rpcbind 2-4 (RPC #100000)
| rpcinfo: 
|   program version   port/proto  service
|   100000  2,3,4        111/tcp  rpcbind
|   100000  2,3,4        111/udp  rpcbind
|   100003  3           2049/udp  nfs
|   100003  3,4         2049/tcp  nfs
|   100005  1,2,3      20048/tcp  mountd
|   100005  1,2,3      20048/udp  mountd
|   100021  1,3,4      33686/udp  nlockmgr
|   100021  1,3,4      39359/tcp  nlockmgr
|   100024  1          43852/udp  status
|   100024  1          50703/tcp  status
|   100227  3           2049/tcp  nfs_acl
|_  100227  3           2049/udp  nfs_acl
2049/tcp open  nfs_acl 3 (RPC #100227)
3306/tcp open  mysql   MySQL 5.7.28
| mysql-info: 
|   Protocol: 10
|   Version: 5.7.28
|   Thread ID: 15
|   Capabilities flags: 65535
|   Some Capabilities: IgnoreSpaceBeforeParenthesis, InteractiveClient, SupportsLoadDataLocal, SupportsTransactions, Support41Auth, FoundRows, SwitchToSSLAfterHandshake, DontAllowDatabaseTableColumn, Speaks41ProtocolNew, IgnoreSigpipes, SupportsCompression, Speaks41ProtocolOld, LongPassword, ODBCClient, LongColumnFlag, ConnectWithDatabase, SupportsAuthPlugins, SupportsMultipleStatments, SupportsMultipleResults
|   Status: Autocommit
|   Salt: \x7F|;amt\x1DA?vPb)yY\x186\x18zH
|_  Auth Plugin Name: 79
MAC Address: 02:90:84:59:C5:9B (Unknown)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.60%E=4%D=6/13%OT=21%CT=1%CU=39228%PV=Y%DS=1%DC=D%G=Y%M=029084%T
OS:M=6487B726%P=x86_64-pc-linux-gnu)SEQ(SP=108%GCD=1%ISR=10B%TI=Z%CI=Z%TS=A
OS:)SEQ(SP=108%GCD=1%ISR=10B%TI=Z%CI=Z%II=I%TS=A)OPS(O1=M2301ST11NW7%O2=M23
OS:01ST11NW7%O3=M2301NNT11NW7%O4=M2301ST11NW7%O5=M2301ST11NW7%O6=M2301ST11)
OS:WIN(W1=68DF%W2=68DF%W3=68DF%W4=68DF%W5=68DF%W6=68DF)ECN(R=Y%DF=Y%T=FF%W=
OS:6903%O=M2301NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=FF%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N
OS:)T3(R=N)T4(R=Y%DF=Y%T=FF%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=FF%W=0
OS:%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=FF%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7
OS:(R=Y%DF=Y%T=FF%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T=FF%IPL=164%UN=
OS:0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=FF%CD=S)

Network Distance: 1 hop
Service Info: OS: Unix

TRACEROUTE
HOP RTT     ADDRESS
1   0.38 ms ip-MACHINE_IP.eu-west-1.compute.internal (10.10.35.99)

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 22.07 seconds
```

Wow. We have a bunch of services running on this machine. Let's look at the one by one.

##### FTP

As we can see in our nmap scan, we have the `ftp-anon` enabled on this machines, what refers to  "log-in" with no known password. 

```
ftp-anon: Anonymous FTP login allowed (FTP code 230)
```
Let's try to login to the FTP server.
When prompted, we can leave the password field blank and hit [ENTER]. 

```
root@ip-10-10-148-9:~# ftp MACHINE_IP
Connected to MACHINE_IP.
220 (vsFTPd 3.0.2)
Name (MACHINE_IP:root): Anonymous
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
```

Time to list all the files with the `ls` command:

```
ftp> ls
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
-rwxrwxrwx    1 0        0              39 Dec 10  2019 file.txt
drwxr-xr-x    2 0        0               6 Nov 04  2019 pub
d-wx-wx--x    2 14       50              6 Nov 04  2019 uploads
-rw-r--r--    1 0        0             224 Nov 04  2019 welcome.msg
226 Directory send OK.
```

After looking at the folders, there is nothing of interest for us, so let's grab `file.txt` & `welcome.msg` with the `get` command to move them to our personal machine:

```
ftp> get file.txt
local: file.txt remote: file.txt
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for file.txt (39 bytes).
226 Transfer complete.
39 bytes received in 0.00 secs (82.0818 kB/s)
ftp> get welcome.msg
local: welcome.msg remote: welcome.msg
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for welcome.msg (224 bytes).
226 Transfer complete.
224 bytes received in 0.00 secs (150.1373 kB/s)
``` 

Looking at the `welcome.msg` file, we see nothing of interest, it is simply a file to show us some default data about the ftp page where we logged into.

![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/Advent%20of%20Cyber%201%20%5B2019%5D/images/Day_11_welcome.PNG?raw=true)


But, if we look at the `file.txt` file, we have some usefull information for further enumeration on this machine.

![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/Advent%20of%20Cyber%201%20%5B2019%5D/images/Day_11_file.PNG?raw=true)


##### NFS

Moving on to `port 2049`: `NFS`. [NFS](https://en.wikipedia.org/wiki/Network_File_System) 
stands for `Network File System` what gives us an idea on what this application might be used for. Let's see what data we can grab from here.
To start we can list all the folders (mount points) on this `NFS` instance:

```
root@ip-10-10-240-134:~# showmount -e MACHINE_IP
Export list for MACHINE_IP:
/opt/files *
```
There seems to be only one folder/file listed: `/opt/files`. Time to mount it to our machine:

```
root@ip-10-10-240-134:~# mount MACHINE_IP:/opt/files /root/Desktop/
```

![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/Advent%20of%20Cyber%201%20%5B2019%5D/images/Day_11_creds.PNG?raw=true)

And taking a look at the file, we see a password. Yay!

![alt text](https://github.com/4lch3my/WriteUps/blob/main/TryHackMe/Advent%20of%20Cyber%201%20%5B2019%5D/images/Day_11_creds_data.PNG?raw=true)




sql

if no sql reader, install: mysql-client-core-5.7

mysql -u root -pff912ABD* -h MACHINE_IP

(-pPassword, yes! -p is WITH password!, all one word)

mysql> SELECT VERSION(), CURRENT_DATE;
+-----------+--------------+
| VERSION() | CURRENT_DATE |
+-----------+--------------+
| 5.7.28    | 2023-06-14   |
+-----------+--------------+
1 row in set (0.00 sec)

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| data               |
| mysql              |
| performance_schema |
| sys                |
+--------------------+

mysql> use data;
Reading table information for completion of table and column names


mysql> show tables;
+----------------+
| Tables_in_data |
+----------------+
| USERS          |
+----------------+
1 row in set (0.00 sec)



mysql> SELECT * FROM USERS;
+-------+--------------+
| name  | password     |
+-------+--------------+
| admin | bestpassword |
+-------+--------------+
1 row in set (0.00 sec)

mysql> 


https://dev.mysql.com/doc/refman/8.0/en/getting-information.html