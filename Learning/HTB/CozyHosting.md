
IP: 10.10.11.230

Begin running an [[Nmap]] scan to see what ports are open.

`nmap -sV 10.10.11.230`

Nmap output: 

```
Starting Nmap 7.93 ( https://nmap.org ) at 2023-11-02 23:32 EDT
Nmap scan report for 10.10.11.230
Host is up (0.14s latency).
Not shown: 996 closed tcp ports (conn-refused)
PORT     STATE SERVICE   VERSION
22/tcp   open  ssh       OpenSSH 8.9p1 Ubuntu 3ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp   open  http      nginx 1.18.0 (Ubuntu)
8000/tcp open  http-alt?
8083/tcp open  us-srv?
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 118.14 seconds

```

Looks like there is an Nginx server running on either port 80, 8000, or both. Next step will be to see what is going on on those ports in the browser. 

When navigating to the IP on port 80, there is a redirect to the hostname cozyhosting.htb. This must be added to the `/etc/hosts` file and associated with the IP for everything to work properly. 



