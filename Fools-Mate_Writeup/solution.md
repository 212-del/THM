<div align="center">

<img src="https://cdn-images.tryhackme.com/room-icons/62ff64c3c859dc0042b2b9f6-1782993874301" alt="TryHackMe Logo" width="220"/>

<br/>

[![TryHackMe](https://img.shields.io/badge/TryHackMe-Fools-Mate-red?style=for-the-badge&logo=tryhackme&logoColor=white)](https://tryhackme.com)
[![Difficulty](https://img.shields.io/badge/Difficulty-Easy-brightgreen?style=for-the-badge&logo=shield&logoColor=white)]()
[![Technique](https://img.shields.io/badge/Technique-Clickjacking-blue?style=for-the-badge&logo=php&logoColor=white)]()

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


Lets undersand the board and position

Here is the board 

![Board](https://user-images.githubusercontent.com/99700157/189538278-7ead0640-6f7a-4939-8ed0-909fdb170996.png)

Here the piece at the bottom left is at the position a1. 
The Piece at the position bottom right is h1. 

Similarly we could understand all positions.

Here is something important stuff i leant that if we look at the request that is captured. 

```REQ Body
POST /api/move HTTP/1.1
Host: 10.48.184.80
Content-Length: 23
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/150.0.0.0 Safari/537.36
Content-Type: application/json
Accept: */*
Sec-GPC: 1
Accept-Language: en-US,en;q=0.5
Origin: http://10.48.184.80
Referer: http://10.48.184.80/
Accept-Encoding: gzip, deflate, br
Cookie: sid=565b7c6c61e0860cf385d48f12e32cd1
Connection: keep-alive

{"from":"a1","to":"a8"}
```

Now In this we are moving the a1 piece. i.e. Elephant

To the postion a8 to checkmate.

In the response i got 

```Res Body

HTTP/1.1 400 Bad Request
X-Powered-By: Express
Content-Type: application/json; charset=utf-8
Content-Length: 80
ETag: W/"50-vbVldvUULxPq+DdD1STJ6cKrwtg"
Date: Mon, 27 Jul 2026 02:21:30 GMT
Connection: keep-alive
Keep-Alive: timeout=5

{"ok":false,"error":"illegal move","fen":"6k1/5ppp/8/8/5PPP/8/R7/6K1 w - - 1 5"}
```

Now we're going to do the same stuff but by changing the approch that is we are going to intercept the req to /api/move in the Burp's Interceptor.


So here we intercept it in the interceptor i got the response 

```RES BODY
HTTP/1.1 200 OK
X-Powered-By: Express
Set-Cookie: sid=037be96afbf97022794449c8712be884; Path=/; HttpOnly; SameSite=Lax
Content-Type: application/json; charset=utf-8
Content-Length: 155
ETag: W/"9b-4UJFSVz7fqu+CivQPQPb7IbmWH0"
Date: Mon, 27 Jul 2026 03:09:37 GMT
Connection: keep-alive
Keep-Alive: timeout=5

{"ok":true,"move":"a1a8","fen":"R5k1/5ppp/8/8/8/8/5PPP/6K1 b - - 1 1","status":"checkmate","turn":"b","winner":"white","flag":"THM{REDACTED}

```

But why i didn't get this in the repeater too. For this when i send the same req again into the repeater then i got the above response.

It was a room glitch.

So here in the response we got the flag.
