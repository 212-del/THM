After getting the ip and opening it into the new page this was our first look of the target

![image](homepage.png)

the nmap result was 

```
Starting Nmap 7.98 ( https://nmap.org ) at 2026-04-26 14:32 +0530
Nmap scan report for olympus.thm (10.48.164.66)
Host is up (0.062s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 24:c9:a1:2a:a4:64:90:7f:7b:ce:7c:98:e3:18:aa:f8 (RSA)
|   256 99:84:13:0f:8f:df:7c:b7:f3:df:64:24:b0:48:fc:31 (ECDSA)
|_  256 ec:db:7d:9b:e8:9d:a1:35:35:1d:2c:0e:bc:79:62:48 (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Olympus
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.98%E=4%D=4/26%OT=22%CT=1%CU=43846%PV=Y%DS=3%DC=T%G=Y%TM=69EDD4D
OS:C%P=x86_64-pc-linux-gnu)SEQ(SP=102%GCD=1%ISR=10D%TI=Z%CI=Z%II=I%TS=A)SEQ
OS:(SP=103%GCD=1%ISR=10C%TI=Z%CI=Z%TS=A)SEQ(SP=104%GCD=1%ISR=107%TI=Z%CI=Z%
OS:II=I%TS=A)SEQ(SP=104%GCD=1%ISR=10E%TI=Z%CI=Z%II=I%TS=A)SEQ(SP=105%GCD=2%
OS:ISR=10A%TI=Z%CI=Z%TS=A)OPS(O1=M4E8ST11NW7%O2=M4E8ST11NW7%O3=M4E8NNT11NW7
OS:%O4=M4E8ST11NW7%O5=M4E8ST11NW7%O6=M4E8ST11)WIN(W1=F4B3%W2=F4B3%W3=F4B3%W
OS:4=F4B3%W5=F4B3%W6=F4B3)ECN(R=Y%DF=Y%T=40%W=F507%O=M4E8NNSNW7%CC=Y%Q=)T1(
OS:R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S
OS:=A%A=Z%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R
OS:=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=
OS:AR%O=%RD=0%Q=)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%
OS:RUD=G)IE(R=Y%DFI=N%T=40%CD=S)

Network Distance: 3 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```
After doing a directory enumuration with the command 

```
ffuf -w /home/Seclists/Discovery/Web-Content/common.txt -u http://olympus.thm/FUZZ -ac
```

there are the endpoints

![endpoints](endpoints.png)

When entering into the page http://olympus.thm/~webmaster

There is this page and this page alone give me many page.

![page](page.png)

here are the endpoints that i found from that page


- http://olympus.thm/~webmaster/index.php
- http://olympus.thm/~webmaster/post.php?post=1 > not found
- http://olympus.thm/~webmaster/post.php?post=2 > not found
- http://olympus.thm/~webmaster/post.php?post=3 > not found
- http://olympus.thm/~webmaster/post.php?post=6 > not found
- http://olympus.thm/~webmaster/category.php?cat_id=1 > a page
- http://olympus.thm/~webmaster/category.php?cat_id=2 > a page
- http://olympus.thm/~webmaster/category.php?cat_id=3 > a page
- http://olympus.thm/~webmaster/category.php?cat_id=7 > a page
- http://olympus.thm/~webmaster/category.php?cat_id=1 > a page

And from above link in any page there was not a single newpage.

There is 2 more page under the same endpoint

- http://olympus.thm/~webmaster/admin > got redirected
- http://olympus.thm/~webmaster/register.php > not found

viewing source code of it gave us few more links

Here are the links inside it

- http://olympus.thm/~webmaster/admin/js/tinymce/tinymce.min.js
- http://olympus.thm/~webmaster/admin/js/tinymce/script.js
- http://olympus.thm/~webmaster/img/vimeo.png
- https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css
- http://olympus.thm/~webmaster/css/cms-home.css
- http://olympus.thm/~webmaster/css/material-icons.css
- http://olympus.thm/~webmaster/admin/font-awesome/css/font-awesome.min.css
- http://olympus.thm/~webmaster/index.php
- http://olympus.thm/~webmaster/admin
- http://olympus.thm/~webmaster/register.php
- http://olympus.thm/~webmaster/post.php?post=2
- http://olympus.thm/~webmaster/#
- http://olympus.thm/~webmaster/img/img.jpg
- http://olympus.thm/~webmaster/post.php?post=3
- http://olympus.thm/~webmaster/img/61X1U2-xUTL.jpg
- http://olympus.thm/~webmaster/post.php?post=6
- http://olympus.thm/~webmaster/img/
- http://olympus.thm/~webmaster/category.php?cat_id=1
- http://olympus.thm/~webmaster/category.php?cat_id=2
- http://olympus.thm/~webmaster/category.php?cat_id=3
- http://olympus.thm/~webmaster/category.php?cat_id=7
- http://olympus.thm/~webmaster/category.php?cat_id=8
- http://olympus.thm/~webmaster/js/jquery.js
- http://olympus.thm/~webmaster/js/bootstrap.min.js

since going to /admin was not possible but from source code i was able to find something inside the admin endpoint.

So when i modified the link to 

__ http://olympus.thm/~webmaster/admin/js/ __

i got a lot of pages

- http://olympus.thm/~webmaster/admin/js/bootstrap.js
- http://olympus.thm/~webmaster/admin/js/bootstrap.min.js
- http://olympus.thm/~webmaster/admin/js/jquery-1.9.1.min.js
- http://olympus.thm/~webmaster/admin/js/jquery.js
- http://olympus.thm/~webmaster/admin/js/npm.js
- http://olympus.thm/~webmaster/admin/js/plugins/
- http://olympus.thm/~webmaster/admin/js/tinymce/

The content of inside 

__http://olympus.thm/~webmaster/admin/js/plugins/ __

- http://olympus.thm/~webmaster/admin/js/plugins/excanvas.min.js
- http://olympus.thm/~webmaster/admin/js/plugins/flot-data.js
- http://olympus.thm/~webmaster/admin/js/plugins/jquery.flot.js
- http://olympus.thm/~webmaster/admin/js/plugins/jquery.flot.pie.js
- http://olympus.thm/~webmaster/admin/js/plugins/jquery.flot.resize.js
- http://olympus.thm/~webmaster/admin/js/plugins/jquery.flot.tooltip.min.js

the content inside are

__ http://olympus.thm/~webmaster/admin/js/tinymce/__

- http://olympus.thm/~webmaster/admin/js/tinymce/morris-data.js
- http://olympus.thm/~webmaster/admin/js/tinymce/morris.js
- http://olympus.thm/~webmaster/admin/js/tinymce/morris.min.js
- http://olympus.thm/~webmaster/admin/js/tinymce/raphael.min.js

So there were all inside admin/js

Lets get onto now admin/font-awesome/

- http://olympus.thm/~webmaster/admin/font-awesome/HELP-US-OUT.txt
- http://olympus.thm/~webmaster/admin/font-awesome/fonts/
- http://olympus.thm/~webmaster/admin/font-awesome/scss/

now there are not much more interesting files under fonts and scss but since i though HELP-US-OUT.txt is useful and i saw its content was

```text
I hope you love Font Awesome. If you've found it useful, please do me a favor and check out my latest project,
Fort Awesome (https://fortawesome.com). It makes it easy to put the perfect icons on your website. Choose from our awesome,
comprehensive icon sets or copy and paste your own.

Please. Check it out.

-Dave Gandy
```

But i think its a advertisement.

Here are some few more endpoints under ~webmaster/

![web](webmaster1.png)
