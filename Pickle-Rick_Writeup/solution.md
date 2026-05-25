So first thing first after getting the ip i opened up into the browser and i got this

That is  

![homepage.png](homepage.png)

now we are going to do directory bruteforcing to get some hint regarding the password.

But before it we will lookup for the source code in the hope it could somehow help.

and yup we got the username that is 

```html
  <!--

    Note to self, remember username!

    Username: R1ckRul3s

  -->
```

Now we know the username we need to know the endpoint which is needed in login 

Now the directroy bruteforcing is crutial.

In directory bruteforcing there was a robots.txt too which contains this

```text
Wubbalubbadubdub
```

And we are able to find the login endpoint with it it was

- /login.php

The text inside the robots.txt was the password and we are able to get the login at the edpoint /lgoin.php with the extracted username and password.

And there is a panel which lets us execute the commands when i did whoami i got _whoami_ i got _www-data_ in replly.

Meaning command execution is working the endpoint for it was 
- /portal.php

And it was in-build.

so i did ls -a and i was able to get the contents they were


- .
- ..
- Sup3rS3cretPickl3Ingred.txt
- assets
- clue.txt
- denied.php
- index.html
- login.php
- portal.php
- robots.txt

but doing it access and read content with the same endpiont with cat clue.txt gave me the custom error

![error.png](error.png)

Instead i tried directly accesssing the file as robots.txt is in same directory and can be accessed directly using the url

http://IP/robots.txt

Similarly i tried to access the Sup3rS3cretPickl3Ingred.txt by 

http://IP/Sup3rS3cretPickl3Ingred.txt

And i was able to get the content that was the answer of q1.

Now after accessing the file clue.txt By the url

http://IP/clue.txt

And the content inside it was 

```text
Look around the file system for the other ingredient.
```

So i tried looking around after opening the page denied.php i got

![denied.php](rick.png)

Now after a lot of here and there on the server when i was on the portal endpoint everytime i used cat,vim or nano. i was getting the error

![rick.png](rick.png)

So i tried executing what program is actuallly installed on that system with the command 

ls /usr/bin

And i was able to get a lot of entries in which cat was also there i fed the entire list to ai and said to spot that prgram that is able to read the content of a file.

It spot the less command.

which was successfullly reading the content of the file.

which i tested for the file 

less clue.txt

And i get succeed in readig the content you can view the poc below

![poc.png](poc.png)

now we can walk around in the filesystem and be able to read anyfile as per our wish.

but when i did finding which directory i am in since id  was www-data

so obviuls i should be inside /var/html/www

And our assumption was correct as i did pwd.

And then to seach around in filesystem i tried to change the directroy to the root with cd /

But after execuring it when i checked pwd i was still at /var/html/www

Meaninng the cd was disabled too as like the cat.

But since we can't go there but we could peek there.

so i tried to peek inside with the command 

ls  -lah /

And i was able to read the content.


so i tried to go more deep i peek inside the /home there was a folder rick so  i entered there too there was a file named second ingredients

so i tried reading the file with 

less /home/rick/second\ ingredients

And i was able to get the answer of 2nd quesiton.
