After getting the IP, at first after opening the IP in the browser as http://10.49.148.79:5000

![def_page](def_page.png)

The page did not seem like a normal page, no more complex page, so I opened the source code

![source_code](source_code.png)

But as I can see there is no interesting info in the page source.

But since we couldn't spot any endpoint we will do fuzzing now with the command
```bash
ffuf -u http://10.49.148.79:5000/FUZZ -w /path/to/wordlist 
```

The results were below:

![ffuf_res](ffuf_res.png)

As we can see the useful result is /robots.txt

![robots_file](robots_file.png)

Now we can see there is a secret endpoint. 

When we go to that endpoint we are not reached yet.

![secret_page](secret_page.png)

Here we tried fuzzing on that hidden endpoint with the command.
```bash
ffuf -u http://10.49.148.79:5000/cupids_secret_vault/FUZZ -w /path/to/wordlist
```

The result was another endpoint

![ffuf_res2](ffuf_res2.png)

When I go to that endpoint I got a login page

![login_page](login_page.png)

And since its endpoint is /administrator it is obvious for the administrator.

So I tried logging in as admin and the password — that passphrase that I got at the bottom of robots.txt

🎉 Boom!! We got the flag.
