As after starting the machine and after getting the ip this is our target look

![target Look](https://miro.medium.com/v2/resize:fit:720/format:webp/1*KWyHAloxIdVGjmskuuvdVA.png)

we read the task 1 its content is below

## Task 1

This boot2root machine is brilliant for new starters. You will have to enumerate this machine by finding open ports, do some online research (its amazing how much information Google can find for you), decoding hashes, brute forcing a pop3 login and much more!

This will be structured to go through what you need to do, step by step. Make sure you are [connected to our network](https://tryhackme.com/access)

Credit to [berzerk0](https://twitter.com/berzerk0) for creating this machine. **This machine is used here with the explicit permission of the creator <3**


Now as we proceed we are asked how many ports are open so to answer it we need to the namp scan and our namp scan gives this result

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

So the answer of first question will be 4 ports open.


Next question was 

```text
Using the information from the open ports. Look around. What can you find?
```

for this we opened the same target with different ports.

with port 110 and 143 we got the below error

![ERR_UNSAFE_PORT](https://static-kb.siteground.com/wp-content/uploads/sites/2/2025/02/err-unsafe-port-chrome-1536x747.jpg)


I think we already that the questio is asking to do that is 

Using the information from the open ports. Look around. What can you find?

because we have already do -A attribute in nmap attack method.

We scanned the target with  nmap -sV -A -O -Pn 10.49.175.51

Now the next question is 

Using Google, can you find any public information about them?

now are going for passive scanning for info we have gathered till now.

We searched for the pop3 version Dovecot pop3d & imap version Dovecot imapd.

But we didn't find anythig useful.

But on the website homepage we could see they stated about a databreach which compomisted their [official twitter account](https://x.com/FowsniffCorp) and attacker said they will release the data on this account.

![Fowsniff Page](https://miro.medium.com/v2/resize:fit:720/format:webp/1*KWyHAloxIdVGjmskuuvdVA.png)

Now after vising the page there are many thing posted on the account.

![Twitter account](https://private-user-images.githubusercontent.com/88498991/369710876-8cc0076b-1fdb-4be5-8ff5-10c7b241feab.png?jwt=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3ODA0MTQwNDQsIm5iZiI6MTc4MDQxMzc0NCwicGF0aCI6Ii84ODQ5ODk5MS8zNjk3MTA4NzYtOGNjMDA3NmItMWZkYi00YmU1LThmZjUtMTBjN2IyNDFmZWFiLnBuZz9YLUFtei1BbGdvcml0aG09QVdTNC1ITUFDLVNIQTI1NiZYLUFtei1DcmVkZW50aWFsPUFLSUFWQ09EWUxTQTUzUFFLNFpBJTJGMjAyNjA2MDIlMkZ1cy1lYXN0LTElMkZzMyUyRmF3czRfcmVxdWVzdCZYLUFtei1EYXRlPTIwMjYwNjAyVDE1MjIyNFomWC1BbXotRXhwaXJlcz0zMDAmWC1BbXotU2lnbmF0dXJlPWI0YWNhMzdlNmUyODU2MzU0ZGNjOTVmNWMyMzk1MTdjODA0YjYyMGU0ZTM5YzU5NTkzYWYyNjY2MGRhYzhjYjEmWC1BbXotU2lnbmVkSGVhZGVycz1ob3N0JnJlc3BvbnNlLWNvbnRlbnQtdHlwZT1pbWFnZSUyRnBuZyJ9._zjk8EDMuAIxpSnzCj0JvRfMnk4Vsj5ICZ4w3k3r9YE)


The links that are in BIO are not working anymore

And all the links that were provided in the posts were also not working too.

These are those non working links

- https://pastebin.com/378rLnGi
- https://pastebin.com/NrAqVeeX

But in the posts section there is one interesting post that is

![interesting post](https://private-user-images.githubusercontent.com/88498991/369711355-26b02afe-1716-44a1-85fd-654d317c04d9.png?jwt=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3ODA0MTQ0MzYsIm5iZiI6MTc4MDQxNDEzNiwicGF0aCI6Ii84ODQ5ODk5MS8zNjk3MTEzNTUtMjZiMDJhZmUtMTcxNi00NGExLTg1ZmQtNjU0ZDMxN2MwNGQ5LnBuZz9YLUFtei1BbGdvcml0aG09QVdTNC1ITUFDLVNIQTI1NiZYLUFtei1DcmVkZW50aWFsPUFLSUFWQ09EWUxTQTUzUFFLNFpBJTJGMjAyNjA2MDIlMkZ1cy1lYXN0LTElMkZzMyUyRmF3czRfcmVxdWVzdCZYLUFtei1EYXRlPTIwMjYwNjAyVDE1Mjg1NlomWC1BbXotRXhwaXJlcz0zMDAmWC1BbXotU2lnbmF0dXJlPTAyYTYxYWYyMDdlNDdkNDlkM2ExNDk5YTljNzE2Y2IzNmUyMWYwYzRiZThlMWJlZmNkNzBiODNhYzJmOGE3ZWEmWC1BbXotU2lnbmVkSGVhZGVycz1ob3N0JnJlc3BvbnNlLWNvbnRlbnQtdHlwZT1pbWFnZSUyRnBuZyJ9.pMb95Abt4v4gFujX7rErCiYeGFdvmw-b9mgZ4Azn0Mg)

This reveals that sysadmin has below credentials

stone@fowsniff:a92b8a29ef1183192e3d35187e0cfabd

When we tried to crack that hash we were not able to crack it.

To crack the hash the room has also intructed us to open a site whihc is not working now

![The site](https://hashkiller.io/listmanager)

Now we're now able to crack the pass so next we need to the belwo task as it told in room info

Using the usernames and passwords you captured, can you use metasploit to brute force the pop3 login?


But in metasploit when i seached for the exploit for pop3 with 

search pop3d i got only 1 exploit and even with that exploit i got


```info
[*] Exploit completed, but no session was created.
```

But when searched for the pop3 only i got try on many auxilary and exploits as showed by seach result i tried each one and failed but one didn't let my hope breaks. 

It was 

use auxiliary/scanner/pop3/pop3_login

Options that we need to set in this are

- RHOSTS
- LHOST
- RPORT
- USER_FILE
- PASS_FILE

So i set the username with this username file of seclists

- /home/Seclists/Usernames/xato-net-10-million-usernames.txt

for password this file

- /home/Seclists/Passwords/Leaked-Databases/rockyou-75.txt

Since msf is slow we are not going to use it.

Hydra is faster so we are going to use it instead 

with this structure

 hydra -L usernames.txt -P passwords.txt pop3://<machine_ip>

Within few minutes we get the id and pass of pop3 

Also the next question is

What was seina's password to the email service?

Which we extracted from the bruteforcing now.

aFTER getting the credentials we logged in with the commadn 

nc <target_ip> 110

then we have to login by the method

- USER seina

then

- PASS REDACTED

Then we will be prompted +OK Logged in. 

if we enter the credentials correct 

now we list all mails with the commadn 

LIST

And we got 2 enteries 

```text
+OK 2 messages:
1 1622
2 1280
.
```

now we saw the messege 1622 with 

RETR 1

The messege is 

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

The temporary password for SSH is "REDACTE"

You MUST change this password as soon as possible, and you will do so under my
guidance. I saw the leak the attacker posted online, and I must say that your
passwords were not very secure.

Come see me in my office at your earliest convenience and we'll set it up.

Thanks,
A.J Stone


.

```

aND IN THIS WAY THE ROOM COMPLETES.
