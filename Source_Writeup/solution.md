After getting the ip and opening it into the browser gave me the error that it refused to connect

But then i tried to do a namp scan with the below commadn 

```bash
nmap -sV -A -O -Pn 10.49.186.80
```

And what i got from it was 

Starting Nmap 7.99 ( https://nmap.org ) at 2026-05-29 22:10 +0530
Nmap scan report for 10.49.186.80
Host is up (0.068s latency).
Not shown: 998 closed tcp ports (reset)
PORT      STATE SERVICE VERSION
22/tcp    open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 b7:4c:d0:bd:e2:7b:1b:15:72:27:64:56:29:15:ea:23 (RSA)
|   256 b7:85:23:11:4f:44:fa:22:00:8e:40:77:5e:cf:28:7c (ECDSA)
|_  256 a9:fe:4b:82:bf:89:34:59:36:5b:ec:da:c2:d3:95:ce (ED25519)
10000/tcp open  http    MiniServ 1.890 (Webmin httpd)
|_http-title: Site doesn't have a title (text/html; Charset=iso-8859-1).
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.99%E=4%D=5/29%OT=22%CT=1%CU=41328%PV=Y%DS=3%DC=T%G=Y%TM=6A19C1B
OS:8%P=x86_64-pc-linux-gnu)SEQ(TI=Z%CI=Z%TS=D)SEQ(SP=103%GCD=1%ISR=102%TI=Z
OS:%CI=Z%II=I%TS=A)SEQ(SP=103%GCD=1%ISR=104%TI=Z%CI=Z%II=I%TS=A)SEQ(SP=105%
OS:GCD=1%ISR=106%TI=Z%CI=Z%II=I%TS=A)SEQ(SP=106%GCD=2%ISR=10A%TI=Z%CI=Z%II=
OS:I%TS=A)OPS(O1=M4E8ST11NW6%O2=M4E8ST11NW6%O3=M4E8NNT11NW6%O4=M4E8ST11NW6%
OS:O5=M4E8ST11NW6%O6=M4E8ST11)WIN(W1=F4B3%W2=F4B3%W3=F4B3%W4=F4B3%W5=F4B3%W
OS:6=F4B3)ECN(R=Y%DF=Y%T=40%W=F507%O=M4E8NNSNW6%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=
OS:O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD
OS:=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0
OS:%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1
OS:(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI
OS:=N%T=40%CD=S)

Network Distance: 3 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 554/tcp)
HOP RTT      ADDRESS
1   67.45 ms 192.168.128.1
2   ...
3   71.54 ms 10.49.186.80

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 55.03 seconds

Meaningly the port 10000 is open and web service is there so i need to do the focus there insted on the default port 80 that is the reson behing the site refused to connect 

After accessing it at the port 10,000 here is our first look

![img](https://miro.medium.com/v2/resize:fit:720/format:webp/1*vrEeRLx8X8O9xCRHblefIQ.png)


On following the redirection, i got a dns error.

Since i have not mapped that new ip with the url that i have to visit now that why i got this error.


Due to my case i have to map it like this way

 10.49.186.80 ip-10-49-186-80.ap-south-1.compute.internal 

After mapping it when i opened it i got again another error that is 

![here](https://supporthost.com/wp-content/uploads/2022/06/chrome-err-cert-authority-invalid-selfsigned-certificate-1024x496.png)

Then what i need to do was tap on advance and procceed to that site.

After proceedign we are now welcomed with a login page.

![img](https://miro.medium.com/v2/resize:fit:720/format:webp/1*Yb6sl7s_Tlwy8a-zZv4hdQ.png)


As if we saw in the nmap output look at the snippet

10000/tcp open  http    MiniServ 1.890 (Webmin httpd)


Meaning its Webmin is 1.890 we could seach for vuln into it.


