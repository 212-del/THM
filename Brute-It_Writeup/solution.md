So after getting the ip and opnenign it into the brower

It presented itself with apache2 default page

![img](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh2jXbyL79IpN7aO7Fa1XYpoeJnjz-1ADwUPUkO6rNUwbUP9VDzTwsFb2EouiY4Sn_aWT1I66fJ2yZ4iAuQpyApSRF2QBga4agCEMuNGlIp5OFbJnY5qiNrcEghj0hp0-aeWiG4lnt7AFU/s640/Apache+Default+Page.PNG)

After it i started to 2 most critical stuffs do a directory enumuration(recursive one) and nmap scan.

For directory enumuration endpoints were

- /admin
- /admin/.zip
- /admin/panel/id_rsa

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


Since i got the id_rsa for at the location /admin/panel/id_rsa

Then we are able to do the login but as were doing login we are prompted to enter a passphare.

But we dont know the passphrase 

So to do so we need to crack the passphrase.

For it we are going to execute these sequece of commands.

ssh2john <path to key> <path to hashed key>

ssh2john : this is the tool which helps to get the hashed key

After this we need to crack the key for pharase with the below commands 

john --wordlist=/path/to/wordlsit key_hash

But when i did these seq of commands i didn't get the password.

But soon after i changed the wordlist and tried again.

But this too didn't get me passpharse after spending my all long password lists i was frusturated very badly. i then decided to spend my all wordlist to crack the passphrase with the command

With the loop : 

```bash
for file in /home/seclists/password/common-credentials/*.txt; do
john --wordlist=$file hashed_key
done
```

This command finally gives us the passphrase.

With the passphrase i am able to get the login with the command

ssh -i key john@10.49.161.41

And when prompted for the password i entered the password.

Make sure to give key the 600 permision before connecting to ssh using the key.

After it we need to bruteforce the password of here too.

So to do the bruteforcing we need to first inquire that what ffuf req is acceptable

since when we saw the req with curl

I was it was accepting a csrf token.

so for curl we need to use that csrf token too to get our request successful

So i did

```bash
curl -i -L \        
  -c cookies.txt \             
  -b cookies.txt \           
  -d "user=admin&pass=Wrongpass" \
  http://10.49.161.41/admin/ 
```

Then i got the result as error that username or password is invalid.

That means our curl is working we will now create our fuzz as according to this curl.

before doing enumuration we need to first find size of requ with the command

curl -s -L -d "user=admin&pass=wrong" http://10.49.161.41/admin/ | wc -c

After seeing the size of wrong pass we will know how much to filter.

And with this command below we will get the correct password.

```bash
ffuf -u http://10.49.156.209/admin/ \
     -X POST \
     -H "Content-Type: application/x-www-form-urlencoded" \
     -d "user=admin&pass=FUZZ" \
     -w /home/Seclists/Passwords/Leaked-Databases/rockyou-75.txt -fs 733

```
![web interface after logging in](https://miro.medium.com/v2/resize:fit:640/format:webp/0*NhbuSbJwpw0EYBTx.png)


After lgogging in we are now loggedin with the brutefoced password



after login we are welcomed with our web flag.


since we have got that ssh shell now get back to it.


there is a user.txt which is a flag and it is at the location 

/home/john/user.txt which is the anser of the quesiton what is user.txt?


and now its time for the privilage escalation 

since the ssh shell is not as a root shell.

For this we will do sudo -l

And that binary is /bin/cat which can be executed as root without any password

now its time to get the root flag with this.

for this we used

sudo /bin/cat /root/root.txt

And in this way we got our root flag
