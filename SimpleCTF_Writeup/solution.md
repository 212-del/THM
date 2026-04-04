As the 1st one asks for how many servies are running under port 1000. 

It means it is asking how many open ports are there whose port number are less than 1000.

Lets do a quick Nmap to get the result

<Nmap_res>

We can clearly see how many ports are running whose port number are less than 1000.

This result also gave out answer of 2nd quesiton that What is running on the higher port?

For the next Quesiion it is asking for What's the CVE you're using against the application?

But we still doesn't know what application is runnning on this ip. 

<apache_def>

We can see the default page of apache server seems like no service is running. 

Since Its a webpage we tried to bruteforce endpoints By the command 

```
ffuf -u http://<iP>/FUZZ -w /usr/share/seclists/Discovery/Web-content/big.txt
```

<ffuf_res>

We Got 2 interestign endpoint those are /simple and /robots.txt

<robots_endpoint>

Hitting to robots.txt gave us another endpoint but that is unreachable so thats a clue for us now.

We have now 2 valid Endpoints.

When hitting to the 2nd endpoint it gave us the famous open source software CMS made Simple.

<cms_version>

We can see the version is 2.2.8.as well as we will now have a application.

The question asked next is What's the CVE you're using against the application?

We will now search if there is any exploit is available for this version or not

For the we will use the tool **searchsploit**

```
searchsploit -c cms 2.2.8
```

<searchsploit_res>

We can see clearly that this is vulneable to sqli. and finally we found a vulnerblity but we need a cve of this this search result gave you a path at right plane.

Now to know more about that exploit. We need another commad to know more about that exploit with the help of its path. Make sure to copy that path that you saw in right plane.

We will run below command to know *CVE*
```
searchsploit -x <path>
```
You will got the cve number and that your answer for the Q3.


We will use it with msfconsole. I immediately started msfconsole and serch by the command **seach cms 2.2.8**. 

I got a exploit it and set it by **use <exploit_name>**.

<msf_exploit1>

So this is basically asking for a username and password and it is mandatory. So for now we cant use it now.

Again we will seach by *searchsploit* tool but with another seach term

```
searchsploit  cms made simple 
```

After this command we will get a bunch of result

<msf_bunch>

We are now going to do a perfect search with
```
searchsploit cms made simple 2.2.8
```

We will now get only 1 result with its path we are going to use it by the command
```
seachsploit -m 46635
```
Now it gets copied into your current location.

The syntax to use this exploit is pyhon2 46635.py -u http://<room_ip>/simple

<exploit_use>

Now we have the password hash for the user mitch.

We can try to dehash the password with hashcat.

We are going to use: -
```
hashcat -O -a 0 -m 10 0c01f4468bd75d7a84c7eb73846e8d96:1dac0d92e9fa6bb2 /usr/share/wordlists/rockyou.txt
```
The structure of command is hashcat [options] [options_values] [hash(es):salt] [wordlist/mask]

```
Command explanation: 

1. -O flag

    Optimized kernel mode

    Enables faster cracking

2. -a 0

    Attack mode

    0 = Straight (dictionary) attack
      Hashcat will take each word from the wordlist and try it as a password

3. -m 10

 Hash type

   10 = md5($pass.$salt)

 👉 This means:

 The password is concatenated with the salt
 Then hashed using MD5

 Formula:

  hash = MD5(password + salt)

4. 0c01f4468bd75d7a84c7eb73846e8d96:1dac0d92e9fa6bb2

    This is the hash + salt pair

5. /usr/share/wordlists/rockyou.txt

    This is our wordlist (dictionary)

```

hashcat can also be that it doesn't run on low aspects devices or gives errors.

To dehash we can also use online methods too
