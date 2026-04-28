<div align="center">

<img src="https://assets.tryhackme.com/img/logo/tryhackme_logo_full.svg" alt="TryHackMe Logo" width="260"/>

<br/>

# 🦸 CyberHeroes — Room Writeup

### *Client-Side Authentication Bypass · Source Code Analysis · JavaScript Reversing*

<br/>

[![TryHackMe](https://img.shields.io/badge/Platform-TryHackMe-red?style=for-the-badge&logo=tryhackme&logoColor=white)](https://tryhackme.com)
[![Difficulty](https://img.shields.io/badge/Difficulty-Easy-brightgreen?style=for-the-badge&logo=shield&logoColor=white)](#)
[![Category](https://img.shields.io/badge/Category-Web%20Security-blue?style=for-the-badge&logo=googlechrome&logoColor=white)](#)

</div>

---

## 📋 Room Overview

> **"Want to be part of the elite club of CyberHeroes? Prove your merit by finding a way to log in!"**

This is a beginner-friendly web challenge that teaches one of the most classic mistakes developers make — **storing sensitive credentials directly in client-side JavaScript**. The entire authentication logic lives in the browser, meaning anyone who knows where to look can read the username and password without ever sending a single login attempt.

**Key concepts covered:**
- 🔍 Reading and understanding browser page source
- 🔑 Client-side authentication flaws
- 🔄 Simple string reversal as (very weak) obfuscation
- 🚩 Capturing the flag through a logic bypass

---

## 🛠️ Environment Setup

Before diving into the challenge, make sure your environment is ready:

1. Click the green **"Start Machine"** button inside the TryHackMe task to spin up the vulnerable machine.
2. Start the **AttackBox** using the button at the top-right of the page (or connect through OpenVPN if you prefer your own machine).
3. Once the machine's IP is assigned, open a new browser tab and navigate to:

```
http://MACHINE_IP
```

> 💡 **Tip:** If the page doesn't load immediately, wait 30–60 seconds for the machine to fully boot up, then refresh.

---

## 🎯 Reconnaissance — First Look at the Target

After getting the machine's IP and navigating to it in the browser, we were greeted with the following page:

![homepage](homepage.png)

Right away we can see a **login form** — a username field and a password field with a submit button. The room is telling us to find our way in, so the login page is clearly the entry point.

At this point, the natural instinct is to try a few things:

- 🔨 Brute-force common credentials
- 💉 Try SQL injection in the fields
- 🌐 Inspect network requests with `curl` or Burp Suite

I actually started down the brute-force path first, but hit an obstacle — the login mechanism **triggers a JavaScript popup alert** when credentials are wrong, rather than submitting to a backend endpoint. That's a huge hint: the authentication is happening **entirely on the client side**, not on a server.

---

## 🔍 Source Code Analysis — The Real Attack Surface

When brute-forcing didn't feel right and the popup behaviour seemed odd, I opened the browser's **Page Source** (right-click → View Page Source, or `Ctrl + U`). This is a habit every aspiring hacker should build — always check what the page is actually serving before assuming complexity.

Scrolling through the HTML, I found an embedded JavaScript function handling the login logic:

```js
function authenticate() {
    a = document.getElementById('uname')
    b = document.getElementById('pass')
    const RevereString = str => [...str].reverse().join('');
    if (a.value=="h3ck3rBoi" & b.value==RevereString("54321@terceSrepuS")) { 
        var xhttp = new XMLHttpRequest();
        xhttp.onreadystatechange = function() {
            if (this.readyState == 4 && this.status == 200) {
                document.getElementById("flag").innerHTML = this.responseText ;
                document.getElementById("todel").innerHTML = "";
                document.getElementById("rm").remove() ;
            }
        };
        xhttp.open("GET", "RandomLo0o0o0o0o0o0o0o0o0o0gpath12345_Flag_"+a.value+"_"+b.value+".txt", true);
        xhttp.send();
    }
    else {
        alert("Incorrect Password, try again.. you got this hacker !")
    }
}
```

---

## 🧠 Breaking Down the Logic — Why the Password Was So Easy to Find

Let me walk through this like a human explaining it to a friend over coffee ☕:

**Think of it like a locked diary where the key is taped to the inside cover.**

Here's what's happening line by line:

- `a = document.getElementById('uname')` — this grabs whatever you type into the **username** box. The variable `a` is your username.
- `b = document.getElementById('pass')` — this grabs whatever you type into the **password** box. The variable `b` is your password.
- `const RevereString = str => [...str].reverse().join('');` — this is a helper function that takes any string and **flips it backwards**. For example, `"hello"` becomes `"olleh"`.

Now, the juicy part — the `if` condition:

```js
if (a.value=="h3ck3rBoi" & b.value==RevereString("54321@terceSrepuS"))
```

This is literally saying:
> *"Let the user in **only if** the username equals `h3ck3rBoi` AND the password equals the reversed version of `54321@terceSrepuS`."*

The **username is written in plain text** — no tricks, no encoding. It's just sitting there: `h3ck3rBoi`.

The **password** has a tiny bit of "obfuscation" — it's stored reversed as `54321@terceSrepuS`, and then `RevereString()` is called on it during the check. But here's the thing: calling `RevereString("54321@terceSrepuS")` just flips it back, which means the actual password the system is checking against is `SuperSecret@12345`.

All I had to do was read the string `"54321@terceSrepuS"` and manually reverse it in my head (or even just type it backwards). No tools, no brute-force, no fancy exploit. Just reading.

> 🔑 **The Takeaway:** Never store credentials in client-side JavaScript. Any user can view your page source. If the authentication check runs in the browser, the browser has to have all the information needed to make that check — which means the user has it too.

---

## 🚩 Q1 — Uncover the Flag!

Armed with the credentials extracted directly from the source code:

| Field | Value |
|:---|:---|
| 👤 Username | `h3ck3rBoi` |
| 🔐 Password | `SuperSecret@12345` |

I entered them into the login form and hit submit. The JavaScript's `if` condition evaluated to `true`, the `XMLHttpRequest` fired off a GET request to the hidden flag file path, and the response was rendered directly on the page:

```text
Congrats Hacker, you made it !! Go ahead and nail other challenges as well :D REDACTED
```

> 🎉 Flag captured! The `REDACTED` part above is where the actual flag value appears on your instance.

---

## 📚 Lessons Learned

This room is short, but it packs a genuinely important lesson that real developers and security engineers deal with every day:

1. **Client-side authentication is not authentication.** If the check runs in the browser, it can be bypassed. Always validate credentials on the server.
2. **Page source is public.** Everything in your HTML, CSS, and JavaScript is readable by anyone. Treat it like a billboard, not a vault.
3. **Obfuscation ≠ security.** Reversing a string, base64-encoding it, or renaming variables doesn't protect anything — it just slows down someone curious for about 30 seconds.
4. **Always check the source before reaching for heavy tools.** Burp Suite and Metasploit are powerful, but sometimes the answer is just `Ctrl + U`.

---

<div align="center">

[![TryHackMe](https://img.shields.io/badge/Try%20This%20Room-CyberHeroes-red?style=for-the-badge&logo=tryhackme&logoColor=white)](https://tryhackme.com)
[![Authentication Bypass](https://img.shields.io/badge/Related%20Room-Authentication%20Bypass-orange?style=for-the-badge&logo=shield&logoColor=white)](https://tryhackme.com/room/authenticationbypass)

<br/>

*Happy hacking — ethically, legally, and curiously.* 🔐🦸

</div>
