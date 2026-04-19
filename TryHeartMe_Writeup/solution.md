<div align="center">

<img src="https://assets.tryhackme.com/img/THMlogo.png" alt="TryHackMe Logo" width="220"/>

<br/>

# 💌 TryHeartMe — Writeup

### *JWT Token Manipulation · Business Logic Bypass · Web Application Security*

<br/>

[![TryHackMe](https://img.shields.io/badge/TryHackMe-TryHeartMe-red?style=for-the-badge&logo=tryhackme&logoColor=white)](https://tryhackme.com)
[![Difficulty](https://img.shields.io/badge/Difficulty-Medium-yellow?style=for-the-badge&logo=shield&logoColor=white)](https://tryhackme.com)
[![Category](https://img.shields.io/badge/Category-Web%20Security-blue?style=for-the-badge&logo=firefox&logoColor=white)](https://tryhackme.com)
[![Technique](https://img.shields.io/badge/Technique-JWT%20Forgery-orange?style=for-the-badge&logo=jsonwebtokens&logoColor=white)](https://tryhackme.com)

</div>

---

> 🧠 **Core Concept:** This room revolves around exploiting a poorly validated JSON Web Token (JWT). By forging a token with elevated privileges, we can bypass authentication controls and access content that should be restricted — a classic case of broken access control.

---

## 📋 Table of Contents

| # | Section |
|:---:|:---|
| 1 | [🚀 Task 1 — Deploy the Machine & Access the Web Application](#-task-1--deploy-the-machine--access-the-web-application) |
| 2 | [📝 Task 2 — Sign Up and Explore the Application](#-task-2--sign-up-and-explore-the-application) |
| 3 | [🔑 Task 3 — JWT Token Analysis and Manipulation](#-task-3--jwt-token-analysis-and-manipulation) |
| 4 | [🏆 Task 4 — Access the Hidden Product and Capture the Flag](#-task-4--access-the-hidden-product-and-capture-the-flag) |

---

## 🚀 Task 1 — Deploy the Machine & Access the Web Application

As mentioned on the room page, the machine is accessible at:

```
http://10.49.181.116:5000
```

Navigating to this address, we are greeted with the application's homepage:

![homepage_look](homepage_look.png)

The objective of this room is to **find a way to purchase the hidden item** — a product that is concealed from regular users.

At this stage, the interactable elements are the **Sign Up** page, the **Login** page, and a listing of several products. Viewing the page source did not reveal anything immediately useful, so the next logical step was to create an account and investigate the application from the inside.

---

## 📝 Task 2 — Sign Up and Explore the Application

After creating an account and logging in, we can observe that our **credits balance is zero**:

![credits](credits.png)

While browsing as a logged-in user and visiting the product page, something interesting catches the eye — the application is exposing a **`role`** field in the interface:

![role](role.png)

This is a significant finding. The fact that the application is rendering a role means that role-based access control is likely being handled somewhere client-side — or at least being transmitted in a token that we can inspect and potentially manipulate.

---

## 🔑 Task 3 — JWT Token Analysis and Manipulation

Opening the browser's developer tools and navigating to the **Application → Cookies** tab reveals a cookie named `tryheartme_jwt`. This is a **JSON Web Token (JWT)**.

> 💡 **What is a JWT?** A JSON Web Token is a compact, URL-safe token used to transmit claims between parties. It consists of three Base64-encoded parts: the **header**, the **payload**, and the **signature**, separated by dots (`.`).

The original token found in the cookie was:

```
tryheartme_jwt: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJlbWFpbCI6Imd1ZXN0QGdtYWlsLmNvbSIsInJvbGUiOiJ1c2VyIiwiY3JlZGl0cyI6MCwiaWF0IjoxNzc2NTgyODI5LCJ0aGVtZSI6InZhbGVudGluZSJ9.V1qBFtAwOhBWuPFutzpATGT8SN7V0xjCDprbc2JgHmI
```

Decoding this token (using [jwt.io](https://jwt.io) or a similar tool) reveals the following:

**Header:**
```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

**Payload:**
```json
{
  "email": "guest@gmail.com",
  "role": "user",
  "credits": 0,
  "iat": 1776582829,
  "theme": "valentine"
}
```

The `role` field is set to `"user"`. The goal is to change this to `"admin"`.

Since the server appears to accept tokens without properly verifying the signature, we can craft a new JWT with the `role` field set to `"admin"` while keeping all other values identical. The crafted token becomes:

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJlbWFpbCI6Imd1ZXN0QGdtYWlsLmNvbSIsInJvbGUiOiJhZG1pbiIsImNyZWRpdHMiOjAsImlhdCI6MTc3NjU4MjgyOSwidGhlbWUiOiJ2YWxlbnRpbmUifQ.b7Uza8q9taClisYffV6ehTwwKDCVJHbMi12cGMpuXts
```

This is then pasted into the `tryheartme_jwt` cookie value via the Application tab in developer tools, replacing the original token.

> ⚠️ **Why does this work?** The vulnerability exists because the server either does not validate the JWT signature at all, or uses a weak/guessable secret. This is a well-documented security flaw — **never trust data in a JWT payload without verifying its signature server-side**.

---

## 🏆 Task 4 — Access the Hidden Product and Capture the Flag

After replacing the cookie value and refreshing the page, the application now recognises us as an **admin** — and our credit balance jumps to **5,000 tokens**:

![admin](admin.png)

Heading back to the product listing, the previously hidden item is now visible:

![hidden](hidden.png)

Clicking on the hidden product and hitting **Buy** triggers the purchase — and the application responds with the **flag**. 🎉

---

<div align="center">

### 🔐 Key Takeaways

| Concept | Detail |
|:---|:---|
| **Vulnerability** | JWT token forgery / signature not validated |
| **Impact** | Full privilege escalation from `user` to `admin` |
| **Fix** | Verify JWT signatures server-side using a strong, secret key |
| **Tools Used** | Browser DevTools, jwt.io |

<br/>

[![forthebadge](https://forthebadge.com/images/badges/built-with-love.svg)](https://github.com/212-del/THM)

*Practise ethically. Hack legally. Learn relentlessly.* 🛡️

</div>
