# Simple CTF — Writeup 🚩

---

## 🔍 Enumeration

**Q1 & Q2 — Open Ports**

The first question asks how many services are running under port 1000 — in other words, how many open ports have a port number less than 1000.

Let's do a quick Nmap scan to find out.

![Nmap_res](Nmap_res.png)

We can clearly see the ports running with a number below 1000. This scan also answers Q2 — *What is running on the higher port?*

---

**Q3 — Finding the CVE**

Next up: *What's the CVE you're using against the application?*

We still don't know what application is running on this IP, so let's check.

![apache_def](apache_def.png)

We can see the default Apache page — it seems like no custom service is running at the root.

Since it's a webpage, we tried to brute-force the endpoints using:

```
ffuf -u http://<IP>/FUZZ -w /usr/share/seclists/Discovery/Web-content/big.txt
```

![ffuf_res](ffuf_res.png)

We got 2 interesting endpoints — `/simple` and `/robots.txt`.

![robots_endpoint](robots_endpoint.png)

Hitting `/robots.txt` revealed another endpoint, but it's unreachable — that's a useful clue.

We now have 2 valid endpoints. Hitting the `/simple` endpoint brought up the well-known open-source CMS — **CMS Made Simple**.

![cms_version](cms_version.png)

We can see the version is **2.2.8**. Now we have an application to work with.

We'll search for any available exploit for this version using **searchsploit**:

```
searchsploit -c cms 2.2.8
```

![searchsploit_res](searchsploit_res.png)

We can see it's vulnerable to **SQLi**. The search result also shows a path on the right side — we'll need that.

To get more details about the exploit and find the CVE, run:

```
searchsploit -x <path>
```

> Make sure to copy the path shown in the right column of the previous result.

You'll get the CVE number — that's the answer to Q3. ✅

---

## 💥 Exploitation

We'll use msfconsole to work with this. I searched using the command **search cms 2.2.8** and loaded the exploit with **use \<exploit\_name\>**.

![msf_exploit1](msf_exploit1.png)

This module requires a username and password — both mandatory — so we can't use it just yet.

We'll go back to *searchsploit* with a different search term:

```
searchsploit cms made simple
```

After this, we'll get a bunch of results.

![msf_bunch](msf_bunch.png)

Let's narrow it down further:

```
searchsploit cms made simple 2.2.8
```

We get exactly 1 result. Copy the exploit ID and run:

```
searchsploit -m 46635
```

This copies the exploit script to your current directory. The syntax to run it is:

```
python2 46635.py -u http://<room_ip>/simple
```

![exploit_use](exploit_use.png)

We now have the password hash for user **mitch**. 🎯

---

## 🔓 Cracking the Password

We'll crack this hash using **hashcat**. The command structure is:

```
hashcat [options] [options_values] [hash:salt] [wordlist/mask]
```

```
Command explanation:

1. -O flag
    Optimized kernel mode
    Enables faster cracking

2. -a 0
    Attack mode
    0 = Straight (dictionary) attack
    Hashcat takes each word from the wordlist and tries it as the password

3. -m 10
    Hash type
    10 = md5($pass.$salt)

    👉 This means:
    The password is concatenated with the salt, then hashed using MD5

    Formula: hash = MD5(password + salt)

4. 0c01f4468bd75d7a84c7eb73846e8d96:1dac0d92e9fa6bb2
    This is the hash + salt pair

5. /usr/share/wordlists/rockyou.txt
    The wordlist (dictionary)
```

> ⚠️ Hashcat may not run on low-spec devices or might throw errors.

You can also crack the hash online using:
🔗 [https://hashes.com/en/decrypt/hash](https://hashes.com/en/decrypt/hash)

Enter the hash and salt in the format: `password_hash:salt`

Once cracked, submit the password as the answer to Q5. ✅

---

## 🔑 SSH Login

For Q6, it asks where we can log in using the above credentials — the answer is 3 letters. **SSH** came to mind immediately.

Let's log in as `mitch`:

```
ssh mitch@<room_ip> -p 2222
```

![ssh_login](ssh_login.png)

This SSH session contains the **user flag** (Q7) and the answer to **Q8** as well.

---

## ⬆️ Privilege Escalation

Inside our SSH session, let's check what permissions we have:

```
sudo -l
```

![perm_img](perm_img.png)

We can see that user `mitch` is allowed to run **vim** as root.

For Q9, it asks what we're using as leverage to gain root access — the answer is the program we're permitted to run as root.

After opening vim with **sudo vim**, type **:!bash** to spawn a shell. Since we're running vim as root, the shell we spawn is a **root shell**. 🔥

![shell_img](shell_img.png)

After this, we'll have a root shell — verify it with `whoami`.

Poke around and you'll find the **root flag**, which is the answer to Q10. 🎉
