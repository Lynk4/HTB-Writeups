# Subatomic

---

### Sherlock Scenario

Forela is in need of your assistance. They were informed by an employee that  their Discord account had been used to send a message with a link to a file they suspect is malware. The message read: "Hi! I've been working on a new game I think you may be interested in it. It combines a number of games we like to play together, check it out!". The Forela user has 
tried to secure their Discord account, but somehow the messages keep being sent. They need your help to understand this malware and regain control of their account! **Warning:** This is a warning that this Sherlock includes software that is going to  interact with your computer and files. This software has been intentionally included for educational purposes and is NOT intended to be executed or used otherwise. Always handle such files in isolated, controlled, and secure environments. Once the Sherlock zip has been unzipped, you will find a DANGER.txt  file. Please read this to proceed.

---

## Task 1:

**What is the Imphash of this malware installer?**

```
b34f154ec913d2d2c435cbd644e91687
```

upload the malware to virustotal. In details you will find Imphash.

![Screenshot 2025-11-11 at 11.41.45 AM.png](Subatomic/Screenshot_2025-11-11_at_11.41.45_AM.png)

---

## Task 2:

**The malware contains a digital signature. What is the program name specified in the `SpcSpOpusInfo` Data Structure?**

```
Windows Update Assistant
```

Found answer in virus total.

![Screenshot 2025-11-11 at 11.45.45 AM.png](Subatomic/Screenshot_2025-11-11_at_11.45.45_AM.png)

---

## **Task 3:**

**The malware uses a unique GUID during installation, what is this GUID?**

```
cfbc383d-9aa0-5771-9485-7b806e8442d5
```

use flare vm open the exe in 7z which will allow full access.

inside it **[NSIS].nsi -** File which is responsible for the procedure taken during the  installation of malware. Analyze it.

![Screenshot 2025-11-11 at 11.50.36 AM.png](Subatomic/Screenshot_2025-11-11_at_11.50.36_AM.png)

![Screenshot 2025-11-11 at 11.55.00 AM.png](Subatomic/Screenshot_2025-11-11_at_11.55.00_AM.png)

---

## Task 4:

**The malware contains a package.json file with metadata associated with it. What is the 'License' tied to this malware?**

```
ISC
```

Found app-32.7z inside $PLUGINSDIR

![Screenshot 2025-11-11 at 11.57.07 AM.png](Subatomic/Screenshot_2025-11-11_at_11.57.07_AM.png)

app.asar in resources dir .asar is a another format for archive. refer this link:  https://github.com/electron/asar

![Screenshot 2025-11-11 at 12.01.13 PM.png](Subatomic/Screenshot_2025-11-11_at_12.01.13_PM.png)

Extract all the files.

![Screenshot 2025-11-11 at 12.09.18 PM.png](Subatomic/Screenshot_2025-11-11_at_12.09.18_PM.png)

Open package.json

![Screenshot 2025-11-11 at 12.11.50 PM.png](Subatomic/Screenshot_2025-11-11_at_12.11.50_PM.png)

---

## Task 5:

**The malware connects back to a C2 address during execution. What is the domain used for C2?**

```
illitmagnetic.site
```

I obtain the answer during initial analysis using virustotal.

![Screenshot 2025-11-11 at 12.16.23 PM.png](Subatomic/Screenshot_2025-11-11_at_12.16.23_PM.png)

---

## Task 6:

**The malware attempts to get the public IP address of an infected system. What is the full URL used to retrieve this information?**

```
https://ipinfo.io/json
```

app.js contains the malicious code. It is obfuscated.

![Screenshot 2025-11-11 at 12.22.11 PM.png](Subatomic/Screenshot_2025-11-11_at_12.22.11_PM.png)

![Screenshot 2025-11-11 at 12.46.36 PM.png](Subatomic/Screenshot_2025-11-11_at_12.46.36_PM.png)

It's worth emphasizing that the malware analysis system I use operates on a virtual computer that is separated from the internet and disables file and clipboard sharing.  Before beginning the investigation, I took a picture of the virtual computer.  I opened the malicious code with VSCode, which is pre-installed on FlareVM.  The initial attempt to execute the script in debug mode failed. Despite extracting all files, the script encountered a problem with the primno/dpapi.

I discovered the primno directory in node_modules and restored the package using npm install @primno/dpapi.  Unfortunately, the following attempt to run the script was equally unsuccessful. This time, the issue was with the sqlite3 dependence.  I used the same procedure as previously, removing the sqlite3 directory from node_modules and restoring the dependency using the command npm install sqlite3.

![Screenshot 2025-11-11 at 12.48.28 PM.png](Subatomic/Screenshot_2025-11-11_at_12.48.28_PM.png)

After rerunning the script in debug mode, it completed correctly.  In the "Loaded Scripts" column in the bottom left corner of **eval**, I discovered the deobfuscated app.js script.

![Screenshot 2025-11-11 at 12.51.13 PM.png](Subatomic/Screenshot_2025-11-11_at_12.51.13_PM.png)

deobfuscated `app.js` script

![Screenshot 2025-11-11 at 12.54.19 PM.png](Subatomic/Screenshot_2025-11-11_at_12.54.19_PM.png)

Finding the function in charge of getting the public IP address was simple since, based on my first research with VirusTotal, I knew I was looking for a reference to the [ipinfo.io](http://ipinfo.io/) site.

![Screenshot 2025-11-11 at 1.59.27 PM.png](Subatomic/Screenshot_2025-11-11_at_1.59.27_PM.png)

---

## Task 7:

**The malware is looking for a particular path to connect back on. What is the full URL used for C2 of this malware?**

```
https://illitmagnetic.site/api/
```

![Screenshot 2025-11-11 at 2.07.23 PM.png](Subatomic/Screenshot_2025-11-11_at_2.07.23_PM.png)

---

## Task 8:

**The malware has a configured `user_id` which is sent to the C2 in the headers or body on every request. What is the key or variable name sent which contains the user_id value?**

```
duvet_user
```

![Screenshot 2025-11-11 at 2.10.29 PM.png](Subatomic/Screenshot_2025-11-11_at_2.10.29_PM.png)

---

## Task 9:

**The malware checks for a number of hostnames upon execution, and if any are found it will terminate. What hostname is it looking for that begins with `arch`?**

```
archibaldpc
```

Using the file search function search for arch.

![Screenshot 2025-11-11 at 2.13.05 PM.png](Subatomic/Screenshot_2025-11-11_at_2.13.05_PM.png)

---

## Task 10:

**The malware looks for a number of processes when checking if it is running in a VM; however, the malware author has mistakenly made it check for the same process twice. What is the name of this process?**

```
vmwaretray
```

![Screenshot 2025-11-11 at 2.16.29 PM.png](Subatomic/Screenshot_2025-11-11_at_2.16.29_PM.png)

---

## Task 11:

The malware has a special function which checks to see if `C:\Windows\system32\cmd.exe` exists. If it doesn't it will write a file from the C2 server to an unusual location on disk using the environment variable `USERPROFILE`. What is the location it will be written to?

```
%USERPROFILE%\Documents\cmd.exe
```

Once again using the file search function.

![Screenshot 2025-11-11 at 2.19.15 PM.png](Subatomic/Screenshot_2025-11-11_at_2.19.15_PM.png)

---

## Task 12:

**The malware appears to be targeting browsers as much as Discord. What command is run to locate Firefox cookies on the system?**

```
where /r . cookies.sqlite
```

use the file search option again. search firefoxcookie.

![Screenshot 2025-11-11 at 2.24.05 PM.png](Subatomic/Screenshot_2025-11-11_at_2.24.05_PM.png)

---

## Task 13:

**To finally eradicate the malware, Forela needs you to find out what Discord module has been modified by the malware so they can clean it up. What is the Discord module infected by this malware, and what's the name of the infected file?**

```
discord_desktop_core-1, index.js
```

![Screenshot 2025-11-11 at 2.35.30 PM.png](Subatomic/Screenshot_2025-11-11_at_2.35.30_PM.png)

---