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



