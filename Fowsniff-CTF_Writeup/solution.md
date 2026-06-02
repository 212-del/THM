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
