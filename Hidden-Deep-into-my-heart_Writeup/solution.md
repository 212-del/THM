After getting the ip At first after opening the ip in browser as http://10.49.148.79:5000

<def_page>

The page was not seems a normal page no more complex page so i opened the source code

<source_code>

But as i can see there is no interesting info in page source.

But since can't we spotted any endpoint we will do fuzzing now with the command
```bash
ffuf -u http://10.49.148.79:5000/FUZZ -w /path/to/wordlist 
```

The result were below:

<ffuf_res>

As we can the useful result is /robots.txt

<robots_file>

Now we can see there is secret endpoint. 

When we goto that endpoint we are not reached yet.

<secret_page>

Here we tried for fuzzing on that hidden endpoint with the command.
```bash
ffuf -u http://10.49.148.79:5000/cupids_secret_vault/FUZZ -w /path/to/wordlist
```

The result was another endpoint

<ffuf_res2>

When i go to that endpoint i got a login page

<login_page>

And since its endpoint is /administrator it is obvious for the administrator.

So i tried login as admin and password that that passphrase that i got in the bottom of robots.txt and 

Boom!! We got the flag.
