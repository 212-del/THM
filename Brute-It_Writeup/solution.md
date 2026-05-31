So after getting the ip and opnenign it into the brower

It presented itself with apache2 default page

![img](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh2jXbyL79IpN7aO7Fa1XYpoeJnjz-1ADwUPUkO6rNUwbUP9VDzTwsFb2EouiY4Sn_aWT1I66fJ2yZ4iAuQpyApSRF2QBga4agCEMuNGlIp5OFbJnY5qiNrcEghj0hp0-aeWiG4lnt7AFU/s640/Apache+Default+Page.PNG)

After it i started to 2 most critical stuffs do a directory enumuration(recursive one) and nmap scan.

For directory enumuration endpoints were

- /admin
- /admin/.zip
- /admin/id_rsa

Nmap scan gave us 2 ports opened 

Port 22 And port 80

After doing so we get onto our first quesion that was how many ports are opened
It was 2 the next question was what version of ssh is running.

It was openssh 7.6p1

Next qustion was what version of apache is running. 

It was 2.4.29 

Theser all anserr were got from the nmap scan result.

After it next question was what is hidden endpoint.

It was /admin and when visited to that endpoint we were presented with a lgoin page.

![img](https://miro.medium.com/v2/resize:fit:640/format:webp/0*3D2PYB0UO9YSof__.png)

After this we opened the source code and got this line 

```html
 <!-- Hey john, if you do not remember, the username is admin -->
```

So we are sure there the username is admin here but there is john named username too.


