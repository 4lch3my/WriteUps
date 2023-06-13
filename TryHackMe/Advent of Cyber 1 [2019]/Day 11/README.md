nmap 

nmap -sC -sV -A 10.10.35.99
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
|      Connected to 10.10.240.134
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
1   0.38 ms ip-10-10-35-99.eu-west-1.compute.internal (10.10.35.99)

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 22.07 seconds




ftp



nfs

root@ip-10-10-240-134:~# showmount -e 10.10.35.99
Export list for 10.10.35.99:
/opt/files *

mount 10.10.35.99:/opt/files /root/Desktop/

sql

if no sql reader, install: mysql-client-core-5.7

mysql -u root -pff912ABD* -h 10.10.35.99

(-pPassword, yes! -p is WITH password!, all one word)