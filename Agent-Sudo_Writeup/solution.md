<div align="center">

<img src="https://miro.medium.com/v2/resize:fit:640/format:webp/1*uPrmx2XcdkUuQCZ-T3J4Kg.png" alt="TryHackMe Logo" width="220"/>

<br/>

[![TryHackMe](https://img.shields.io/badge/TryHackMe-AgentT-red?style=for-the-badge&logo=tryhackme&logoColor=white)](https://tryhackme.com)
[![Difficulty](https://img.shields.io/badge/Difficulty-Easy-brightgreen?style=for-the-badge&logo=shield&logoColor=white)]()
[![Technique](https://img.shields.io/badge/Technique-RCE%20%7C%20PHP%208.1.0--dev-blue?style=for-the-badge&logo=php&logoColor=white)]()

</div>

---

## 🕵️ Agent-Sudo — Full Walkthrough

> *"Something seems a little off with the server…"*
> — and it really was.

---

## Step 1 -- Lookup

So after opening the ip in the browser here is the first look of our target!

![look](https://miro.medium.com/v2/resize:fit:640/format:webp/1*NPumNJ_ELFgfmIRsqx2GIA.png)

Now it time for the First Questions.


It goes like 

| Quesiton | Hints
|------|--------------|
| How many open ports are there | Use Nmap |


For this here is the simple loolipoop nmap command to scan for open ports.


> nmap --top-ports 1000 <Ip of room>


## Step 2 -- Initial Foothold

Lets Went to our second Question that is 

| Quesiton | Hints |
|------|-------------|
| How Do you redirect Yourself to the secret Page? | Nothing |

Since we need a link that will somehow redirecting us to the secret page 


We just need to find that page(endpoint).

for this we are going to start directory enumuration.


But in our case the directory enumuration tool dirbuster didn't gave anything.



so we need to change our approch.

As it is being said in homepage of target is 

```
Dear agents,

Use your own codename as user-agent to access the site.

From,
Agent R
```

And to answer that question We need to use user agent with the value R

With this command below

> curl -v  -H "User-Agent: R" http://10.49.177.146

Now when we did it we got something special text that is 

```
What are you doing! Are you one of the 25 employees? If not, I going to report this incident
```

And since whatever that program is saying i made a small script that will loop with all a to z as the value of user-agent.

here is the mini script

```
for file in $(echo {A..Z}); do                          
echo "for $file"
 curl -s  -H "User-Agent: $file" http://10.49.177.146| wc -c | grep -v 218
done
```

218 is the length for the wrong response size.

but changing the user-agent gave us a new page this means that new page could be a secret page.


But why did we get this secret page what helped us to get this secret page that is user-agent.

so our answer for this question will be user-agent.


Lets Move on to our third question to solve this room.

It is asking what is the agent name?

for this i thought with the value of user-agent = R the endpoint will be different so i run a directory bruteforcing with the user-agent value = R


But we dont find any valuable endpoint.


After this we found a way to change the user-agent using the inspect tab.

with this trick when i chnaged the user-agent to C i got something unexpected.


This response was not given even with this command 

> curl -v  -H "User-Agent: C" http://10.48.188.99


Now the trick to change the user-agent via the inspect tab is

Ctrl + Shift + I

then 

Ctrl + Shift + P

Then enter "Show Network Conditions"

Now here you can change the value of user-agent.

when changed the user-agent i got the the response

```
Attention chris,

Do you still remember our deal? Please tell agent J about the stuff ASAP. Also, change your god damn password, is weak!

From,
Agent R
```

it worked by browser method cuz with curl i didn't enabaled the redirect via the flag -L.


: << END_NOTE

These are my notes.
You can write multiple lines here.
Bash ignores everything until END_NOTE.

The Setup (What You Were Testing)
You were poking at a web security lab where:

Changing the User-Agent in Chrome's DevTools (via Network Conditions) and reloading made the browser land on a completely different endpoint.
Hitting that new endpoint directly with curl worked fine and gave the expected response.
But copying the exact same request the browser made (via "Copy as cURL") and running it in the terminal did not reproduce the browser's behavior — it gave back the normal/plain response instead.

This is a great instinct to test, by the way — mismatched browser-vs-curl behavior is exactly how people discover UA-based routing, hidden redirects, and access-control bugs in the wild.
The Investigation (What We Ruled Out Together)
Think of it like a detective story where we crossed suspects off the list one by one:

JavaScript redirect? ❌ Ruled out — you disabled JS in DevTools and the redirect still happened.
Service worker intercepting the request? ❌ Ruled out — you checked the Application tab, no service worker registered.
Client Hints / Sec-CH-UA headers not matching? — Possible, but not confirmed as the cause here.
Cookies/session state set on a prior request? — Possible, but not confirmed as the cause here.
An HTML <meta http-equiv="refresh"> tag sitting in the page that curl just doesn't act on? — This was my strongest guess, since it explains "no JS, no service worker, but still auto-navigates."

The Actual Answer
It turned out to be much simpler than any of those five theories: you weren't following redirects with curl.
Here's the "aha" in plain terms — imagine the server sends back a letter that says "Thanks! Now go to this other address instead." That's an HTTP redirect (like a 301 or 302 status code with a Location: header).

A browser is like a person who reads that letter and automatically walks to the new address without you telling it to. This is just normal browser behavior — it always follows redirects unless told not to.
curl, by default, is like someone who reads the letter, sees the instruction, and just stops and hands you the letter — it does not walk to the new address on its own. You have to explicitly tell curl "hey, if you get an instruction to go somewhere else, follow it" — and that's exactly what the -L flag does (-L = "follow Location headers").

So the sequence was:

You change the UA in the browser → reload.
Server sees the new UA and responds with a redirect (e.g., 302 Found, Location: /new-endpoint).
Browser automatically follows that → lands on /new-endpoint → shows the expected content.
When you ran plain curl (without -L) with the same UA, curl got that same 302 redirect response back — but just displayed it as-is (or showed nothing useful) instead of following it, since you never told it to.
Once you added -L, curl started behaving like the browser: it saw the redirect and automatically followed it to the new endpoint — and boom, matching result.

What You Learned
This is a genuinely useful lesson for security testing, so let's nail it down clearly:

curl does not follow redirects by default. You must add -L (or --location) if you want it to behave like a real browser when a server responds with a 3xx status code and a Location header.
"Copy as cURL" from DevTools also does not add -L automatically. So even a perfectly copied browser request will not auto-follow redirects unless you add the flag yourself. This is a common trap — people assume "Copy as cURL" gives them 100% browser-equivalent behavior, but it doesn't include this one crucial piece.
To debug UA-based routing/redirect logic properly, always check the status code first (curl -v shows this clearly) before assuming something JS-related or cookie-related is going on. A 301/302/303/307/308 status with a Location: header is the simplest and most common explanation — and it's easy to overlook because curl "silently" doesn't act on it unless asked.

Quick reference for next time you test something like this:
bashcurl -v -H "User-Agent: <ua>" http://target        # -v shows you the status code and headers, so you can SEE the redirect happening
curl -L -H "User-Agent: <ua>" http://target         # -L makes curl follow it, like a browser would
This lines up nicely with your ethical hacking / Red Hat Ethical Hacker background too — UA-based conditional redirects are a real pattern you'll see in bug bounty and pentest work (e.g., mobile-only endpoints, bot-detection bypass pages, or hidden admin panels gated by a specific UA string). Good catch running this test carefully instead of assuming — that's exactly the kind of instinct that catches real bugs.

END_NOTE


## Step 3 -- Some Bypasses

In this question we are asked that 

FTP Password


So we have recently found the username chris

We are going to bruteforce the password this ftp user via the hydra


> hydra -l <target_username> -P <wordlist.txt> ftp://<target_server>:21


After this we got the password 

I logged into the ftp and got these files 

```
-rw-r--r--    1 0        0             217 Oct 29  2019 To_agentJ.txt
-rw-r--r--    1 0        0           33143 Oct 29  2019 cute-alien.jpg
-rw-r--r--    1 0        0           34842 Oct 29  2019 cutie.png
```

Now we extract those files to our local machine and proceeding to our next question that is


Then i checked are they those that they actually look.

with these commands

```
$ file To_agentJ.txt                          
To_agentJ.txt: ASCII text
                                                                             
$ file cutie.png    
cutie.png: PNG image data, 528 x 528, 8-bit colormap, non-interlaced
                                                                             
$ file cute-alien.jpg 
cute-alien.jpg: JPEG image data, JFIF standard 1.01, resolution (DPI), density 96x96, segment length 16, baseline, precision 8, 440x501, components 3

```

And now we're soure that they are actually they that they look.

Since it looks ok but in this part of task there is one another question too that asks for what is the zip password.

But we did't find any zip from python.

We need to look deep into the files.

I did binwalk for all these files but the result of "cutie.jpg" gave me something relevent that is 

```

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             PNG image, 528 x 528, 8-bit colormap, non-interlaced
869           0x365           Zlib compressed data, best compression
34562         0x8702          Zip archive data, encrypted compressed size: 98, uncompressed size: 86, name: To_agentR.txt
34820         0x8804          End of Zip archive, footer length: 22
```

Meaning our zip file  is there we gonna extarct it by 

```
binwalk -e cute-alien.jpg
```

after this i got a folder with named

_cute-alien.jpg

And it gave me 3 files.

``` 365 365.zlib 8702.zip ```

Now the file 8702.zip is password proctected.

For this we're going to break this with this trick.

first going to convert the zip to hash with 

zip2john 8702.zip > zip.hash

Next we're going to break it with john 

``` john --wordlist=/home/Seclists/Passwords/Common-Credentials/xato-net-10-million-passwords-1000000.txt zip.hash 
```

## Step 4 -- Strong Foothold

And we will get the pass.

then when we unzip it with passphrase. i got this context

```
Agent C,

We need to send the picture to 'QXJlYTUx' as soon as possible!

By,
Agent R
```

Now lets come on to our next file that is 365.zlip which contains 2 file which can be verified with this output

```
7z l 365.zlib 

7-Zip 26.02 (x64) : Copyright (c) 1999-2026 Igor Pavlov : 2026-06-25
 64-bit locale=en_US.UTF-8 Threads:2 OPEN_MAX:4096, ASM

Scanning the drive for archives:
1 file, 33973 bytes (34 KiB)

Listing archive: 365.zlib

--
Path = 365.zlib
Type = zip
Offset = 33693
Physical Size = 280

   Date      Time    Attr         Size   Compressed  Name
------------------- ----- ------------ ------------  ------------------------
2019-10-29 17:59:11 .....           86           98  To_agentR.txt
------------------- ----- ------------ ------------  ------------------------
2019-10-29 17:59:11                 86           98  1 files
                                                       
```

What you're seeing is a file that isn't just a ZIP file. It's a larger file (365.zlib, 33,973 bytes) that contains an embedded ZIP archive starting at offset 33693.

This line is the clue:

Type = zip
Offset = 33693
Physical Size = 280

It means:

Total file size: 33973 bytes
ZIP archive starts at byte 33693
ZIP archive size: 280 bytes


Now we are going to seprate both files with the command 

``` dd if=365.zlib of=embedded.zip bs=1 skip=33693 ```
 
and

```dd if=365.zlib of=prefix.bin bs=1 count=33693 ```

After this when we extracted the embedded.zip with the same cracked pass we got the same content that is

```
Agent C,

We need to send the picture to 'QXJlYTUx' as soon as possible!

By,
Agent R
```

after it when i do more reseach on the prefix.bin i got that both the reuslt of 

file 365 and file prefix.bin has same output.

xxd 365 | tail -n 10 and xxd prefix.bin | tail -n 10 has too the same outputs.

it could be verified using this command and its output 

```
 md5sum decoded.bin 365
1e7ac52e2601e6722fda312938ab2c1d  decoded.bin
1e7ac52e2601e6722fda312938ab2c1d  365
```

So here we tried QXJlYTUx as the steghide password of cute_alien.jpg

So here we didn't get any data and said it is false password.

Since the password is base64 encoded we decoded it with base64.

and the result is Area51.

after decrypting the file with that i got a file named messege.txt


And the content within the messege.txt is 


```
Hi james,

Glad you find this message. Your login password is hackerrules!

Don't ask me why the password look cheesy, ask agent R who set this password for you.

Your buddy,
chris
```

Here we loggedin with james and got the user_flag.txt


## Step 5 --
