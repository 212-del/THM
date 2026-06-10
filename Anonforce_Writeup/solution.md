So after getting  the ip and opening it into the browser here is our first look of the target 

![Target](https://www.dreamhost.com/blog/wp-content/smush-webp/2023/11/google-chrome-err-connection-refused-error-page-1460x997.jpg.webp)

# Part 1 Reconnaissance

## Nmap

When doing nmap we found the below result 

```nmap
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:192.168.246.164
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 1
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| drwxr-xr-x    2 0        0            4096 Aug 11  2019 bin
| drwxr-xr-x    3 0        0            4096 Aug 11  2019 boot
| drwxr-xr-x   17 0        0            3700 Jun 07 17:46 dev
| drwxr-xr-x   85 0        0            4096 Aug 13  2019 etc
| drwxr-xr-x    3 0        0            4096 Aug 11  2019 home
| lrwxrwxrwx    1 0        0              33 Aug 11  2019 initrd.img -> boot/initrd.img-4.4.0-157-generic
| lrwxrwxrwx    1 0        0              33 Aug 11  2019 initrd.img.old -> boot/initrd.img-4.4.0-142-generic
| drwxr-xr-x   19 0        0            4096 Aug 11  2019 lib
| drwxr-xr-x    2 0        0            4096 Aug 11  2019 lib64
| drwx------    2 0        0           16384 Aug 11  2019 lost+found
| drwxr-xr-x    4 0        0            4096 Aug 11  2019 media
| drwxr-xr-x    2 0        0            4096 Feb 26  2019 mnt
| drwxrwxrwx    2 1000     1000         4096 Aug 11  2019 notread [NSE: writeable]
| drwxr-xr-x    2 0        0            4096 Aug 11  2019 opt
| dr-xr-xr-x  101 0        0               0 Jun 07 17:46 proc
| drwx------    3 0        0            4096 Aug 11  2019 root
| drwxr-xr-x   18 0        0             540 Jun 07 17:46 run
| drwxr-xr-x    2 0        0           12288 Aug 11  2019 sbin
| drwxr-xr-x    3 0        0            4096 Aug 11  2019 srv
| dr-xr-xr-x   13 0        0               0 Jun 07 17:46 sys
|_Only 20 shown. Use --script-args ftp-anon.maxlist=-1 to see all.
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 8a:f9:48:3e:11:a1:aa:fc:b7:86:71:d0:2a:f6:24:e7 (RSA)
|   256 73:5d:de:9a:88:6e:64:7a:e1:87:ec:65:ae:11:93:e3 (ECDSA)
|_  256 56:f9:9f:24:f1:52:fc:16:b7:7b:a3:e2:4f:17:b4:ea (ED25519)
```

Since the the port 80 is not opened any no any port that is serving a web. so this room is not about lab anymore.

## Part 2 Initial Foothold


Now let's get onto some nmap services who says ftp is allowed us the anonymous login on / folder.

Now we could read the contents the filesystem.

- When reading inside /opt

We were not permitted as current user to read the contents inside that folder.


- When visiting to /tmp

Nothing important was there.

- When visiting to a interesting directory there were 2 files inside /notread

They were

- backup.pgp 
- private.asc


Now we could get the contents 

- The contents inside the privat.asc was OpenPGP(GPG) private key.

- The contents inside the backup.pgp was the random text when we see what file actually it is with the command file backup.pgp it showed me data.

> It means we need to decode it further to see the file contents.

## Part 3 User.txt flag.

Now when we enumurate a little bit more we found the current /home directory.


There was a user named **melodias**

And inside his file the file user.txt was saved.

## Part 4 Root.txt flag.


While enumurating a bit more we found a another file named .wget_hsts on the home folder of user melodias.

The content inside were 

```content
# HSTS 1.0 Known Hosts database for GNU Wget.
# Edit at your own risk.
# <hostname>[:<port>]   <incl. subdomains>      <created>       <max-age>
gist.githubusercontent.com      0       0       1565570405      31536000
```


Here is the way to crack that passpharse that is required to decrypt the file backup.pgp using the private key


Here are the steps below:

- gpg --import private.asc && gpg --decrypt backup.pgp

- gpg2john private.asc > hash.txt

- john hash.txt --wordlist=/home/Seclists/Passwords/Leaked-Databases/rockyou-75.txt 


So in this way you will got the passphrase and now you could decrypt the file backup.pgp

In the backup.pgp i saw the content of /etc/passwd

And into this was the hash passowrd of root file.


Now i break the hash using john and got the  password of root.

After breakng the password we will get the pass and will be logged in as root with ssh and get the root flag with  cat root.txt.

That's It!!!!!!!!
