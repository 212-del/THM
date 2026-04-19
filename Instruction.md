<div align="center">

# 📡 TryHackMe — Connection Guide

### *How to Connect to TryHackMe Rooms on Kali Linux*

[![Platform](https://img.shields.io/badge/Platform-TryHackMe-red?style=for-the-badge&logo=tryhackme&logoColor=white)](https://tryhackme.com)
[![OS](https://img.shields.io/badge/Recommended%20OS-Kali%20Linux-557C94?style=for-the-badge&logo=kalilinux&logoColor=white)](https://kali.org)
[![VPN](https://img.shields.io/badge/VPN-OpenVPN-EA7E20?style=for-the-badge&logo=openvpn&logoColor=white)](https://openvpn.net)

</div>

---

> 🗒️ **Note:** These instructions are optimised for **Kali Linux**. The solutions and writeups in this repository work best on Kali Linux, which comes pre-loaded with all the essential penetration testing tools.

---

## 📖 Table of Contents

| # | Section |
|:---:|:---|
| 1 | [🚀 Step 1 — Launch the Room](#-step-1--launch-the-room) |
| 2 | [🔌 Step 2 — Connect to the Room](#-step-2--connect-to-the-room) |
| 3 | [🖥️ Method A — AttackBox (Browser-Based)](#-method-a--attackbox-browser-based) |
| 4 | [💻 Method B — Your Own Machine via OpenVPN](#-method-b--your-own-machine-via-openvpn) |
| 5 | [⚡ Pro Tip — Bulk /etc/hosts Shortcut](#-pro-tip--bulk-etchosts-shortcut) |
| 6 | [🔍 Useful Commands](#-useful-commands) |

---

## 🚀 Step 1 — Launch the Room

1. Log in to your **[TryHackMe account](https://tryhackme.com)**
2. Navigate to the room you want to complete
3. Click the green **"Start Machine"** button to deploy the vulnerable target machine
4. Wait for the machine's **IP address** to appear (usually displayed at the top of the task panel)

> 💡 **Tip:** Copy the IP address — you'll need it in the next steps.

---

## 🔌 Step 2 — Connect to the Room

Once the room machine is running, you have **two ways** to connect and interact with it:

---

## 🖥️ Method A — AttackBox (Browser-Based)

The **easiest method** — no setup required. TryHackMe provides a fully configured virtual machine directly in your browser.

**How to use it:**

1. Click the **"Start AttackBox"** button at the top-right of the room page
2. A browser-based Kali Linux desktop will load within the page
3. All essential tools (Nmap, Gobuster, Metasploit, etc.) are pre-installed
4. The AttackBox is already connected to the room's network — no VPN needed

> ✅ **Best for:** Quick sessions, beginners, or when you don't want to set up a VPN.  
> ⚠️ **Limitation:** Free-tier users have limited AttackBox time per day.

---

## 💻 Method B — Your Own Machine via OpenVPN

Connect your **local Kali Linux machine** directly to the TryHackMe room network using OpenVPN.

### 📋 Prerequisites

- Kali Linux (native install, VM, or WSL)
- OpenVPN installed (`sudo apt install openvpn -y`)
- A TryHackMe account

---

### 🔧 Setup Steps

#### Step 1 — Add the Room IP to `/etc/hosts`

Copy the room's IP address and add it to your hosts file so your machine can resolve it:

```bash
sudo nano /etc/hosts
```

Add the following line at the bottom of the file:

```
<MACHINE_IP>    <room-hostname>.thm
```

Save and exit (`Ctrl+O`, `Enter`, `Ctrl+X`).

> 💡 **Example:** `10.10.123.45    target.thm`

---

#### Step 2 — Download Your TryHackMe VPN Config File

1. Visit **[https://tryhackme.com/access](https://tryhackme.com/access)**
2. Under the **"Machines"** tab, download your personal `.ovpn` config file
3. Save it somewhere memorable — for example:

```
~/Desktop/tryhackme.ovpn
```

---

#### Step 3 — Connect via OpenVPN

Run the following command to connect your machine to the TryHackMe network:

```bash
sudo openvpn ~/Desktop/tryhackme.ovpn
```

Leave this terminal open and running. You are now connected! 🎉

> 🔑 **Important:** Once connected, your machine will communicate with the room through a new virtual network interface called **`tun0`**, assigned a different IP address. The room machine will only be reachable via this interface.

---

#### Step 4 — Verify Your Connection

In a new terminal, run:

```bash
ip a
```

Look for the **`tun0`** interface. Your TryHackMe-assigned IP will be listed there. This is the IP the target machine sees when you interact with it.

> ✅ If `tun0` appears with an IP address, you are successfully connected to the TryHackMe network.

---

## ⚡ Pro Tip — Bulk `/etc/hosts` Shortcut

Manually adding the room IP to `/etc/hosts` every time is tedious. Here's a one-liner that pre-populates your hosts file with all common TryHackMe IP ranges — so you're always ready to go:

```bash
echo 10.49.{1..254}.{1..254} | sed 's/ /\n/g' >> /tmp/thm_ips.txt && \
echo 10.48.{1..254}.{1..254} | sed 's/ /\n/g' >> /tmp/thm_ips.txt && \
sudo bash -c 'cat /tmp/thm_ips.txt >> /etc/hosts'
```

> ⚠️ **Use with caution.** This adds a large number of entries to your hosts file. It is useful for rapid practice but may slow down DNS resolution on your system. Consider cleaning up `/etc/hosts` afterwards.

---

## 🔍 Useful Commands

Here are a few essential commands to keep handy during any TryHackMe room:

| 🛠️ Command | 📋 Purpose |
|:---|:---|
| `ip a` | View all network interfaces and your current IP addresses |
| `ping <MACHINE_IP>` | Verify you can reach the target machine |
| `sudo openvpn <file>.ovpn` | Connect to TryHackMe via VPN |
| `nmap -sV -sC <MACHINE_IP>` | Run a default service/version scan on the target |
| `gobuster dir -u http://<IP> -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt` | Directory enumeration |
| `cat /etc/hosts` | View your current hosts file entries |

---

<div align="center">

*Happy Hacking — ethically, legally, and relentlessly.* 🖥️🔐

[![TryHackMe](https://img.shields.io/badge/TryHackMe-LinuxX-red?style=for-the-badge&logo=tryhackme&logoColor=white)](https://tryhackme.com/p/LinuxX)
[![GitHub](https://img.shields.io/badge/GitHub-212--del-black?style=for-the-badge&logo=github&logoColor=white)](https://github.com/212-del)

</div>
