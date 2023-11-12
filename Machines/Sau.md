# Sau

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/f9bfba05-ffac-4cb7-8ab6-1bb2160c2151/6c9b7d74-5ced-4b44-86b1-88cfcb75ec7c/Untitled.png)

## Enumeration

nmap -A -sV -sS <target_ip>

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/f9bfba05-ffac-4cb7-8ab6-1bb2160c2151/16eae50b-0c06-4557-9d9b-9a715615726f/Untitled.png)

Port 55555 is running a “request baskets” service. Request baskets is a web service to collect arbitrary HTTP requests and inspect them via RESTful API or simple web UI. We can see that the service running is in 1.2.1 version.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/f9bfba05-ffac-4cb7-8ab6-1bb2160c2151/7e10a5bc-29ba-4eb3-88f4-361d2078cafd/Untitled.png)

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/f9bfba05-ffac-4cb7-8ab6-1bb2160c2151/8d3432e7-91a9-4853-b854-a418ca95a17f/Untitled.png)

## Exploitation

After some research i found that this version have one vulnerability that can be exploitable (CVE-2023-27163). Request baskets service is vulnerable to a SSRF attack as we can see in the below proof of concept:

https://github.com/entr0pie/CVE-2023-27163#poc-of-ssrf-on-request-baskets-cve-2023-27163

Essentially, when we have a SSRF vulnerability, a vulnerable server can make request to other internal services on behalf of the attacker.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/f9bfba05-ffac-4cb7-8ab6-1bb2160c2151/4f3c4397-5acc-49da-b682-fba0b5d3f58f/Untitled.png)

As we can saw in the enumeration step, this machine have a service running in port 80. So i created a basket to make a request to the port 80 of the target. And i had success.

After that i saw that is running Maltrail v0.53. It’s a vulnerable version and we can found the exploit in https://github.com/spookier/Maltrail-v0.53-Exploit#usage.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/f9bfba05-ffac-4cb7-8ab6-1bb2160c2151/89f080d9-466a-4228-85dd-832b48e037b1/Untitled.png)

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/f9bfba05-ffac-4cb7-8ab6-1bb2160c2151/40e72aa0-a120-411e-8e0d-24014f64c574/Untitled.png)

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/f9bfba05-ffac-4cb7-8ab6-1bb2160c2151/3847da76-2185-4928-b27e-d3850d5c18f3/Untitled.png)

User flag found in the “puma” user directory.

## Privilege Escalation

sudo -l to see what commands can the user run as root

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/f9bfba05-ffac-4cb7-8ab6-1bb2160c2151/b756b83e-29f7-42c9-a67d-d24c74d669e0/Untitled.png)

If we can execute systemctl status as root, we can spawn another shell in the pager with root privileges:

https://gtfobins.github.io/gtfobins/systemctl/

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/f9bfba05-ffac-4cb7-8ab6-1bb2160c2151/12a96ed7-8142-41cb-9690-2f78191190c7/Untitled.png)

Root flag found in root directory.# Sau

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/f9bfba05-ffac-4cb7-8ab6-1bb2160c2151/6c9b7d74-5ced-4b44-86b1-88cfcb75ec7c/Untitled.png)

## Enumeration

nmap -A -sV -sS <target_ip>

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/f9bfba05-ffac-4cb7-8ab6-1bb2160c2151/16eae50b-0c06-4557-9d9b-9a715615726f/Untitled.png)

Port 55555 is running a “request baskets” service. Request baskets is a web service to collect arbitrary HTTP requests and inspect them via RESTful API or simple web UI. We can see that the service running is in 1.2.1 version.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/f9bfba05-ffac-4cb7-8ab6-1bb2160c2151/7e10a5bc-29ba-4eb3-88f4-361d2078cafd/Untitled.png)

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/f9bfba05-ffac-4cb7-8ab6-1bb2160c2151/8d3432e7-91a9-4853-b854-a418ca95a17f/Untitled.png)

## Exploitation

After some research i found that this version have one vulnerability that can be exploitable (CVE-2023-27163). Request baskets service is vulnerable to a SSRF attack as we can see in the below proof of concept:

https://github.com/entr0pie/CVE-2023-27163#poc-of-ssrf-on-request-baskets-cve-2023-27163

Essentially, when we have a SSRF vulnerability, a vulnerable server can make request to other internal services on behalf of the attacker.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/f9bfba05-ffac-4cb7-8ab6-1bb2160c2151/4f3c4397-5acc-49da-b682-fba0b5d3f58f/Untitled.png)

As we can saw in the enumeration step, this machine have a service running in port 80. So i created a basket to make a request to the port 80 of the target. And i had success.

After that i saw that is running Maltrail v0.53. It’s a vulnerable version and we can found the exploit in https://github.com/spookier/Maltrail-v0.53-Exploit#usage.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/f9bfba05-ffac-4cb7-8ab6-1bb2160c2151/89f080d9-466a-4228-85dd-832b48e037b1/Untitled.png)

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/f9bfba05-ffac-4cb7-8ab6-1bb2160c2151/40e72aa0-a120-411e-8e0d-24014f64c574/Untitled.png)

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/f9bfba05-ffac-4cb7-8ab6-1bb2160c2151/3847da76-2185-4928-b27e-d3850d5c18f3/Untitled.png)

User flag found in the “puma” user directory.

## Privilege Escalation

sudo -l to see what commands can the user run as root

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/f9bfba05-ffac-4cb7-8ab6-1bb2160c2151/b756b83e-29f7-42c9-a67d-d24c74d669e0/Untitled.png)

If we can execute systemctl status as root, we can spawn another shell in the pager with root privileges:

https://gtfobins.github.io/gtfobins/systemctl/

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/f9bfba05-ffac-4cb7-8ab6-1bb2160c2151/12a96ed7-8142-41cb-9690-2f78191190c7/Untitled.png)

Root flag found in root directory.
