<div align="center">

<img src="https://tryhackme.com/_next/image?url=https%3A%2F%2Fcdn-images.tryhackme.com%2Froom-icons%2F016dea7c96e8b422241016405b571c8b.jpeg&w=96&q=75" alt="TryHackMe Logo" width="220"/>

<br/>

[![TryHackMe](https://img.shields.io/badge/TryHackMe-Tomcat-red?style=for-the-badge&logo=tryhackme&logoColor=white)](https://tryhackme.com)
[![Difficulty](https://img.shields.io/badge/Difficulty-Easy-brightgreen?style=for-the-badge&logo=shield&logoColor=white)]()
[![Technique](https://img.shields.io/badge/Technique--blue?style=for-the-badge&logo=php&logoColor=white)]()

</div>

---

## 🕵️ Agent-Sudo — Full Walkthrough

> *"Something seems a little off with the server…"*
> — and it really was.

---

## Step 1 -- Lookup


Here is the nmap output

```
PORT     STATE SERVICE    VERSION
22/tcp   open  ssh        OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 f3:c8:9f:0b:6a:c5:fe:95:54:0b:e9:e3:ba:93:db:7c (RSA)
|   256 dd:1a:09:f5:99:63:a3:43:0d:2d:90:d8:e3:e1:1f:b9 (ECDSA)
|_  256 48:d1:30:1b:38:6c:c6:53:ea:30:81:80:5d:0c:f1:05 (ED25519)
53/tcp   open  tcpwrapped
8009/tcp open  ajp13      Apache Jserv (Protocol v1.3)
| ajp-methods: 
|_  Supported methods: GET HEAD POST OPTIONS
8080/tcp open  http       Apache Tomcat 9.0.30
|_http-title: Apache Tomcat/9.0.30
|_http-favicon: Apache Tomcat
```

Now we have 4 services

- ssh
- tcpwrapped
- ajp13
- http


Here when searched the version number
