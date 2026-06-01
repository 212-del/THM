# 🎯 Brute-It Writeup - Complete Solution Guide

## 📋 Table of Contents
1. [Initial Reconnaissance](#-initial-reconnaissance)
2. [Nmap & Directory Enumeration](#-nmap--directory-enumeration)
3. [SSH Key Discovery & Passphrase Cracking](#-ssh-key-discovery--passphrase-cracking)
4. [Web Admin Panel Brute Force](#-web-admin-panel-brute-force)
5. [SSH Access & User Flag](#-ssh-access--user-flag)
6. [Privilege Escalation](#-privilege-escalation)
7. [Password Hash Cracking](#-password-hash-cracking)

---

## 🔍 Initial Reconnaissance

After obtaining the target IP address and opening it in a browser, the server responded with the Apache2 default page.

![Apache Default Page](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh2jXbyL79IpN7aO7Fa1XYpoeJnjz-1ADwUPUkO6rNUwbUP9VDzTwsFb2EouiY4Sn_aWT1I66fJ2yZ4iAuQpyApSRF2QBga4agCEMuNGlIp5OFbJnY5qiNrcEghj0hp0-aeWiG4lnt7AFU/s640/Apache+Default+Page.PNG)

### Initial Steps
At this point, I initiated two critical reconnaissance tasks:
- **Directory Enumeration** (recursive) - to discover hidden endpoints and sensitive files
- **Nmap Scan** - to identify open ports, services, and their versions

---

## 🌐 Nmap & Directory Enumeration

### Discovered Endpoints
Through recursive directory enumeration, the following endpoints were identified:
- `/admin` - Admin login portal
- `/admin/.zip` - Backup archive
- `/admin/panel/id_rsa` - SSH private key file

### Open Ports & Services
The Nmap scan revealed two open ports:
- **Port 22** - SSH Service
- **Port 80** - HTTP Web Server

---

## ❓ Question 1: How Many Ports Are Open?

**Answer: 2 ports** (Port 22 and Port 80)

---

## ❓ Question 2: What Version of SSH Is Running?

From the Nmap scan results:
**Answer: OpenSSH 7.6p1**

The SSH service version was clearly identified in the detailed Nmap output.

---

## ❓ Question 3: What Version of Apache Is Running?

From the Nmap scan results:
**Answer: Apache 2.4.29**

All service version information was obtained directly from the comprehensive Nmap scan results.

---

## ❓ Question 4: What Is the Hidden Endpoint?

The hidden endpoint discovered was **`/admin`**

When we navigated to this endpoint, we were presented with a login page:

![Admin Login Page](https://miro.medium.com/v2/resize:fit:640/format:webp/0*3D2PYB0UO9YSof__.png)

### Important Discovery 🔑
By examining the HTML source code of the login page, we found a helpful comment:

```html
<!-- Hey john, if you do not remember, the username is admin -->
```

This comment confirmed that the username is **`admin`**, and also hinted at another user named **`john`** who might have important roles in the system.

---

## 🔐 SSH Key Discovery & Passphrase Cracking

### Finding the SSH Private Key
During directory enumeration, we discovered the SSH private key at the location:
**`/admin/panel/id_rsa`**

### Initial Challenge: Passphrase Protection
When attempting SSH login with the key, we were prompted to enter a passphrase, which we did not initially know. This is a common security practice to add an extra layer of protection to SSH keys.

#### Step 1: Convert SSH Key to Hash Format

To crack the passphrase, we first converted the SSH key to a hash format using `ssh2john`:

```bash
ssh2john <path to key> <path to hashed key>
```

**What is ssh2john?**
`ssh2john` is a utility that extracts the passphrase hash from an SSH private key file, making it suitable for dictionary attacks with tools like John the Ripper. This tool essentially converts the SSH key format into a format that password cracking tools can work with.

#### Step 2: Initial Cracking Attempts

We started by attempting to crack the hash using John the Ripper with standard wordlists:

```bash
john --wordlist=/path/to/wordlist key_hash
```

Unfortunately, the initial wordlist attempts were unsuccessful. This is not uncommon, as passphrases can be arbitrary and may not appear in standard word lists.

#### Step 3: Successful Passphrase Cracking

After trying multiple individual wordlists without success, I realized a more comprehensive approach was needed. I created a bash loop to iterate through multiple wordlist files from the Seclists collection:

```bash
for file in /home/seclists/password/common-credentials/*.txt; do
john --wordlist=$file hashed_key
done
```

This iterative approach tests the passphrase hash against many different wordlists sequentially. The loop continued until John the Ripper finally found a match in one of the wordlists and revealed the SSH passphrase!

#### Step 4: SSH Access

With the discovered passphrase, we successfully logged into the SSH shell:

```bash
ssh -i key john@10.49.161.41
```

When prompted, we entered the decrypted passphrase we discovered.

**🚨 Important Security Note:** Before establishing an SSH connection using a private key file, it is crucial to set the correct file permissions:

```bash
chmod 600 key
```

This ensures the key file has read/write permissions (600) only for the owner, preventing potential security vulnerabilities where other users or processes could read or modify the key.

---

## 🎯 Web Admin Panel Brute Force

### Challenge: CSRF Token Protection
The web admin panel required a CSRF (Cross-Site Request Forgery) token for form submissions, making simple brute force attempts ineffective. However, CSRF tokens alone do not prevent brute force attacks if the token is not properly validated or if it can be easily obtained.

#### Understanding the Request Structure

To understand the exact format of HTTP requests the web application was accepting, we examined the requests using `curl`:

```bash
curl -i -L \
  -c cookies.txt \
  -b cookies.txt \
  -d "user=admin&pass=Wrongpass" \
  http://10.49.161.41/admin/
```

**Request Parameter Breakdown:**
- `-i` : Include response headers in output
- `-L` : Follow redirects automatically
- `-c cookies.txt` : Save cookies to a file (session management)
- `-b cookies.txt` : Use cookies from the file in the request
- `-d` : POST data with username and password fields

The response correctly indicated an error: "username or password is invalid", which confirmed that our curl command was properly formatted and could be adapted for fuzzing.

#### Step 1: Determine Response Size

Before fuzzing with multiple passwords, we needed to establish a baseline by determining the response size for an incorrect password:

```bash
curl -s -L -d "user=admin&pass=wrong" http://10.49.161.41/admin/ | wc -c
```

This command helped us identify the expected response size (in bytes) for a failed login attempt. This size becomes our filtering baseline in the fuzzing attack—any response that differs significantly from this size is likely a successful authentication.

#### Step 2: Brute Force with ffuf

With the proper request format understood and the response size baseline established, we performed the actual brute force attack using ffuf:

```bash
ffuf -u http://10.49.156.209/admin/ \
     -X POST \
     -H "Content-Type: application/x-www-form-urlencoded" \
     -d "user=admin&pass=FUZZ" \
     -w /home/Seclists/Passwords/Leaked-Databases/rockyou-75.txt -fs 733
```

**ffuf Parameters Explained:**
- `-u` : Target URL where the fuzzing will occur
- `-X POST` : HTTP method to use (POST for form submission)
- `-H` : Custom request header (content type for form data)
- `-d` : POST data with FUZZ placeholder (will be replaced with wordlist entries)
- `-w` : Wordlist file containing potential passwords to try
- `-fs 733` : Filter responses with a size of 733 bytes (failed login attempts)

**How This Works:**
ffuf iterates through each password in the wordlist, replacing "FUZZ" with each candidate password and sending the request. Responses matching the filter size (failed attempts) are hidden, so we only see responses that indicate success—which would have a different size.

### Result: Successful Brute Force 🎉

![Web Interface After Login](https://miro.medium.com/v2/resize:fit:640/format:webp/0*NhbuSbJwpw0EYBTx.png)

After the successful brute force attack, we gained access to the admin panel and discovered the **web flag**. This demonstrates the importance of implementing proper rate limiting and account lockout policies to prevent brute force attacks in production environments.

---

## 🚪 SSH Access & User Flag

### Establishing SSH Shell Access

With the SSH key passphrase successfully cracked, we returned to our SSH shell with user-level access to the system.

### Retrieving the User Flag

Once inside the system, we located the user flag at:

**`/home/john/user.txt`**

---

## ❓ Question 5: What Is User.txt?

**Answer:** The flag located at `/home/john/user.txt`

This file contains the user-level flag that proves we successfully compromised the user account.

---

## 🛡️ Privilege Escalation

### Initial Assessment

From our SSH shell (running as user `john`), we needed to identify methods for escalating our privileges to root level. This is a critical phase in penetration testing.

#### Checking Sudo Privileges

The first and most important step in privilege escalation assessment is to check what commands the current user can execute with elevated privileges using sudo:

```bash
sudo -l
```

### Critical Discovery ⚠️

The sudo privileges check revealed a **significant security misconfiguration**: the binary `/bin/cat` can be executed as root **without requiring a password**!

This is dangerous because `cat` is a utility that can read file contents, and with root privileges, it can read any file on the system, including sensitive configuration files and other users' data.

#### Retrieving the Root Flag

Exploiting this misconfiguration, we executed:

```bash
sudo /bin/cat /root/root.txt
```

**Success!** We obtained the root flag without needing the actual root password. This demonstrates how misconfigurations in sudo privileges can lead to trivial privilege escalation.

---

## ❓ Question 6: What Is Root.txt?

**Answer:** The flag located at `/root/root.txt` (obtained via `sudo /bin/cat`)

---

## 🔑 Password Hash Cracking - Finding Root Password

### Challenge: Finding the Root Password

The final question required us to discover the root user's password. To accomplish this, we needed to examine password hashes stored on the system and crack them.

### Understanding Linux Password Storage 📚

In Unix/Linux systems, password hashes are stored in the `/etc/shadow` file (not `/etc/passwd`), which is only readable by root. The format is:

```
username:password_hash:lastchanged:minimum:maximum:warn:inactive:expire
```

**Field Descriptions:**

| Field | Description |
|-------|-------------|
| **Username** | Login name of the user account |
| **Password Hash** | Encrypted password hash (minimum 8-12 characters including special characters, digits, and lowercase letters) |
| **Last Password Change** | Days since January 1, 1970 when password was last changed (epoch time) |
| **Minimum** | Minimum number of days required between password changes |
| **Maximum** | Maximum number of days the password is valid; user is forced to change after this period expires |
| **Warn** | Days before expiration when user receives a warning to change their password |
| **Inactive** | Days after password expiration when account is automatically disabled |
| **Expire** | Absolute expiration date (days since Jan 1, 1970) when the login may no longer be used |

### Password Hash Format and Algorithms

Password hashes follow the format: `$id$salt$hashed`

Where `$id` represents the hashing algorithm used:
- **`$1$`** - MD5 (weak, legacy)
- **`$2a$`** - Blowfish (modern, strong)
- **`$2y$`** - Blowfish variant (PHP-compatible)
- **`$5$`** - SHA-256 (strong, widely used)
- **`$6$`** - SHA-512 (very strong, recommended)

### Extracted Password Hashes

From the system's `/etc/shadow` file (accessed with root privileges via sudo), we extracted the following hashes:

**User: thm**
```
thm:$6$hAlc6HXuBJHNjKzc$NPo/0/iuwh3.86PgaO97jTJJ/hmb0nPj8S/V6lZDsjUeszxFVZvuHsfcirm4zZ11IUqcoB9IEWYiCV.wcuzIZ.:18489:0:99999:7:::
```

**User: sshd**
```
sshd:*:18489:0:99999:7:::
```
*(The asterisk indicates this account cannot log in)*

**User: john**
```
john:$6$iODd0YaH$BA2G28eil/ZUZAV5uNaiNPE0Pa6XHWUFp7uNTp2mooxwa4UzhfC0kjpzPimy1slPNm9r/9soRw8KqrSgfDPfI0:18490:0:99999:7:::
```

**User: root** (Our Target) 🎯
```
root:$6$zdk0.jUm$Vya24cGzM1duJkwM5b17Q205xDJ47LOAg/OpZvJ1gKbLF8PJBdKJA4a6M.JYPUTAaWu4infDjI88U9yUXEVgL.:18490:0:99999:7:::
```

### Cracking the Root Password Hash

To crack the root password, we extracted the root hash and used John the Ripper with an iterative approach through multiple wordlists:

**Step 1: Create a file with the root hash:**
```bash
root:$6$zdk0.jUm$Vya24cGzM1duJkwM5b17Q205xDJ47LOAg/OpZvJ1gKbLF8PJBdKJA4a6M.JYPUTAaWu4infDjI88U9yUXEVgL.:18490:0:99999:7:::
```

**Step 2: Execute the cracking process using multiple wordlists:**
```bash
for file in /home/Seclists/Passwords/Common-Credentials/*.txt; do
john --wordlist=$file pass
done
```

This loop systematically tests the root password hash against multiple wordlists until John the Ripper finds a match.

### Success! 🎉

The iterative John the Ripper attack successfully cracked the root password hash, revealing the root user's password. This password can now be used to log in as the root user directly or to verify access control.

---

## ❓ Final Question: What Is the Root's Password?

**Answer:** [The password discovered through the hash cracking process above]

---

## 📊 Attack Summary & Key Techniques Used

### Techniques Demonstrated:

| Technique | Purpose | Tool Used |
|-----------|---------|-----------|
| Directory Enumeration | Discover hidden endpoints and files | Enum tools (recursive) |
| Port Scanning | Identify services and versions | Nmap |
| Source Code Analysis | Extract credentials and hints | Web Browser |
| SSH Key Extraction | Obtain authentication material | Directory discovery |
| Passphrase Cracking | Decrypt SSH private key | ssh2john + John the Ripper |
| Web Application Testing | Understand request/response format | curl |
| Brute Force Attack | Guess login credentials | ffuf |
| Privilege Escalation | Gain higher access levels | Sudo misconfiguration |
| Password Hash Cracking | Recover plaintext passwords | John the Ripper |

---

## 🎓 Key Takeaways & Security Lessons

✅ **Directory Enumeration Importance**: Hidden endpoints can contain sensitive files and credentials. Always perform thorough reconnaissance.

✅ **SSH Key Security**: Protect SSH private keys with strong passphrases. Even if the key is compromised, the passphrase adds an extra layer of security.

✅ **CSRF Tokens Alone**: CSRF tokens do not prevent brute force attacks if they're not properly validated or if they're easily obtainable.

✅ **Sudo Misconfiguration Risks**: Allowing powerful utilities like `cat` to run as root without a password creates a direct path to privilege escalation.

✅ **Password Strength Matters**: Strong, unique passwords that don't appear in wordlists are crucial. However, systematic hash cracking can still succeed with persistence.

✅ **Multi-layered Security**: This challenge demonstrates the importance of implementing multiple security controls at each layer of an application.

✅ **Rate Limiting**: Without proper rate limiting on login forms, brute force attacks can succeed quickly.

✅ **Principle of Least Privilege**: Users should only have the minimum permissions necessary to perform their duties.

---

## 🔗 Related Resources

- **TryHackMe**: [Brute-It Room](https://tryhackme.com/room/bruteit)
- **OWASP**: Top 10 Web Application Security Risks
- **Kali Linux Tools**:
  - Nmap (Port scanning)
  - ffuf (Web fuzzing)
  - ssh2john & John the Ripper (Hash cracking)

---

*This writeup demonstrates a complete penetration testing methodology, from initial reconnaissance through exploitation, privilege escalation, and post-exploitation activities. All techniques shown here are performed ethically within the bounds of the TryHackMe legal training environment.*

**Happy Hacking! 🛡️**
