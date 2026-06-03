# 🎯 Fowsniff-CTF | Room Overview & Walkthrough Guide

---

<div align="center">

## 🐦 Room Name: **Fowsniff CTF** 

![Difficulty Badge](https://img.shields.io/badge/Difficulty-Easy-brightgreen?style=for-the-badge)
![Category Badge](https://img.shields.io/badge/Category-Boot2Root-blue?style=for-the-badge)

</div>

---

## 📝 Room Description

Hack this machine and get the flag. There are lots of hints along the way and it's perfect for beginners!

This boot2root machine is excellent for newcomers. You will need to:
- Enumerate the machine by finding open ports
- Conduct online research (it's amazing how much information Google can provide)
- Decode hashes and credentials
- Brute-force POP3 login attempts
- And much more!

This walkthrough is structured to guide you step by step through what you need to accomplish. Make sure you are [connected to the TryHackMe network](https://tryhackme.com/access).

**Credit to [berzerk0](https://twitter.com/berzerk0)** for creating this machine. *This machine is used here with the explicit permission of the creator* ❤️

---

## ❓ Room Questions & Tasks

| # | Question | 💡 Hint |
|:---:|:---|:---|
| **Q1** | Deploy the machine. On the top right of this you will see a Deploy button. Click on this to deploy the machine into the cloud. Wait a minute for it to become live. | N/A |
| **Q2** | 🔍 **Using nmap, scan this machine. What ports are open?** | `nmap -A -p- -sV MACHINE_IP` |
| **Q3** | 🕵️ **Using the information from the open ports. Look around. What can you find?** | Check web servers, mail services, SSH banners |
| **Q4** | 🔎 **Using Google, can you find any public information about them?** | Check Pastebin, GitHub (berzerk0/Fowsniff), or Wayback Machine |
| **Q5** | 🔐 **Can you decode these MD5 hashes?** | Use hashkiller, Crackstation, or online hash lookup services |
| **Q6** | ⚔️ **Using the usernames and passwords you captured, can you use Metasploit to brute force the POP3 login?** | Use `auxiliary/scanner/pop3/pop3_login` in Metasploit or Hydra |
| **Q7** | 📧 **What was Seina's password to the email service?** | Brute-force results will reveal this |
| **Q8** | 💌 **Can you connect to the POP3 service with her credentials? What email information can you gather?** | Use `nc <ip> 110` to access POP3 |
| **Q9** | 🔑 **Looking through her emails, what was a temporary password set for her?** | Check email content from admin |
| **Q10** | 👤 **In the email, who sent it? Using the password from the previous question and the sender's username, connect to the machine using SSH.** | SSH into the system with admin credentials |
| **Q11** | 👥 **Once connected, what groups does this user belong to? Are there any interesting files that can be run by that group?** | Check `id` and `ls -la` in group-writable directories. Look for `cube.sh` |
| **Q12** | ⚙️ **Now you have found a file that can be edited by the group, can you edit it to include a reverse shell?** | Add reverse shell to the editable file |
| **Q13** | 🚀 **This file is run as root when a user connects via SSH (banner/MOTD). Include it in `/etc/update-motd.d/` to execute as root** | Edit `/etc/update-motd.d/` to trigger your reverse shell |
| **Q14** | 🏆 **Start a netcat listener (`nc -lvp 1234`) and then re-login to SSH. You will receive a reverse shell as root!** | `nc -lvp 1234` on your machine, then SSH to target |
| **Q15** | 📚 **Stuck? There's a brilliant walkthrough available** | [HackingArticles Fowsniff Walkthrough](https://www.hackingarticles.in/fowsniff-1-vulnhub-walkthrough/) |

---

## 🎓 Key Learning Objectives

By completing this room, you will master:

✅ **Port Scanning & Enumeration**
- Using Nmap to discover open services
- Identifying service versions and vulnerabilities

✅ **OSINT & Information Gathering**
- Social media reconnaissance
- Public database searches and archives
- Credential leaks and data breaches

✅ **Cryptography & Hash Cracking**
- MD5 hash identification
- Online hash lookup services
- Rainbow tables and hash crackers

✅ **Password Attacks**
- Brute-force methodology
- Email service exploitation
- Credential reuse detection

✅ **Email Protocol Exploitation**
- POP3 protocol basics
- Email extraction via Netcat
- Information disclosure via email

✅ **Privilege Escalation**
- Group-based privilege escalation
- Cron job and MOTD exploitation
- Reverse shell execution

---

## 🛠️ Tools You'll Use

| Tool | Purpose |
|:---|:---|
| **Nmap** | Port scanning and service enumeration |
| **curl/wget** | Web reconnaissance |
| **Metasploit/Hydra** | POP3 brute-forcing |
| **Netcat (nc)** | Email retrieval, reverse shells |
| **SSH** | Remote system access |
| **Hashcat/Crackstation** | Hash cracking |
| **Text editors** | Privilege escalation via file editing |

---

## 💡 Pro Tips

🎯 **Reconnaissance is Key** — Spend time enumerating. The more information you gather, the easier exploitation becomes.

🔍 **Google is Your Friend** — Real-world penetration testing often involves OSINT. Learn to search effectively.

🔐 **Hash Cracking Takes Time** — Some hashes crack instantly with online services, others require wordlists or computational power.

📧 **Protocol Understanding** — Understanding POP3 and email protocols helps you extract maximum value from compromised accounts.

🚀 **Privilege Escalation** — The final steps often rely on group permissions and scheduled tasks. Always check `/etc/update-motd.d/` and cron jobs.

---

## ✅ Room Completion Checklist

- [ ] Deploy the machine successfully
- [ ] Identify 4 open ports using Nmap
- [ ] Find public information about Fowsniff Corp
- [ ] Crack MD5 hashes from the breach data
- [ ] Brute-force POP3 service and find Seina's credentials
- [ ] Extract email contents
- [ ] Find the temporary SSH password
- [ ] SSH into the system as the admin user
- [ ] Identify group-writable files (cube.sh)
- [ ] Edit cube.sh to add a reverse shell
- [ ] Configure MOTD to execute cube.sh as root
- [ ] Receive root shell via reverse connection
- [ ] Capture the final flag
- [ ] Complete and submit the room

---

<div align="center">

### 🏆 **Ready to Hack?**

Good luck! Remember: *Patience, persistence, and proper documentation are key to successful penetration testing.*

**[View Full Solution Walkthrough →](./solution.md)**

🔐 *Happy hacking, ethically and legally!*

</div>
