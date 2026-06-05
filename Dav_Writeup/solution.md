# Dav Writeup | Full CTF Walkthrough


![Room page](https://www.digitalocean.com/cdn-cgi/image/quality=75/https://www.digitalocean.com/api/static-content/v1/images?src=https%3A%2F%2Fassets.digitalocean.com%2Farticles%2Fhow-to-install-apache-webserver-22.04%2Fapache-default.PNG&raw=1)

## Part 1 Reconnaissance


### Directory Enumuration 


Since the directory enumuration that we're doing now is the recursive one so 

We found one endpoint that was requiring login to proceed the endpoint was

- /webdav

### Nmap

The nmap output was 

```nmap
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
```

Meaning the room is more squuzeed because there is no ssh.


When we proceed to the endpoint /webdav
