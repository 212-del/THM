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

We can see the version is 2.2.8.

We will now search if there is any exploit is available for this version or not

For the we will use the tool **searchsploit**

```
searchsploit -c cms 2.2.8
```

<searchsploit_res>

We can see clearly that this is vulneable to sqli.

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


