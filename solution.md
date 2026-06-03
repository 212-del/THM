As we open the website in the broswer this is our first look to target.

![target](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh2jXbyL79IpN7aO7Fa1XYpoeJnjz-1ADwUPUkO6rNUwbUP9VDzTwsFb2EouiY4Sn_aWT1I66fJ2yZ4iAuQpyApSRF2QBga4agCEMuNGlIp5OFbJnY5qiNrcEghj0hp0-aeWiG4lnt7AFU/s640/Apache+Default+Page.PNG)

After it lets refer to our first quesiton that is is how many ports are opened 

For this we are going to do a port scan with nmap


With this option --top-ports

nmap --top-ports 1000 <Target>

And we found 2 ports were opened.

Now as the next question asks us that what is the ssh version running on that webserver.

for this we are going to make a nmap scan again against the target with 

nmap -sC -sV -A -p22,80 10.48.136.181

It gave me ssh version was 7.6

But we need to submit full answer so i submitted openssh 7.6p1

Next question was what version of apache is running for this  we again refer to our same nmap result and it gave us that  version of apache was apache 2.4.29

And it accped anser as it is 2.4.29

Next question was which distribution of linux is running which was also visible into our scan result

It was 

Device type: general purpose|phone
Running (JUST GUESSING): Linux 4.X|5.X|6.X (96%), Google Android 10.X|11.X|12.X (93%)
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:google:android:10 cpe:/o:google:android:11 cpe:/o:google:android:12 cpe:/o:linux:linux_kernel:6 cpe:/o:linux:linux_kernel:5.4
Aggressive OS guesses: Linux 4.15 - 5.19 (96%), Linux 4.15 (96%), Linux 5.4 - 5.15 (96%), Android 10 - 12 (Linux 4.14 - 4.19) (93%), Linux 5.14 - 6.8 (93%), Android 10 - 11 (Linux 4.9 - 4.14) (92%), Android 12 (Linux 5.4) (92%), Android 9 - 11 (Linux 4.9 - 4.14) (92%), Linux 2.6.32 (92%), Linux 2.6.39 - 3.2 (92%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 3 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

After seeing it i thought it was android but submitted answer got wrong so i tried again and this time i thoght it could be another linux distro so i submitted ubuntu and it got accepted.

As we move on to our next question it asks for me that what is the hidden endpoint that is running onto that 

After a directory enumuraion it got reveled it was /admin and /admin/index.html

So obviously due to the answer format the answer was /admin.

For the next part to answer we need to visit the endpoint /admin.

It was a login page

![lgoin](https://miro.medium.com/v2/resize:fit:640/format:webp/0*3D2PYB0UO9YSof__.png)


We need to bruteforce this page to get the access since it has nothing in source code except the username admin.

This is the exact phrase i got 

```html
<!-- Hey john, if you do not remember, the username is admin -->
```

After this i did directory bruteforcing recursively and i got some new endpoints they were

- /admin/.zip
- /admin/panel/id_rsa

First endpiont gave us the file of homepage into a zip file 

The second endpiont gave us a ssh key as per its name id_rsa

We will now try to login with the key
