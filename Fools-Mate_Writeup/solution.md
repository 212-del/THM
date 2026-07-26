<div align="center">

<img src="https://cdn-images.tryhackme.com/room-icons/62ff64c3c859dc0042b2b9f6-1782993874301" alt="TryHackMe Logo" width="220"/>

<br/>

[![TryHackMe](https://img.shields.io/badge/TryHackMe-AgentT-red?style=for-the-badge&logo=tryhackme&logoColor=white)](https://tryhackme.com)
[![Difficulty](https://img.shields.io/badge/Difficulty-Easy-brightgreen?style=for-the-badge&logo=shield&logoColor=white)]()
[![Technique](https://img.shields.io/badge/Technique-RCE%20%7C%20PHP%208.1.0--dev-blue?style=for-the-badge&logo=php&logoColor=white)]()

</div>

---

## Fools Mate — Full Walkthrough

> *"Something seems a little off with the server…"*
> — and it really was.

---

## Step 1 -- Lookup

Here is our first look of the target

![img](https://miro.medium.com/v2/resize:fit:700/1*cqp0cVuwQksGS2UaZybD9Q.png)

When doing checkmating the king by elephant we got this 

![checkmate](https://miro.medium.com/v2/resize:fit:700/1*oaiu4eD9iV1DuwX3dH-NMA.png)


And here is our nmap result

```
Nmap scan report for 10.49.169.139
Host is up (0.062s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 9.6p1 Ubuntu 3ubuntu13.16 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 ca:11:3d:1f:ed:7e:cc:d7:dd:de:24:55:1b:8d:8e:59 (ECDSA)
|_  256 57:23:7f:6e:41:7a:5a:14:29:e5:b2:d5:19:a6:72:ed (ED25519)
80/tcp open  http    Node.js Express framework
|_http-title: Endgame Trainer
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
```
