# Sau

![1](https://github.com/joaorelva/HackTheBox/assets/45034257/f93822ea-fabd-4300-a5f9-4ee8471aa457)

## Enumeration

nmap -A -sV -sS <target_ip>

![2](https://github.com/joaorelva/HackTheBox/assets/45034257/1ec31b16-7561-4a1b-8962-aa8d26444933)

Port 55555 is running a “request baskets” service. Request baskets is a web service to collect arbitrary HTTP requests and inspect them via RESTful API or simple web UI. We can see that the service running is in 1.2.1 version.

![4](https://github.com/joaorelva/HackTheBox/assets/45034257/b712821b-5781-4bc3-8d4f-f99e5a9b47ed)
![3](https://github.com/joaorelva/HackTheBox/assets/45034257/cefb8aa7-7494-462a-b174-8d3319a7f47f)

## Exploitation

After some research i found that this version have one vulnerability that can be exploitable (CVE-2023-27163). Request baskets service is vulnerable to a SSRF attack as we can see in the below proof of concept:

https://github.com/entr0pie/CVE-2023-27163#poc-of-ssrf-on-request-baskets-cve-2023-27163

Essentially, when we have a SSRF vulnerability, a vulnerable server can make request to other internal services on behalf of the attacker.

![5](https://github.com/joaorelva/HackTheBox/assets/45034257/cbd4cb1e-b210-4a79-8649-22512031da2d)

As we can saw in the enumeration step, this machine have a service running in port 80. So i created a basket to make a request to the port 80 of the target. And i had success.

After that i saw that is running Maltrail v0.53. It’s a vulnerable version and we can found the exploit in https://github.com/spookier/Maltrail-v0.53-Exploit#usage.

![7](https://github.com/joaorelva/HackTheBox/assets/45034257/dccd2070-a74b-471f-af5c-6f97a94275f4)
![6](https://github.com/joaorelva/HackTheBox/assets/45034257/4373a6f3-ce35-426d-ad6b-db1f14f49267)
![8](https://github.com/joaorelva/HackTheBox/assets/45034257/cadd6aab-81de-4381-b1a7-ff6186e87fa8)

User flag found in the “puma” user directory.

## Privilege Escalation

sudo -l to see what commands can the user run as root

![9](https://github.com/joaorelva/HackTheBox/assets/45034257/81a54b6f-a6d7-43ca-b773-46a8f1188d1c)

If we can execute systemctl status as root, we can spawn another shell in the pager with root privileges:

https://gtfobins.github.io/gtfobins/systemctl/

![10](https://github.com/joaorelva/HackTheBox/assets/45034257/b6fe1335-3049-44a9-b6bf-b75e266c2c15)

Root flag found in root directory.
