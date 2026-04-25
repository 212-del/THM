<div align="center">

<img src="https://assets.tryhackme.com/img/THMlogo.png" alt="TryHackMe Logo" width="220"/>

<br/>

[![TryHackMe](https://img.shields.io/badge/TryHackMe-AgentT-red?style=for-the-badge&logo=tryhackme&logoColor=white)](https://tryhackme.com)
[![Difficulty](https://img.shields.io/badge/Difficulty-Easy-brightgreen?style=for-the-badge&logo=shield&logoColor=white)]()
[![Technique](https://img.shields.io/badge/Technique-RCE%20%7C%20PHP%208.1.0--dev-blue?style=for-the-badge&logo=php&logoColor=white)]()

</div>

---

## 🕵️ AgentT — Full Walkthrough

> *"Something seems a little off with the server…"*
> — and it really was.

---

## 🔍 Step 1 — Initial Reconnaissance

After deploying the machine and navigating to the target IP in the browser, we were greeted with a fairly plain admin dashboard — nothing immediately screaming "exploit me", but that's rarely how it works.

![Homepage](homepage.png)

A quick peek at the page source didn't reveal much beyond a handful of domain references, so the next logical step was to run a proper port scan.

```bash
nmap -sV -sC 10.48.148.100
```

**Nmap output:**

```
Starting Nmap 7.98 ( https://nmap.org ) at 2026-04-25 23:01 +0530
Nmap scan report for 10.48.148.100
Host is up (0.063s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
80/tcp open  http    PHP cli server 5.5 or later (PHP 8.1.0-dev)
|_http-title:  Admin Dashboard
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.19
Network Distance: 3 hops
```

That version banner — **PHP 8.1.0-dev** — is the key detail here. This is a known backdoored development build of PHP, and it's a well-documented Remote Code Execution (RCE) vulnerability. Any experienced eye would spot that immediately.

---

## 💥 Step 2 — Finding & Deploying the Exploit

A quick search on Exploit-DB confirmed the suspicion:

```bash
searchsploit -s PHP 8.1.0-dev
```

![Exploit Search](exploit.png)

The exploit (EDB-ID: **49933**) was right there. This backdoor was accidentally introduced into the PHP source tree in early 2021 — it responds to a crafted `User-Agentt` HTTP header and executes arbitrary PHP code on the server. Hence the room name — **AgentT**.

Copied it locally:

```bash
searchsploit -m 49933
```

Ran the script, which prompted for the full target URL:

```
Enter the full host URL (e.g. http://10.x.x.x): http://10.48.148.100
```

---

## 🐚 Step 3 — Getting a Shell

Within seconds of providing the URL, we had a pseudo-shell on the target machine.

![Shell Access](shell.png)

A quick identity check confirmed we were running as **root** — which is both impressive and a good reminder of why running a backdoored PHP binary in production is catastrophic. The environment was restricted though; navigating the filesystem freely wasn't possible, and most directories were off-limits. Only the current working directory and a limited set of paths were readable.

---

## 🚩 Q1 — What is the flag?

Time to hunt for the flag. Since regular directory traversal wasn't cooperating, the cleanest approach was to search the entire filesystem for any `.txt` files:

```bash
find / -type f -name "*.txt" 2>/dev/null
```

One of the results pointed directly to `flag.txt`. Reading its contents revealed the flag — mission accomplished. 🎉

---

<div align="center">

---

### ✅ Room Completed

[![TryHackMe](https://img.shields.io/badge/TryHackMe-Completed-success?style=for-the-badge&logo=tryhackme&logoColor=white)](https://tryhackme.com)

*Made with ❤️ and a lot of late-night terminal sessions.*

</div>
