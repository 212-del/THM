<div align="center">

<img src="https://assets.tryhackme.com/img/THMlogo.png" alt="TryHackMe Logo" width="180"/>

<br/><br/>

# 🐍 Operation Slither — Full Writeup

### *OSINT · Social Media Reconnaissance · Threat Actor Profiling*

<br/>

[![TryHackMe](https://img.shields.io/badge/TryHackMe-Operation%20Slither-red?style=for-the-badge&logo=tryhackme&logoColor=white)](https://tryhackme.com)
[![Difficulty](https://img.shields.io/badge/Difficulty-Easy-brightgreen?style=for-the-badge)](https://tryhackme.com)
[![Category](https://img.shields.io/badge/Category-OSINT-blueviolet?style=for-the-badge&logo=searchengin&logoColor=white)](https://tryhackme.com)
[![Status](https://img.shields.io/badge/Status-Completed%20✓-success?style=for-the-badge)](https://tryhackme.com)

<br/>

<img src="https://media.giphy.com/media/077i6AULCXc0FKTj9s/giphy.gif" width="130" alt="Hacker at work"/>

</div>

---

## 🗺️ Room Overview

Unlike most TryHackMe rooms, this one gives us **no IP address, no target machine, and no exploit to fire** — just a paragraph pulled from a hacker forum. That's the entire starting point.

The target company: **TryTelecomMe**
The threat group responsible: **Sneaky Viper Group**
Our objective: **Unmask every operator behind Operation Slither**

This is a pure **OSINT (Open-Source Intelligence)** challenge — it's all about tracing digital footprints across social media platforms, correlating usernames, and reading between the lines of seemingly innocent public content.

> 💡 **Core Skills Tested:** Social media enumeration, cross-platform username correlation, encoded content identification, and methodical OSINT pivoting.

---

## 📋 Reconnaissance Instructions (From the Room)

> Keep these guidelines in mind as you work through each part. They're not just flavour text — they're actual hints pointing you in the right direction.

```
🔍 Part 1 — Reconnaissance Guide:
  - Begin with the provided username and perform a broad search across common social platforms.
  - Correlate discovered profiles to confirm ownership and authenticity.
  - Review interactions, posts, and replies for potential leads.

🔍 Part 2 — Reconnaissance Guide:
  - Use related usernames or connections identified in earlier steps to expand reconnaissance.
  - Enumerate additional platforms for linked accounts and shared content.
  - Follow media or resource references across platforms to trace information flow.

🔍 Part 3 — Reconnaissance Guide:
  - Identify secondary accounts through visible interactions (likes, follows, collaborations).
  - Extend reconnaissance into developer or technical platforms associated with the identity.
  - Analyse activity history (such as repositories or commits) for embedded information.
```

---

## 🧩 Part 1 — The Forum Post

![part1](part1.png)

Here's the initial forum post we were presented with:

```
Full user database TryTelecomMe on sale!!!

As part of Operation Slither, we've been hiding for weeks in their network and have now
started to exfiltrate information.
This is just the beginning. We'll be releasing more data soon. Stay tuned!

@v3n0mbyt3_

---
```

The only concrete lead we have right now is the username **`@v3n0mbyt3_`**. That's our entry point — and in OSINT, a username is often all you need to start pulling on a thread.

<div align="center">

```
 ╔══════════════════════════════════════════════════════════╗
 ║                                                          ║
 ║    T A R G E T   A C Q U I R E D                         ║
 ║                                                          ║
 ║    Username  :  @v3n0mbyt3_                              ║
 ║    Platform  :  Unknown (yet)                            ║
 ║    Status    :  [ TRACKING... ]                          ║
 ║                                                          ║
 ╚══════════════════════════════════════════════════════════╝
```

<img src="https://media.giphy.com/media/xTiTnxpQ3ghPiB2Hp6/giphy.gif" width="200" alt="Investigation in progress"/>

</div>

---

## ❓ Question 1 — What Other Platform Does v3n0mbyt3_ Use?

> **Q1: Aside from Twitter / X, what other platform is used by v3n0mbyt3_? Answer in lowercase.**

### 🔎 Methodology

The question tells us Twitter/X is already known and excluded — so the account exists on at least one additional platform. The reconnaissance guide says to perform a **broad search across common social platforms**, which means going through them one by one.

I put together a list of popular platforms and mapped out which ones have 7-character names (the question is case-sensitive about lowercase, and testing platform names of similar length helps narrow things down fast):

| Platform | Letter Count | Checked |
|:---------|:------------:|:-------:|
| Twitter/X | 7 | ❌ Excluded |
| Discord | 7 | ✅ Not found |
| YouTube | 7 | ✅ Not found |
| **Threads** | **7** | ✅ **Found it!** |
| MySpace | 7 | ✅ Not found |
| Reddit | 6 | ✅ Not found |
| GitHub | 6 | ✅ Not found |

After working through the list, the profile turned up on **Threads** — Meta's text-based social platform, which launched as a direct competitor to Twitter/X.

> 💡 **Tip:** When doing username searches across platforms, going alphabetically or by popularity saves time. Don't just randomly guess — be systematic about it.

### ✅ Answer: `threads`

---

## ❓ Question 2 — What Is the Value of the Flag?

> **Q2: What is the value of the flag?**

### 🔎 Methodology

With **Threads** confirmed, the next step is enumerating the profile as thoroughly as possible. The reconnaissance guide specifically says to *"review interactions, posts, and replies for potential leads"* — which is exactly where things get interesting.

When I tried searching for the username with `@` included on the Threads search bar, it returned nothing. The fix was simple — just plug the username directly into the URL:

**Direct Profile URL:** [https://www.threads.com/@v3n0mbyt3_](https://www.threads.com/@v3n0mbyt3_)

> 💡 **Tip:** When a platform's search bar fails with special characters, bypass it entirely by constructing the URL manually. This works on almost every major social platform.

You'll need to create a Threads account to view replies and full interaction history — it's necessary here. After logging in, I went through everything:

- 📌 Posts — nothing obvious
- 💬 **Replies tab** — 🚨 **this is where it was hiding**
- ❤️ Liked posts — nothing
- 🔔 Mentions — nothing

Inside the **replies tab**, there was a suspicious-looking string that clearly didn't belong in any normal conversation:

![flag](flag1.png)

```
VEhNe3NsMXRoM3J5X3R3MzN0el80bmRfbDM0a3lfcjNwbDEzcyF9
```

The moment I saw that string, the character set gave it away — **Base64**. No padding symbols this time, but the alphanumeric + `+` / `/` character distribution is unmistakable once you've decoded a few.

### 🛠️ Decoding

I ran it through my [CyberSecurity Calculator](https://github.com/212-del/CyberSecurity-Calculator.git) using Option 2 (Base64 decode):

```
Input  :  VEhNe3NsMXRoM3J5X3R3MzN0el80bmRfbDM0a3lfcjNwbDEzcyF9
Output :  THM{REDACTED}
```

And there's the first flag. Q2 solved.

### ✅ Answer: `THM{REDACTED}`

---

## 🧩 Part 2 — The Second Forum Message

<div align="center">

```
60GB of data owned by TryTelecomMe is now up for bidding!

Number of users: 64500000 Accepting all types of crypto
For takers, send your bid on Threads via this handle:

HIDDEN CONTENT 
----------------------------------------------------------------------------------------------------- 
You must register or log in to view this content
```

</div>

The second forum post was published, but our forum account was deleted before we could retrieve the operator's handle. The trail now continues from what we've already found on Threads.

---

## ❓ Question 3 — Who Is the Second Operator?

> **Q3: What is the username of the second operator talking to v3n0mbyt3_ from the previous platform?**

### 🔎 Methodology

Back to the **replies tab** on the Threads profile. The recon guide says to look at interactions, and when you check through the replies section of `@v3n0mbyt3_`, there's only one account actively engaging with them:

**`@_myst1cv1x3n_`**

The naming pattern is very telling — leetspeak characters, underscores on both sides, deliberately obfuscated. This is consistent with the kind of coordinated persona handles threat groups use. It's a natural pivot point for the next phase of the investigation.

> 💡 **Note:** In OSINT, sometimes the most obvious thing in plain sight is the answer. Don't overthink it.

### ✅ Answer: `_myst1cv1x3n_`

---

## ❓ Question 4 — What Is the Value of the Flag?

> **Q4: What is the value of the flag?**

### 🔎 Methodology

Following the Part 2 recon guide:

> *"Enumerate additional platforms for linked accounts and shared content."*
> *"Follow media or resource references across platforms to trace information flow."*

I searched for `_myst1cv1x3n_` across several platforms. The account surfaced on **Instagram**.

Browsing through the Instagram profile, I found a **reel** that has comments on it, the flag was visible in the comment:

**Reel:** [https://www.instagram.com/reel/C6BuP1CNMCI/](https://www.instagram.com/reel/C6BuP1CNMCI/)

> 💡 **Lesson Learned:** Flags and sensitive data aren't always hiding in text posts. Check video content, story highlights, pinned posts, and media captions. OSINT means looking *everywhere*.

<div align="center">

<img src="https://media.giphy.com/media/l46CkATpdyLwLI7vi/giphy.gif" width="200" alt="Searching for clues"/>

</div>

### ✅ Answer: *(visible within the Instagram reel)*

---

## 🧩 Part 3 — The Final Operator

The third and final forum post is the most technically detailed of the three:

```
FOR SALE

Advanced automation scripts for phishing and initial access!

Inclusions:
  - Terraform scripts for a resilient phishing infrastructure
  - Updated Google Phishlet (evilginx v3.0)
  - GoPhish automation scripts
  - Google MFA bypass script
  - Google account enumerator
  - Automated Google brute-forcing script
  - Cobalt Strike aggressor scripts
  - SentinelOne, CrowdStrike, Cortex XDR bypass payloads

PRICE: $1500
Accepting all types of crypto
Contact me on REDACTED@protonmail.com
```

<div align="center">

```
 ╔═══════════════════════════════════════════════════════╗
 ║                                                       ║
 ║    O P E R A T O R   # 3   —   L O C A T I N G        ║
 ║                                                       ║
 ║    Cross-platform pivot in progress...                ║
 ║    [ ██████████████████░░░░░░░░ ]  72%                ║
 ║                                                       ║
 ╚═══════════════════════════════════════════════════════╝
```

</div>

The level of technical knowledge shown here — Terraform, evilginx, Cobalt Strike, EDR bypasses — tells us this third operator has a very strong technical background. That detail becomes important for Q6.

---

## ❓ Question 5 — What Is the Handle of the Third Operator?

> **Q5: What is the handle of the third operator?**

### 🔎 Methodology

The Part 3 recon guide points us toward **visible interactions** — likes, follows, and collaborations. Going back to the second operator's Instagram profile and carefully examining who they interact with in their posts and story activity, a third handle begins to emerge from the interaction data.

This is a classic OSINT pivot: from Operator 2's social graph, we can identify Operator 3's handle before we've even found their profile directly.

> 💡 **Tip:** On Instagram, check followers, followings, tagged posts, story mentions, and comment threads. Operators in the same group tend to interact with each other across their personas.

### ✅ Answer: *(discovered through Instagram interaction analysis on `@_myst1cv1x3n_`'s profile)*

---

## ❓ Question 6 — What Other Platform Does the Third Operator Use?

> **Q6: What other platform does the third operator use? Answer in lowercase.**

### 🔎 Methodology

The Part 3 recon guide is remarkably specific here:

> *"Extend reconnaissance into developer or technical platforms associated with the identity."*
> *"Analyse activity history (such as repositories or commits) for embedded information."*

The words **"developer or technical platforms"** combined with **"repositories or commits"** are not subtle hints — they're practically pointing at you. Then factor in the forum post content: **Terraform scripts**, **GoPhish automation**, **Cobalt Strike aggressor scripts** — this person clearly writes code for a living.

I cross-referenced the third operator's handle across technical platforms, focusing on 6-character names first (which the answer turns out to be):

| Platform | Characters | Technical Focus |
|:---------|:----------:|:---------------:|
| **GitHub** | **6** | ✅ **Yes — repositories, commits** |
| GitLab | 6 | ✅ Yes |
| Bitbucket | 9 | ✅ Yes |

Trying **GitHub** first — and that was the right call. The account was there.

> 💡 **Tip:** When the recon guide specifically mentions "repositories" and "commits," that's a near-direct reference to GitHub. Always read the hints carefully.

### ✅ Answer: `github`

---

## ❓ Question 7 — What Is the Final Flag?

> **Q7: What is the value of the flag?**

### 🔎 Methodology

On the third operator's GitHub profile, the final section of the recon guide says to *"analyse activity history (such as repositories or commits) for embedded information."*

This means going through:

- 📁 **Repositories** — README files, file contents, repo descriptions
- 📝 **Commit history** — commit messages, diff content, older file versions
- 📌 **Pinned repositories** — often the most notable/active work
- 👤 **Profile README** — if they have one, it's worth checking

The flag is embedded somewhere in the GitHub activity trail — methodically working through each of these areas will surface it.

> 💡 **Pro Tip:** On GitHub, even *deleted* content can sometimes be recovered through commit history. Don't just look at the current state of a repository — scroll back through the commits.

<div align="center">

<img src="https://media.giphy.com/media/RbDKaczqWovIugyJmW/giphy.gif" width="160" alt="Flag captured"/>

</div>

### ✅ Answer: *(discovered through GitHub repository/commit analysis)*

---

## 🧠 Key Takeaways

This room was genuinely one of the more interesting ones I've done — it forces you to slow down, think like an investigator, and follow the breadcrumbs rather than just firing tools at a machine. Here's what I took away:

| 🎯 Lesson | 📖 Detail |
|:----------|:----------|
| 🔗 **Username Correlation** | The same handle patterns (leetspeak + underscores) appeared across platforms — cross-referencing was straightforward once you recognised the style |
| 🌐 **URL-Based Search** | When platform search bars fail, construct the profile URL manually — works almost every time |
| 🎥 **Media-Embedded Content** | The second flag was inside a video reel — OSINT means checking *all* content types, not just text |
| 💻 **Read the Hints** | The recon guides literally said "repositories or commits" — that's GitHub. Always read carefully before guessing |
| 🔑 **Base64 Recognition** | The character set of the encoded string gave it away immediately — training your eye on encoding patterns pays off fast |
| 🕸️ **Social Graph Pivoting** | Moving from Operator 1 → Operator 2 → Operator 3 through interaction data is classic OSINT methodology |

---

## 🛠️ Tools Used

| 🔧 Tool | 🎯 Purpose |
|:--------|:-----------|
| 🌐 **Threads** | Platform enumeration for first operator (`@v3n0mbyt3_`) |
| 📸 **Instagram** | Cross-platform pivot for second operator (`@_myst1cv1x3n_`) |
| 💻 **GitHub** | Technical platform analysis for third operator |
| 🔐 **[CyberSecurity Calculator](https://github.com/212-del/CyberSecurity-Calculator.git)** | Base64 identification and decoding |
| 🧠 **Manual OSINT** | Username correlation, interaction mapping, content analysis |
| 🌍 **Browser URL Manipulation** | Bypassing broken platform search features |

---

<div align="center">

```
 ▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓
 ▓                                                   ▓
 ▓    M I S S I O N   C O M P L E T E                ▓
 ▓                                                   ▓
 ▓    Operators Identified  :  3 / 3  ✓              ▓
 ▓    Flags Captured        :  3 / 3  ✓              ▓
 ▓    Operation Slither     :  N E U T R A L I S E D ▓
 ▓                                                   ▓
 ▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓
```

### *The snake has been defanged.* 🐍✅

<br/>

[![TryHackMe](https://img.shields.io/badge/TryHackMe-LinuxX-red?style=for-the-badge&logo=tryhackme&logoColor=white)](https://tryhackme.com/p/LinuxX)
[![GitHub](https://img.shields.io/badge/GitHub-212--del-black?style=for-the-badge&logo=github&logoColor=white)](https://github.com/212-del)
[![OSINT](https://img.shields.io/badge/Skill-OSINT-blueviolet?style=for-the-badge&logo=searchengin&logoColor=white)](https://github.com/212-del/THM)

<br/>

*Made with ☕ and way too many browser tabs open.*

</div>

