<div align="center">

<img src="https://assets.tryhackme.com/img/logo/tryhackme_logo_full.svg" alt="TryHackMe Logo" width="260"/>

<br/><br/>

<img src="https://media.giphy.com/media/RbDKaczqWovIugyJmW/giphy.gif" width="200" alt="Hacking Animation"/>

<br/><br/>

# 🌐 Dig-Dug — DNS Flag Retrieval

### *TryHackMe Room Writeup — DNS Enumeration & Custom DNS Server Querying*

<br/>

[![Room](https://img.shields.io/badge/TryHackMe-Dig--Dug-red?style=for-the-badge&logo=tryhackme&logoColor=white)](https://tryhackme.com/room/digdug)
[![Difficulty](https://img.shields.io/badge/Difficulty-Easy-brightgreen?style=for-the-badge)]()
[![Category](https://img.shields.io/badge/Category-DNS%20%7C%20Reconnaissance-blue?style=for-the-badge&logo=cloudflare&logoColor=white)]()
[![Tool](https://img.shields.io/badge/Tool-dig-orange?style=for-the-badge&logo=linux&logoColor=white)]()

</div>


---


## 📋 Room Overview

This room, **Dig-Dug**, is a neat little puzzle that throws you straight into the world of DNS. The machine deployed in this room is **not a web server** — it is a custom **DNS server** that only responds to a very specific query. Your mission is to use DNS enumeration tools, specifically `dig`, to extract a hidden flag that the server has stored as a DNS record.

At first glance, opening the machine's IP in a browser seems like the natural first step — after all, that's what most rooms require. But this one is different. No web page, no HTTP service, just DNS.

> 🔎 *This room is a great reminder that not everything on a target machine runs HTTP. Always enumerate all services, not just web ports.*

---

## 🔍 Initial Recon — What's Going On?

After deploying the machine and getting its IP address, the first instinct is to punch the IP into the browser. Unsurprisingly, it doesn't load anything. No page, no service, nothing. The browser just times out.

That is perfectly fine. The room description tells us exactly what is happening:

> *"Turns out, this machine is also a DNS server! If we could dig into it, I am sure we could find some interesting records! But... it seems weird, this only responds to a special type of request for a `givemetheflag.com` domain?"*

So the machine is **purposely running as a DNS server**, not a web server. It will not respond to HTTP requests, but it absolutely will respond to DNS queries — provided we ask it the right question.

---

## 🧠 Understanding DNS — From the Ground Up



### 🌍 What is DNS?

**DNS (Domain Name System)** is often called the *phonebook of the internet*. It is the system that translates human-readable domain names (like `google.com`) into machine-readable IP addresses (like `142.250.64.100`). Without DNS, we would have to memorise the IP address of every single website we want to visit.

The internet is built on IP addresses. When you type `tryhackme.com` into your browser, your computer does not magically know where that server lives — it has to *ask* something. That "something" is a **DNS resolver**.

### 🏗️ How DNS Works — Layer by Layer

DNS is a hierarchical, distributed database. Here's the full chain of events that happen when you type a domain name into your browser:

```
You type: google.com
         │
         ▼
[1] Your OS checks its local cache
         │ (not found)
         ▼
[2] Queries your ISP's Recursive Resolver
         │ (not cached)
         ▼
[3] Recursive Resolver queries a Root Name Server (.)
         │ "Who handles .com?"
         ▼
[4] Root Server replies: "Ask a .com TLD Name Server"
         │
         ▼
[5] Recursive Resolver queries .com TLD Name Server
         │ "Who handles google.com?"
         ▼
[6] TLD Server replies: "Ask Google's Authoritative Name Server"
         │
         ▼
[7] Recursive Resolver queries Google's Authoritative DNS
         │ "What is the IP for google.com?"
         ▼
[8] Authoritative DNS replies: 142.250.64.100
         │
         ▼
[9] Browser connects to 142.250.64.100 ✅
```

Every step in this chain involves a **DNS server** of some kind. The key player relevant to this room is the **Authoritative DNS Server** — this is the server that actually *holds* the DNS records for a domain and answers definitively.

### 📦 What are DNS Records?

DNS is not just about mapping domain names to IPs. It supports multiple record types, each serving a different purpose:

| Record Type | Purpose | Example |
|:---|:---|:---|
| **A** | Maps a hostname to an IPv4 address | `google.com → 142.250.64.100` |
| **AAAA** | Maps a hostname to an IPv6 address | `google.com → 2607:f8b0::` |
| **MX** | Mail exchange — where to send emails | `gmail.com → mail.google.com` |
| **CNAME** | Alias — points one domain to another | `www.google.com → google.com` |
| **TXT** | Arbitrary text data — used for SPF, DKIM, flags 😉 | `"v=spf1 include:..."` |
| **NS** | Name Server — delegates DNS for a domain | `google.com → ns1.google.com` |
| **SOA** | Start of Authority — metadata about the zone | administrative info |

In this room, the flag is stored inside a **TXT record** (or another special record type) under the `givemetheflag.com` domain — but only this specific custom DNS server holds that record. The public internet has no idea about it.

### 🤔 Why is the TryHackMe IP Acting as a DNS Server?

This is the key insight. The target machine has been configured with **DNS server software** (something like BIND9 or a custom resolver) that has a **custom DNS zone** loaded for the `givemetheflag.com` domain. This zone contains the flag as a DNS record.

Think of it like this: someone has set up a completely private, self-contained DNS server that says, *"I am the authority for `givemetheflag.com`, and I have some records stored for it."* The server does not serve any web pages. It does not run SSH or FTP. Its one and only job is to answer DNS queries.

This is actually a very realistic setup — organisations often run **internal DNS servers** that handle private hostnames that are not visible on the public internet. Penetration testers frequently encounter internal DNS servers that hold sensitive hostnames, internal IP mappings, and sometimes even credentials or tokens embedded in TXT records.

Here, TryHackMe has used that exact concept to hide the flag. The flag lives in a DNS record, and the only way to retrieve it is to query that specific DNS server directly.

---

## 🛠️ The Tool — What is `dig`?

### 🔧 Overview

`dig` stands for **Domain Information Groper**. It is a command-line DNS lookup utility that lets you manually query DNS servers and inspect their responses in detail. It is one of the most fundamental tools in any network administrator's or penetration tester's toolkit.

`dig` is installed by default on most Linux distributions and is available on the TryHackMe AttackBox.

### 📐 Basic Syntax

```bash
dig [options] [@server] [domain] [record-type]
```

| Part | Meaning |
|:---|:---|
| `@server` | Specify *which* DNS server to query (instead of your default resolver) |
| `domain` | The domain name you want to look up |
| `record-type` | The type of DNS record to request (A, TXT, MX, etc.) |

### 🎯 The Key Flag — `@server`

Normally, when you run `dig google.com`, `dig` sends the query to your **system's default DNS resolver** (usually your ISP's or a public one like `8.8.8.8`). This is fine for regular lookups.

But in this room, we do not want to ask our default resolver — because it has no idea about `givemetheflag.com`. We want to ask the **target machine** directly, since it is the authoritative DNS server for that domain.

This is exactly what the `@server` syntax is for: it forces `dig` to send the query to a specific DNS server instead of the system default.

---

## ⚙️ Solving the Room — Step by Step

### 🔑 What We Know Going In

- The target machine's IP address (obtained after deploying the room)
- The domain name: `givemetheflag.com` (stated directly in the room description)
- The machine is running a DNS server, not a web server

### 🚀 The Command

```bash
dig @<target-ip> givemetheflag.com
```

Replace `<target-ip>` with the actual IP assigned to your deployed machine (e.g., `10.10.x.x`).

> 💡 *You can also try specifying a record type explicitly, such as `dig @<target-ip> givemetheflag.com ANY` or `dig @<target-ip> givemetheflag.com TXT`, to retrieve all available records or just text records.*

---

## ❓ Q1 — Retrieve the Flag from the DNS Server!

> 🏴 *No hints provided — the room wants you to figure it out yourself.*

### 🖥️ Running the Command

Open a terminal on the TryHackMe AttackBox (or your own machine connected via OpenVPN) and run:

```bash
dig @<target-ip> givemetheflag.com
```

### 📤 Expected Output

When the command runs successfully, `dig` returns a structured output that looks something like this:

```
; <<>> DiG 9.18.x <<>> @10.10.x.x givemetheflag.com
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 12345
;; flags: qr aa rd; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0

;; QUESTION SECTION:
;givemetheflag.com.             IN      A

;; ANSWER SECTION:
givemetheflag.com.      0       IN      A       flag{xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx}

;; Query time: 2 msec
;; SERVER: 10.10.x.x#53(10.10.x.x)
;; WHEN: Mon Apr 28 16:00:00 UTC 2026
;; MSG SIZE  rcvd: 87
```

### 🏁 The Flag

The flag is embedded inside the **ANSWER SECTION** of the `dig` output. It follows the standard TryHackMe flag format:

```
flag{xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx}
```

Copy the flag exactly as it appears in the ANSWER SECTION and submit it on the room's challenge page. ✅

---

## 🔬 Why Did `dig` Work? — A Deep Dive


This is the most educational part of the room. Let's trace exactly why this worked from the ground up.

### 1️⃣ DNS Operates on Port 53

DNS is a network service that runs on **port 53**, using **UDP** by default (and TCP for larger responses or zone transfers). When the target machine was set up, it started a DNS server process listening on port 53 for incoming queries.

When we run `dig @<target-ip> givemetheflag.com`, `dig`:
1. Constructs a **DNS query packet** (a binary message following the RFC 1035 DNS protocol format)
2. Sends that packet over **UDP to port 53** on the target IP
3. Waits for a **DNS response packet** from the server
4. Parses and displays the response in a human-readable format

### 2️⃣ The `@` Syntax Bypasses the Normal DNS Chain

Under normal circumstances, DNS queries flow through the resolution chain (your OS → ISP resolver → root servers → TLD servers → authoritative server). The `@<target-ip>` syntax short-circuits that entire chain and sends the query **directly** to the specified server as if it were the authoritative authority.

This is essential here because:
- The `givemetheflag.com` domain does not exist on the public internet
- No public DNS server knows about it
- Only the target machine holds the records for that domain
- Therefore, we must query the target machine directly

### 3️⃣ The Target Machine Is an Authoritative DNS Server

The target machine has been configured as an **authoritative DNS server** for the `givemetheflag.com` zone. An authoritative server does not ask anyone else — it *is* the definitive source of truth for that domain.

When our `dig` query arrives at port 53 on the target:
1. The DNS server software checks its **zone files** (flat-text databases of DNS records)
2. It finds a matching record for `givemetheflag.com`
3. It constructs a response packet with the flag embedded in the record data
4. It sends that response back to our `dig` client

### 4️⃣ Flags Hidden in DNS Records — Why This Happens in Real CTFs (and Real Life!)

Hiding data inside DNS records is not just a CTF trick. In real penetration testing engagements, DNS records are a goldmine of sensitive information:

- **TXT records** often contain API keys, verification tokens, internal infrastructure details, or misconfigured SPF/DKIM entries that reveal email infrastructure
- **A records** can map internal hostnames that expose internal network architecture
- **CNAME records** can point to abandoned cloud services, creating subdomain takeover vulnerabilities
- **Zone transfers** (`dig @server domain AXFR`) can dump the entire DNS zone if misconfigured, exposing every hostname in the domain

In this room, the CTF author used the DNS infrastructure itself as the "safe" — and `dig` is the combination that cracks it.

### 5️⃣ The Anatomy of a `dig` Response

Let's break down what `dig` actually returns:

```
;; ->>HEADER<<-
```
This is the DNS message header. The `status: NOERROR` field tells us the query was successful. Other statuses you might see include `NXDOMAIN` (domain doesn't exist) or `SERVFAIL` (the server encountered an error).

```
;; QUESTION SECTION:
```
This echoes back the query we sent — what domain we asked for and what record type.

```
;; ANSWER SECTION:
```
This is the gold. This section contains the records the server returned. The structure of each record is:
```
<name>  <TTL>  IN  <type>  <data>
```
- `name` — the domain name
- `TTL` — Time to Live (how long this record can be cached, in seconds)
- `IN` — Internet class (almost always IN)
- `type` — record type (A, TXT, MX, etc.)
- `data` — the actual record value (IP, text, etc.)

In this room, the `data` field of the returned record contains the flag. That's all there is to it.

---

## 📚 Lessons Learned & Key Takeaways


This room packs a surprising amount of real-world knowledge into a very short challenge. Here's what to walk away with:

| 💡 Lesson | 🧠 Why It Matters |
|:---|:---|
| Not every target runs HTTP | Enumeration should always check all ports and services, not just 80/443 |
| DNS is a data store, not just a lookup service | TXT records, zone files, and AXFR transfers can hold sensitive information |
| `dig @server domain` queries a specific DNS server directly | Essential skill for both CTF and real pen testing |
| Authoritative DNS servers hold the ground truth | Understanding the DNS hierarchy helps you know where to look |
| DNS runs on port 53 (UDP/TCP) | Know your standard ports — they define where tools and queries should be directed |

---

## 🔗 Further Reading

Want to go deeper into DNS? Here are some excellent resources (some are also linked from the room itself):

- 🔵 [DNS in Detail — TryHackMe](https://tryhackme.com/room/dnsindetail) — A thorough, guided introduction to the DNS protocol
- 🔵 [Passive Reconnaissance — TryHackMe](https://tryhackme.com/room/passiverecon) — How DNS enumeration fits into the recon phase
- 🔵 [DNS Manipulation — TryHackMe](https://tryhackme.com/room/dnsmanipulation) — Advanced DNS-based attacks and techniques
- 📖 [RFC 1035 — DNS Protocol Specification](https://datatracker.ietf.org/doc/html/rfc1035) — The official specification for DNS (dense but definitive)

---

<div align="center">

[![TryHackMe](https://img.shields.io/badge/TryHackMe-LinuxX-red?style=for-the-badge&logo=tryhackme&logoColor=white)](https://tryhackme.com/p/LinuxX)
[![GitHub](https://img.shields.io/badge/GitHub-212--del-black?style=for-the-badge&logo=github&logoColor=white)](https://github.com/212-del)

<br/>

<div align="center">

<img src="https://media.giphy.com/media/077i6AULCXc0FKTj9s/giphy.gif" width="80" alt="Learning"/>

</div>

*Made with ❤️ and a lot of DNS packets.*

[![forthebadge](https://forthebadge.com/images/badges/built-with-love.svg)](https://github.com/212-del/THM)

</div>
