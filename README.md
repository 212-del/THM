# TryHackMe — Writeup Repository

> **A comprehensive, structured collection of walkthroughs, notes, and solutions for TryHackMe rooms and challenges.**

---

## 🛡️ About This Repository

Welcome to my personal TryHackMe (THM) writeup repository. This is a living, ever-growing knowledge base that documents my journey through the world of ethical hacking, penetration testing, and cybersecurity. Each room covered here represents hours of hands-on practice, research, and problem-solving — translated into structured notes so that others (and my future self) can learn from them.

TryHackMe is one of the most widely used platforms for learning offensive and defensive security skills in a gamified, browser-based environment. Rooms range from absolute beginner-friendly introductions to highly complex, real-world simulated penetration tests. This repository captures my progress across all of those difficulty levels, providing:

- **Step-by-step walkthroughs** for completed rooms
- **Explanations of key concepts** encountered along the way
- **Tool references and command cheatsheets**
- **Personal notes on what worked, what didn't, and why**

---

## 🎯 Purpose & Goals

The primary motivation behind maintaining this repository is multi-faceted:

1. **Knowledge Retention** — Writing down solutions forces a deeper understanding of the material. Passive learning fades; active documentation sticks.
2. **Community Contribution** — The cybersecurity community thrives on shared knowledges. These writeups are intended to be a resource for fellow learners who may be stuck or looking for alternative approaches.
3. **Portfolio Building** — Demonstrating practical, hands-on skills is essential in the cybersecurity field. This repository serves as tangible evidence of consistent practice and growth.
4. **Reference Material** — Over time, specific techniques, exploit patterns, and tool usage accumulate into a personal reference library that accelerates future work.

---

## 🗺️ Journey Overview

This writeup journey begins at the very foundation — understanding networking, Linux, basic scripting, and web technologies — and progressively advances through:

- **Pre-Security & SOC Fundamentals** — Networking basics, how the web works, Linux command-line proficiency, and security operations center essentials.
- **Jr. Penetration Tester Path** — Introduction to ethical hacking methodology, enumeration, exploitation, privilege escalation, and post-exploitation.
- **Web Application Security** — OWASP Top 10, SQL injection, XSS, SSRF, authentication bypass, and API security.
- **Network Security & Forensics** — Packet analysis with Wireshark, intrusion detection, log analysis, and incident response.
- **Active Directory & Windows Security** — AD enumeration, Kerberoasting, Pass-the-Hash, BloodHound, and lateral movement techniques.
- **Capture The Flag (CTF) Challenges** — Mixed-difficulty rooms that combine multiple attack vectors into single, cohesive scenarios.
- **Advanced Topics** — Malware analysis, reverse engineering, exploit development, and red team operations.

---

## 📁 Repository Structure

As writeups are added, the repository will be organised into themed folders mirroring the TryHackMe learning paths and standalone room categories:

```
THM/
├── README.md                   ← You are here
├── beginner/                   ← Entry-level rooms and introductory paths
├── web/                        ← Web application security rooms
├── network/                    ← Networking and network security rooms
├── linux/                      ← Linux privilege escalation and fundamentals
├── windows/                    ← Windows enumeration, AD, and exploitation
├── ctf/                        ← CTF-style standalone challenge rooms
├── forensics/                  ← Digital forensics and incident response
├── malware-analysis/           ← Malware analysis and reverse engineering
└── advanced/                   ← Advanced red team and exploit dev rooms
```

Each writeup follows a consistent format:

```
Room Name/
├── README.md       ← Full walkthrough with explanations
├── notes.md        ← Rough notes, commands, and snippets
└── screenshots/    ← Supporting visual evidence (where applicable)
```

---

## 🔧 Tools & Technologies Covered

Throughout this journey, a wide array of industry-standard tools are encountered and documented:

| Category | Tools |
|---|---|
| **Reconnaissance** | Nmap, Gobuster, Dirb, ffuf, theHarvester, Shodan |
| **Web Exploitation** | Burp Suite, SQLMap, Nikto, WPScan, OWASP ZAP |
| **Password Attacks** | Hydra, John the Ripper, Hashcat, CrackMapExec |
| **Exploitation** | Metasploit Framework, ExploitDB, manual CVE exploitation |
| **Post-Exploitation** | LinPEAS, WinPEAS, PowerSploit, Mimikatz, BloodHound |
| **Network Analysis** | Wireshark, tcpdump, Nessus, Netcat |
| **Scripting** | Bash, Python, PowerShell |
| **Forensics** | Autopsy, Volatility, FTK Imager, Binwalk |

---

## 📖 How to Use This Repository

Whether you are a complete beginner or an experienced practitioner looking for a second perspective, here is how to get the most out of these writeups:

1. **Try the room yourself first.** The best way to learn is by doing. Use these writeups as a guide only after you have made a genuine attempt. Even partial progress is valuable.
2. **Read the explanations, not just the commands.** Every writeup aims to explain *why* a technique works, not just *how* to execute it. Understanding the underlying concept is what makes the skill transferable.
3. **Follow along in a live environment.** TryHackMe provides browser-based attack machines, so you can replicate every step in real time.
4. **Take your own notes.** Adapt the material to your own learning style. Fork this repository if you want to build on top of it.

---

## ⚠️ Disclaimer

All content in this repository is strictly for **educational purposes**. Every technique, tool, and exploit documented here was used exclusively within the TryHackMe legal and controlled environment. Applying any of these techniques against systems, networks, or applications without explicit written authorisation is **illegal** and **unethical**.

> *"With great power comes great responsibility."* — Practise ethically, always.

---

## 🚀 Getting Started

If you are new to TryHackMe and want to follow along:

1. Create a free account at [tryhackme.com](https://tryhackme.com)
2. Start with the **"Pre-Security"** learning path to build a solid foundation
3. Progress to the **"Jr. Penetration Tester"** path once you are comfortable with the basics
4. Use this repository as a supplemental reference when you get stuck or want to compare approaches

---

## 📈 Progress Tracker

| Learning Path | Status |
|---|---|
| Pre-Security | 🔄 In Progress |
| Jr. Penetration Tester | 🔄 In Progress |
| Web Fundamentals | 🔄 In Progress |
| SOC Level 1 | ⏳ Planned |
| Offensive Pentesting | ⏳ Planned |
| Red Teaming | ⏳ Planned |

> This tracker is updated regularly as rooms and paths are completed.

---

## 🤝 Contributing & Feedback

This repository is primarily a personal learning log, but feedback, corrections, and suggestions are always welcome. If you spot an error in a walkthrough, have a more elegant solution, or want to recommend a room worth adding — feel free to open an issue.

---

## 📬 Connect

Follow this journey and connect with the cybersecurity community:

- 🌐 TryHackMe Profile: [tryhackme.com](https://tryhackme.com/p/LinuxX)
- 💻 GitHub: [github.com/212-del](https://github.com/212-del)

---

*Happy Hacking — ethically, legally, and relentlessly.* 🖥️🔐
