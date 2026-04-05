A quick Hit in browser http://10.49.185.184.

It gave us a login page.

<main_png>

In the login page it hinted us to do CTRL + U.

Here is the source Code.

<source_page>

As we can see in line 34 we have the credentials of guest.

After login with guest it shows

<def_page>

Consider the URL: http://10.49.185.184/profile.php?user=guest

Since This room is about IDOR. So it is simple nature we will try for parameter tampering.

After trying a bunch of values of user when hit the value=admin. I got the flag. 

Easiest flag ever catched.
