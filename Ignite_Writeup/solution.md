So this our first look our target after opening the ip into the browser

![ip](homepage.png)

The directory enumuaration reuslt were

- README.md
- robots.txt > /fuel/
- /contibuting.md
- composer.json

When i went to robots.txt and it gave me one more endpoint that is /fuel

When i went to /fuel it redirected me to a login page.

![login](login.png)

After getting the login page we tried

Bruteforcing the page but there was rate limiting so we left it

And if keep scrooling the homepage to the bottom we have got the credentials to login.

After loggedin here is our first look.

![logged](loggedin.png)

As i search on internet for fuel cms 1.4 vuln i got this

![fuelcms](vuln.png)

And when i do reseach about that specific cve i got it could lead to rce.

It was https://www.exploit-db.com/exploits/50477

Its usage is just download it and run it with

python3 <filename> -u <target_url>

As you do so you will get the shell.

Now for the answer of question1.

As it is asked what is user.txt

The answer is contained in a file named /home/www-data/flag.txt

