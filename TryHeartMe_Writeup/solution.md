as it is told in the room page already that machine was accessible at http://10.49.181.116:5000

When i went at that page i saw

![homepage_look](homepage_look.png)


the task of this room was find a way to purchase the hidden item.

Now tappable items are signup login and some products.

The source code was also not helpful.

Then i go to signup and make a account and signup with it. after the login we can see credits is equal to zero.


![credits](credits.png)

Now there is something interesting that when we are loggeed in with the user account and when we are opening the product page it is showing us a role.

![role](role.png)

Now as it is showing role we can try to change role from the inspect tab.

When i did visited to application tab of inspect tab then cookies there was  a jwt token there we will try to modify the token and lets see waht happens. 

Previously this token was 

tryheartme_jwt:eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJlbWFpbCI6Imd1ZXN0QGdtYWlsLmNvbSIsInJvbGUiOiJ1c2VyIiwiY3JlZGl0cyI6MCwiaWF0IjoxNzc2NTgyODI5LCJ0aGVtZSI6InZhbGVudGluZSJ9.V1qBFtAwOhBWuPFutzpATGT8SN7V0xjCDprbc2JgHmI


This jwt token contains this data 

```
{
  "email": "guest@gmail.com",
  "role": "user",
  "credits": 0,
  "iat": 1776582829,
  "theme": "valentine"
}
```


and this header 

```
{
  "alg": "HS256",
  "typ": "JWT"
}
```

And using the same data but with role=admin i made a another jwt token and replaced the current jwt token with the fresly made.


```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJlbWFpbCI6Imd1ZXN0QGdtYWlsLmNvbSIsInJvbGUiOiJhZG1pbiIsImNyZWRpdHMiOjAsImlhdCI6MTc3NjU4MjgyOSwidGhlbWUiOiJ2YWxlbnRpbmUifQ.b7Uza8q9taClisYffV6ehTwwKDCVJHbMi12cGMpuXts
```

Now as i put this jwt token in the inspect tab and refrested the tab it gave me the admin acess of the page now i am signed in a admin. and i have token 5000.

![admin](admin.png)

As i goto the homepage of the site now where the product are listed i could see that hidden product 

![hidden](hidden.png)

Now as we will tap onto this product and hit buy it gave us the flag.
