So after grabbing the ip i went straight to the browser and entered http://10.48.186.40/

And note it has also told us gave us a python file by download button on the room page.

The content of it is 
```bash
import random
import socketserver 
import socket, os
import string

flag = open('flag.txt','r').read().strip()

def send_message(server, message):
    enc = message.encode()
    server.send(enc)

def setup(server, key):
    flag = 'THM{thisisafakeflag}' 
    xored = ""

    for i in range(0,len(flag)):
        xored += chr(ord(flag[i]) ^ ord(key[i%len(key)]))

    hex_encoded = xored.encode().hex()
    return hex_encoded

def start(server):
    res = ''.join(random.choices(string.ascii_letters + string.digits, k=5))
    key = str(res)
    hex_encoded = setup(server, key)
    send_message(server, "This XOR encoded text has flag 1: " + hex_encoded + "\n")
    
    send_message(server,"What is the encryption key? ")
    key_answer = server.recv(4096).decode().strip()

    try:
        if key_answer == key:
            send_message(server, "Congrats! That is the correct key! Here is flag 2: " + flag + "\n")
            server.close()
        else:
            send_message(server, 'Close but no cigar' + "\n")
            server.close()
    except:
        send_message(server, "Something went wrong. Please try again. :)\n")
        server.close()

class RequestHandler(socketserver.BaseRequestHandler):
    def handle(self):
        start(self.request)

if __name__ == '__main__':
    socketserver.ThreadingTCPServer.allow_reuse_address = True
    server = socketserver.ThreadingTCPServer(('0.0.0.0', 1337), RequestHandler)
    server.serve_forever()
```

But since it is no any webserver running so no website.

So our next attepmt will be the obvious nmap scan with the command 

```bash
nmap -sV -A -O 10.48.186.40
```

The output was 
```bash
Starting Nmap 7.98 ( https://nmap.org ) at 2026-04-05 22:11 +0530
Nmap scan report for 10.48.186.40
Host is up (0.078s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 0c:3d:b1:a2:44:6d:6b:66:8f:24:4c:92:05:5a:83:be (RSA)
|   256 40:bc:0f:d9:73:3c:7a:ab:9f:e0:48:c2:88:94:a2:17 (ECDSA)
|_  256 59:a9:93:61:d0:e9:0c:b8:d4:c4:e1:a8:f2:d7:eb:b4 (ED25519)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.98%E=4%D=4/5%OT=22%CT=1%CU=36256%PV=Y%DS=3%DC=T%G=Y%TM=69D290BA
OS:%P=x86_64-pc-linux-gnu)SEQ(SP=101%GCD=1%ISR=10D%TI=Z%CI=Z%II=I%TS=A)SEQ(
OS:SP=105%GCD=1%ISR=10B%TI=Z%CI=Z%II=I%TS=A)SEQ(SP=107%GCD=1%ISR=10C%TI=Z%C
OS:I=Z%II=I%TS=A)SEQ(SP=109%GCD=1%ISR=104%TI=Z%CI=Z%TS=C)SEQ(SP=FA%GCD=1%IS
OS:R=110%TI=Z%CI=Z%II=I%TS=A)OPS(O1=M4E8ST11NW7%O2=M4E8ST11NW7%O3=M4E8NNT11
OS:NW7%O4=M4E8ST11NW7%O5=M4E8ST11NW7%O6=M4E8ST11)WIN(W1=F4B3%W2=F4B3%W3=F4B
OS:3%W4=F4B3%W5=F4B3%W6=F4B3)ECN(R=Y%DF=Y%T=40%W=F507%O=M4E8NNSNW7%CC=Y%Q=)
OS:T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=
OS:0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T
OS:6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+
OS:%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK
OS:=G%RUD=G)IE(R=Y%DFI=N%T=40%CD=S)

Network Distance: 3 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 80/tcp)
HOP RTT      ADDRESS
1   80.81 ms 192.168.128.1
2   ...
3   80.95 ms 10.48.186.40

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 21.92 seconds

```
As the nmap is saying there is only 1 port opened that is of ssh.

next as we see the room Description we can see it say 
```bash
The server is listening on port 1337 via TCP. You can connect to it using Netcat or any other tool you prefer.
```

So we are going to connect to using nc
```bash
nc 10.48.186.40  1337
```

Now it is asking for the encryption key
```bash
This XOR encoded text has flag 1: 20393a4e2845101b5b2c310903742c0045145e3b351f050639183d0e5d0d06050e052d0609384725
What is the encryption key?
```

And each time i am doing the command nc 10.48.186.40 1337 the XOR encoded text changes.

The reason for is clearly stated in the file that we downloaded from the room page. But here comes our LLM claude we are going to use it for the whole conclution of the file.

Whole conclution of the file is 
```
📄 What This File Is:
A Python-based TCP server designed for a CTF (Capture The Flag) challenge, hosted on port 1337.

⚙️ How It Works:
1. Server Setup
The file uses Python's socketserver module to run a multi-threaded TCP server that listens on port 1337 and handles each connection independently.
2. On Every Connection:

It generates a random 5-character key (letters + digits) fresh each session
It XOR-encodes a hardcoded fake flag (THM{thisisafakeflag}) using that key
Sends the result as a hex-encoded string to the user
Then asks the user to guess the key

3. Two Outcomes:

✅ Correct key → server reveals Flag 2 (read from flag.txt on the server)
❌ Wrong key → connection closes with "Close but no cigar"


🎯 Purpose of This Room:
This room is testing your understanding of:

XOR encryption logic
Known plaintext attacks
Basic network interaction via nc
```

When we look deep into the file it is XOR encoding a fixed text *THM{thisisafakeflag}* with a key and encrypted text being showed  and that key is being asked when i run
```bash
nc 10.48.186.40 1337

```

So the magic of XOR enconding that if 
```
text_to_encode+ key =(encoding with xor)= Encoded text
```
So the magic is that it can be reverserd to get the key
```
Encoded text + text_to_encode =(encoding with xor)= key
```
As we can see in the downloaded file that the string(THM{thisisafakeflag}) is not true. Because the hex output that we get each time after running the commadn nc <room_ip> 1337. 

We will got a different XOR encoded text. 

The XOR Encoded Text : -
 Encoded Text lenght  : - 80 char
 Encoded Text byte    : - 40

The PlainText
 Plaintext Lenght : 20 char
 PlainText Byte   : 20

Here, PlainText Byte != Encoded Text Byte.

That Means Our assumed PlainText is incorrect.

As We can see in the downloaded file that the encoded text is encoded from flag 1 and after submitting the key we will get the flag2.

Now here Our first objective will be to get the key.

Remember the Earlier logic of XOR

```bash
Plaintext:   H   E   L   L   O
Key:         K   E   Y   K   E
             ↓   ↓   ↓   ↓   ↓
XOR →        C1  C2  C3  C4  C5   (cipher)

Decrypt:
Cipher:      C1  C2  C3  C4  C5
Key:         K   E   Y   K   E
             ↓   ↓   ↓   ↓   ↓
Plaintext →  H   E   L   L   O

To get the key:
Cipher:      C1  C2  C3  C4  C5
Plaintext:   H   E   L   L   O
             ↓   ↓   ↓   ↓   ↓
Key →        K   E   Y   K   E


```
As we can see in *To get the key section* that 
 What we have known is
 1. Cipher (Whole Cipher Known.)
 2. Plaintext(Since Plaintext is flag1 and tryhackme flag often starts with THM{ )

As we can see in *Decrypt Section* that
 What we have known is
  1. Cipher (Whole Cipher Known.)
  2. Key    (Nothing Known)

We are going to chain both section to get the key.

Small Note: In any hex encoded text 2 char = 1 byte = 1 orginal ASCII char.
Our encoded text is also further encoded into hex encoding.

See in *To get the key* section. We know the plaintext partially.
So we will do only partial xoring only with partial known plaintext.
Partial Known PlainText = "THM{"(Since Every THM flag starts with "THM{")

Now We know each char of encoded text if xored with each char of Plaintext We will get some partial key too.

Since 2 char of encoded text(already hex encoded) = 1 char of original xor encoded char.
so if 1 xored char again xored  with 1 char of plaintext = 1st char of key
Similarly.
 if 2nd xored char again xored  with 2nd char of plaintext = 2nd char of key
 if 3rd xored char again xored  with 3rd char of plaintext = 3rd char of key
 if 4th xored char again xored  with 4th char of plaintext = 4th char of key
And Thats It we will have only 4 char of plaintext.

We will first extract 4 char of key.

To do so we'll go to cyberchef website.

There we will choose xor from left plance to extract the key.

Now in the page only section which supports hex is the field key. So there we put first 8 char of encoded text(already hex encoded) which will be equal  to original 4 char of xor encoded text.

Then we do xor on THM{ and put THM{ in the input field and get the key.

explain these

Now the moment of truth decode the whole line using hex decoder and then xor decode the whole hex using the partial key of lenght 4 with key format utf8 and guess the 5th char by guessing the key when you guess the 5th key you will get the flag 1 and submit the key and got flag2
