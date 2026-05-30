# 🔍 Source — TryHackMe Writeup

> **Exploit a recent vulnerability in Webmin and take control of the system.**

---

## 🎯 Overview

This writeup covers the exploitation of the **Source** machine from TryHackMe, which involves discovering and leveraging a vulnerability in Webmin (a web-based system configuration tool) to gain root access. The journey showcases essential reconnaissance, vulnerability research, and exploitation techniques.

---

## ❓ Question 1: user.txt

### 🔐 Initial Reconnaissance & Discovery

#### 🚀 Network Enumeration

When initially accessing the target IP in a browser, I encountered a connection refusal error. This indicated that the service was likely not running on the default HTTP port (80). To identify available services, I executed a comprehensive Nmap scan:

```bash
nmap -sV -A -O -Pn 10.49.186.80
```

**Nmap Scan Results:**

```
Starting Nmap 7.99 ( https://nmap.org ) at 2026-05-29 22:10 +0530
Nmap scan report for 10.49.186.80
Host is up (0.068s latency).
Not shown: 998 closed tcp ports (reset)
PORT      STATE SERVICE VERSION
22/tcp    open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 b7:4c:d0:bd:e2:7b:1b:15:72:27:64:56:29:15:ea:23 (RSA)
|   256 b7:85:23:11:4f:44:fa:22:00:8e:40:77:5e:cf:28:7c (ECDSA)
|_  256 a9:fe:4b:82:bf:89:34:59:36:5b:ec:da:c2:d3:95:ce (ED25519)
10000/tcp open  http    MiniServ 1.890 (Webmin httpd)
|_http-title: Site doesn't have a title (text/html; Charset=iso-8859-1).
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.99%E=4%D=5/29%OT=22%CT=1%CU=41328%PV=Y%DS=3%DC=T%G=Y%TM=6A19C1B
OS:8%P=x86_64-pc-linux-gnu)SEQ(TI=Z%CI=Z%TS=D)SEQ(SP=103%GCD=1%ISR=102%TI=Z
OS:%CI=Z%II=I%TS=A)SEQ(SP=103%GCD=1%ISR=104%TI=Z%CI=Z%II=I%TS=A)SEQ(SP=105%
OS:GCD=1%ISR=106%TI=Z%CI=Z%II=I%TS=A)SEQ(SP=106%GCD=2%ISR=10A%TI=Z%CI=Z%II=
OS:I%TS=A)OPS(O1=M4E8ST11NW6%O2=M4E8ST11NW6%O3=M4E8NNT11NW6%O4=M4E8ST11NW6%
OS:O5=M4E8ST11NW6%O6=M4E8ST11)WIN(W1=F4B3%W2=F4B3%W3=F4B3%W4=F4B3%W5=F4B3%W
OS:6=F4B3)ECN(R=Y%DF=Y%T=40%W=F507%O=M4E8NNSNW6%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=
OS:O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD
OS:=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0
OS:%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1
OS:(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI
OS:=N%T=40%CD=S)

Network Distance: 3 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 554/tcp)
HOP RTT      ADDRESS
1   67.45 ms 192.168.128.1
2   ...
3   71.54 ms 10.49.186.80

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 55.03 seconds
```

**Key Finding:** Port 10000 is open, running **MiniServ 1.890 (Webmin httpd)**. This explains the initial connection refusal on the default HTTP port.

#### 🌐 Accessing the Web Interface

After identifying port 10000, I accessed it directly in the browser:

![Webmin Login Page](https://miro.medium.com/v2/resize:fit:720/format:webp/1*vrEeRLx8X8O9xCRHblefIQ.png)

And i got the DNS Error.

#### 🔗 DNS & SSL Configuration Issues

The browser's automatic redirection led to a DNS error. To resolve this, I added the target hostname mapping to my local `/etc/hosts` file:

```
10.49.186.80 ip-10-49-186-80.ap-south-1.compute.internal
```


After updating the hosts file, I encountered an SSL certificate error (the application uses a self-signed certificate):

![SSL Certificate Error](https://supporthost.com/wp-content/uploads/2022/06/chrome-err-cert-authority-invalid-selfsigned-certificate-1024x496.png)

**Solution:** I clicked the "Advanced" button and proceeded to bypass the SSL warning.


#### ✅ Webmin Login Page

Once the SSL warning was bypassed, the Webmin login interface appeared:

![Webmin Dashboard](https://miro.medium.com/v2/resize:fit:720/format:webp/1*Yb6sl7s_Tlwy8a-zZv4hdQ.png)

---

### 🛠️ Vulnerability Research & Exploitation

#### 🎯 Identifying the Vulnerable Version

From the Nmap output, I identified **Webmin 1.890**. Using the `searchsploit` tool to search for known vulnerabilities:

```bash
searchsploit webmin 1.890
```

**Result:**
```
Webmin 1.920 - Remote Code Execution | linux/webapps/47293.sh
```

#### 💣 Attempting the Public Exploit

The exploit can be executed using:

```bash
bash <exploitname> <target_url>
```

**Example:**
```bash
bash exploit.sh https://ip-10-49-186-80.ap-south-1.compute.internal:10000/
```

However, after execution, the output indicated:

```
Testing for RCE (CVE-2019-15107) on https://ip-10-49-186-80.ap-south-1.compute.internal:10000/: 
\033[0;32mOK! (target is not vulnerable)\033[0m
```

This was unexpected—the target appeared immune to the script-based exploit.

#### 🚀 Leveraging Metasploit Framework

Since the standalone script-based exploit failed, I turned to the **Metasploit Framework**, which provided a more sophisticated exploitation mechanism:

![Metasploit Shell](https://miro.medium.com/v2/resize:fit:720/format:webp/0*m33MD_uffI7nbwq8.png)

✨ **Success!** The Metasploit exploit granted me a **root-level shell** directly, without requiring privilege escalation.

#### 📍 Flag Retrieval

After gaining shell access, I located and captured the user flag:

**User Flag Location:** `/home/dark/user.txt`

---

## ❓ Question 2: root.txt

### 🏆 Root Access Achievement

Through the Metasploit exploitation, I immediately obtained **root privileges**. This eliminated the need for post-exploitation privilege escalation.

**Root Flag Location:** `/root/root.txt`

---

## 🎓 Key Takeaways

1. **Port Discovery:** Always scan for non-standard ports when standard services appear unreachable.
2. **Vulnerability Research:** Cross-reference version numbers with known CVEs using tools like `searchsploit`.
3. **Tool Selection:** When one exploitation method fails, alternative frameworks like Metasploit can provide successful pathways.
4. **DNS & SSL Handling:** Proper configuration of host mappings and SSL certificate handling is crucial for accessing web-based applications.

---

## 📚 References & Tools Used

- **Nmap:** Network reconnaissance and service enumeration
- **Searchsploit:** CVE and exploit database search
- **Metasploit Framework:** Advanced exploitation and payload delivery
- **TryHackMe:** Challenge platform

---

*Writeup completed and verified. Happy hacking!* 🎉
