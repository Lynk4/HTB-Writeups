##### HTB fs0ciety [easy] Challenge



#### CHALLENGE DESCRIPTION: We believe that there is an SSH Password inside password protected 'ZIP' folder. Can you crack the 'ZIP' folder and get the SSH password?

Link:https://app.hackthebox.com/challenges/20

![image](https://user-images.githubusercontent.com/44930131/233727965-65306c16-1aa1-4b0d-84dd-eb5aa4b5b56f.png)

First, I downloaded a password-protected zip file, then unzipped it with the hackthebox password to get another zip file.



![image](https://user-images.githubusercontent.com/44930131/233728170-14e2b115-81cf-486c-8727-db03c8a10ea3.png)

Now I used the zip2john cracking tool which is called john the ripper to extract the hash of the password protected file.

![image](https://user-images.githubusercontent.com/44930131/233728238-323f46b2-72ab-4fe5-9373-da0e673a32cf.png)

It's time to crack the hash using John the ripper......

```
root@kali /h/l/h/# john hash --wordlist=/usr/share/wordlists/rockyou.txt
```
If you try to crack the hash again it will just show No password hashes left to crack (see FAQ)
use --show option to view the password.

```
root@kali /h/l/h/# john hash --show
```

![image](https://user-images.githubusercontent.com/44930131/233728457-bd8bcf8e-9a3e-40f6-b8e1-31a39e1d5573.png)

###### password: justdoit

Then I unzip the zip file I found one text file

![image](https://user-images.githubusercontent.com/44930131/233728626-897dbad4-5328-4344-9e13-c415ca221dc9.png)

Oh, there's a base64 encoded string in this file.

![image](https://user-images.githubusercontent.com/44930131/233728735-4b9e898b-d3df-4862-bbd4-924749b8b319.png)

Now I used the base64 command to decode that string.

![image](https://user-images.githubusercontent.com/44930131/233728796-aeee469a-f70f-4273-8355-a05ffad3a843.png)

Atlast I used a binary to ASCII converter online.
https://www.rapidtables.com/convert/number/binary-to-ascii.html

![image](https://user-images.githubusercontent.com/44930131/233728883-63b1015d-d435-425c-ac5d-c40329d11ced.png)

Put that string between HTB{}, and you have your flag........

