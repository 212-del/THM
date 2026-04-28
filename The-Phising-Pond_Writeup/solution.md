<div align="center">

<!-- TryHackMe Badge -->
[![TryHackMe](https://img.shields.io/badge/TryHackMe-The%20Phishing%20Pond-red?style=for-the-badge&logo=tryhackme&logoColor=white)](https://tryhackme.com)
&nbsp;
![Category](https://img.shields.io/badge/Category-Phishing%20Awareness-orange?style=for-the-badge&logo=gmail&logoColor=white)
&nbsp;
![Difficulty](https://img.shields.io/badge/Difficulty-Easy-brightgreen?style=for-the-badge)
&nbsp;
![Status](https://img.shields.io/badge/Status-Completed%20✓-brightgreen?style=for-the-badge)

</div>

---

# 🎣 The Phishing Pond — Solution Writeup

---

## 📖 Room Overview

**The Phishing Pond** is a phishing awareness challenge on TryHackMe where you are presented with a series of emails and must determine whether each one is a phishing attempt or legitimate correspondence. The challenge tests your ability to recognise common social engineering tactics used by real-world attackers — the very same tricks that land in corporate inboxes every single day.

> *"Catch the phish before the phish catches you."*

---

## 🎮 Getting Started

After launching the room and navigating to the lab URL, I was greeted with the game's landing page:

![Homepage](homepage.png)

Scrolling to the bottom of the page reveals the full game rules:

![Bottom of Page](bottom.png)

---

## 📋 Game Rules

The rules are straightforward but unforgiving:

- You are shown **10 emails** one at a time
- For each email you must decide: **Phishing** or **Legitimate**
- If you flag an email as phishing, you must also select the **correct reason** from a set of options
- You are allowed a maximum of **3 mistakes** total
- Misidentifying whether an email is phishing *or* selecting the wrong reason both cost you a life
- Complete the game successfully to receive the **flag** 🏁


---

### 📧 Email 1 — Invoice Action Required ⚠️ Phishing

> **From:** Accounts `<accounts@vendor-payments.com>`
> **To:** Peter Smith `<peter.smith@tryhackme.com>`
> **Subject:** Invoice INV-2025-334 (Action required)
>
> *Hi Peter, your invoice INV-2025-334 is ready. Please review and pay via https://pay.vendor-payments-secure.com/invoice/INV-2025-334.*

**Verdict: 🚨 Phishing**

**Why is this phishing?** Pick the correct reason:

- A) The invoice number format does not match standard accounting conventions
- B) The email was sent outside of normal business hours
- C) **✅ The payment link uses a deceptive lookalike domain designed to impersonate a legitimate payment portal**
- D) The email failed to include a PDF attachment

**Correct Answer: C**

**Explanation:** The email arrives from `vendor-payments.com` but directs you to `vendor-payments-secure.com` — a subtle domain swap. Real vendor payment portals do not need to append `-secure` to their domain name; this is a classic trick to make a malicious link look trustworthy at a quick glance. If you were busy processing a batch of invoices, it would be very easy to miss this one.

---

### 📧 Email 2 — Mandatory Security Re-login ⚠️ Phishing

> **From:** IT Helpdesk `<it-support@service-update.com>`
> **To:** Peter Smith `<peter.smith@tryhackme.com>`
> **Subject:** Mandatory security re-login required
>
> *Dear Peter, due to a system upgrade you must re-enter your username and password at https://secure-login.example.com within 48 hours to retain access.*

**Verdict: 🚨 Phishing**


**Why is this phishing?**

A few red flags jump out immediately:

1. **Sender domain mismatch** — A legitimate IT helpdesk email from TryHackMe would come from `@tryhackme.com`, not `@service-update.com`. This is a completely unrelated domain masquerading as internal IT.
2. **Artificial urgency** — *"Within 48 hours to retain access"* is a textbook pressure tactic. Attackers deliberately manufacture time pressure to stop you from pausing to think critically.
3. **Credential harvesting link** — No legitimate IT department sends a link asking you to re-enter your username *and* password via an external URL. This is a classic credential-phishing setup.
4. **Generic greeting** — *"Dear Peter"* combined with a vague system upgrade explanation is typical of mass-distributed phishing templates adapted with just a first name.

---

### 📧 Email 3 — Gift Card Request ⚠️ Phishing

> **From:** Carlos Mendes `<carlos.mendes@partner.example.com>`
> **To:** Peter Smith `<peter.smith@tryhackme.com>`
> **Subject:** Quick favour — can you buy gift cards?
>
> *Hey Pete, hope you're well. I'm swamped with back-to-back calls — can you do me a quick favour? Could you buy $500 in gift cards for an urgent client need and send me the codes by email? I'll reimburse you when I'm free.*

**Verdict: 🚨 Phishing**


**Why is this phishing?**

This is a **Business Email Compromise (BEC) / gift card scam** — one of the most financially damaging forms of phishing, costing organisations billions annually:

1. **Gift card requests are never legitimate business transactions** — No genuine business need is ever fulfilled through gift card codes emailed to a colleague. This is purely a social engineering trick to extract cash quickly and irreversibly.
2. **Urgency + deliberate unavailability combo** — The attacker claims to be *"swamped with back-to-back calls"* so you cannot verify the request by phone. This isolation of the target from other verification channels is completely deliberate.
3. **Colleague impersonation** — Carlos Mendes may be a real person at the organisation. Attackers research their targets on LinkedIn and company websites to impersonate known colleagues and add credibility.
4. **Promise of reimbursement** — The vague *"I'll reimburse you when I'm free"* is designed to lower your guard and frame the transaction as a normal, trust-based workplace favour.

---

### 📧 Email 4A — Job Opportunity Scam ⚠️ Phishing

> **From:** Recruitment `<jobs@career-opps.example.com>`
> **To:** Peter Smith `<peter.smith@tryhackme.com>`
> **Subject:** Exciting job opportunity — immediate start
>
> *Congratulations! We reviewed your profile and you'd be perfect for a new role. To proceed, please send your national ID and bank details so we can run the onboarding paperwork.*

**Verdict: 🚨 Phishing**


**Why is this phishing?**

This email ticks almost every box of a recruitment scam:

1. **Unsolicited job offer out of nowhere** — You never applied. Legitimate employers do not send random *"Congratulations, you're hired!"* emails without any prior interaction or application process.
2. **Requesting sensitive personal data upfront** — Asking for a national ID and bank details before a single interview, reference check, or even a phone call is a massive red flag. This is identity theft and financial fraud dressed up as an onboarding process.
3. **Suspicious and generic domain** — `career-opps.example.com` is not affiliated with any real recruitment agency and the name is suspiciously vague.
4. **Zero specifics about the role** — *"You'd be perfect for a new role"* — what role? What company? What salary? Real offers include specifics; phishing templates do not.

---

### 📧 Email 4B — Survey Feedback Link ⚠️ Phishing

> **From:** Customer Support `<support@survey-feedback.example>`
> **To:** Peter Smith `<peter.smith@tryhackme.com>`
> **Subject:** We value your feedback — quick survey
>
> *Hi Peter, please take this short survey to help us improve: http://survey-feedback.shadylink.fake.*

**Verdict: 🚨 Phishing**


**Why is this phishing?**

This one is particularly obvious if you look at the URL, though in a real attack it would typically be far more convincing:

1. **Clearly fabricated URL** — `shadylink.fake` is not a real top-level domain. In a genuine attack, the domain would be far more convincing, but the pattern — survey link from an unknown provider — remains identical.
2. **Invalid sender domain** — `.example` is not a properly registered domain; it is a reserved placeholder used in documentation, which immediately disqualifies this as a legitimate source.
3. **No context provided** — *"Help us improve"* with no mention of which service, platform, or previous transaction is a hallmark of a generic phishing lure cast at a wide audience.
4. **Plain HTTP, not HTTPS** — The link uses `http://` rather than `https://`, which is a red flag for any page asking you to interact with or submit information.

---

### 📧 Email 5 — Suspicious Activity Password Reset ⚠️ Phishing

> **From:** Social Updates `<no-reply@social.example.com>`
> **To:** Peter Smith `<peter.smith@tryhackme.com>`
> **Subject:** Reset your password to secure your account
>
> *We detected suspicious activity. To secure your account, click https://social.example-security.com/reset and follow the steps to reset your password.*

**Verdict: 🚨 Phishing**


**Why is this phishing?**

1. **Domain impersonation via suffix addition** — The email comes from `social.example.com` but directs you to `social.example-security.com`. The attacker appended `-security` to manufacture a sense of authority while actually landing you on a completely different domain they control.
2. **Unsolicited security alert** — If you did not trigger any suspicious activity, treat any such warning with immediate scepticism. Attackers use fear of account compromise to get victims to act before thinking.
3. **Vague sender identity** — *"Social Updates"* gives no indication of which social platform this is supposedly from, making it clear this is a generic template aimed at catching anyone who has *any* social media account.
4. **Credential harvesting via fake reset form** — Clicking the link takes you to an attacker-controlled page styled to look like a legitimate password reset form, with the sole purpose of capturing whatever you type into it.

---

### 📧 Email 6 — HR Benefits Document with Macros ⚠️ Phishing

> **From:** HR Team `<hr@external-hr-provider.com>`
> **To:** Peter Smith `<peter.smith@tryhackme.com>`
> **Subject:** Updated benefits package (open to review)
>
> *Please review the attached benefits document. If it appears blank, enable macros to view the content.*

**Verdict: 🚨 Phishing**


**Why is this phishing?**

The instruction to *"enable macros to view the content"* is one of the most well-documented and enduring malware delivery techniques in the book:

1. **"Enable macros to view content"** — This is the classic macro malware trick. Macros embedded in Office documents can execute arbitrary code on your machine the moment you click *Enable*. Microsoft has spent years warning users about this exact technique, and for good reason — it is how numerous ransomware campaigns have started.
2. **External HR provider, not the company domain** — Legitimate HR communications at TryHackMe would originate from a `@tryhackme.com` address. `@external-hr-provider.com` has no verifiable connection to the organisation.
3. **Vague subject with no specifics** — *"Updated benefits package"* with no details about what changed, which plan it affects, or who to contact if you have questions is a hallmark of a phishing template.
4. **No named HR contact or return address** — Legitimate HR communications include a named point of contact and clear escalation steps. This email provides neither.

---

### 📧 Email 7 — Open Enrolment Information ✅ Legitimate

> **From:** Benefits Team `<benefits@tryhackme.com>`
> **To:** Peter Smith `<peter.smith@tryhackme.com>`
> **Subject:** Open enrolment information and resources
>
> *Hello Peter, open enrolment for benefits starts next month. We've attached guides and a FAQ page link to help you choose the right plans. No action required now — this is to help you prepare.*

**Verdict: ✅ Legitimate**


**Why is this legitimate?**

1. **Sender domain matches the organisation** — The email comes from `benefits@tryhackme.com`, exactly what you would expect from an internal benefits team communication.
2. **No urgency or pressure tactics** — *"No action required now"* is the polar opposite of a phishing attempt. Attackers thrive on pressure; legitimate communications are measured and patient.
3. **No credential or personal data requests** — The email asks for nothing sensitive. It provides information and points to resources — that is it.
4. **Contextually appropriate and expected** — Open enrolment notifications are routine annual events in any organisation with employee benefits. The email fits perfectly within that expected pattern.
5. **Professional, clear, and informative tone** — The email explains what is happening, why, and what (if anything) needs to happen next. No alarm, no fear, no urgency.

---

### 📧 Email 8 — HR Payroll Correction ⚠️ Phishing

> **From:** HR - Emma Roberts `<emma.roberts@gmail.com>`
> **To:** Peter Smith `<peter.smith@tryhackme.com>`
> **Subject:** Please review the attached payroll correction
>
> *Hi Peter, this is Emma from HR. I'm following up about a payroll correction that requires your bank details. Please open the file attached and send your updated bank account number and sort code so I can process this change.*

**Verdict: 🚨 Phishing**


**Why is this phishing?**

1. **HR using a personal Gmail address** — This is an immediate, non-negotiable red flag. Every HR department at any legitimate organisation uses a corporate email address. `emma.roberts@gmail.com` has no verifiable connection to TryHackMe whatsoever.
2. **Requesting bank details via email** — Legitimate payroll departments never ask for bank account details through an email. Changes to banking information go through authenticated internal portals with verification steps, not unencrypted email conversations.
3. **Vague attachment reference** — *"Please open the file attached"* with no description of what the file contains or its format is a classic malware delivery opener designed to get you to open a weaponised document.
4. **Targeting specific UK banking credentials** — Asking for both an account number and a sort code is specifically targeting UK bank account details — a deliberate, targeted financial fraud attempt rather than a generic template.

---

### 📧 Email 9 — Lunch Plans 🍝 ✅ Legitimate

> **From:** Jane Doe `<jane.doe@tryhackme.com>`
> **To:** Peter Smith `<peter.smith@tryhackme.com>`
> **Subject:** Lunch Plans for Tomorrow
>
> *Hey Peter, do you want to grab lunch tomorrow at noon at the new Italian restaurant downtown? They have good reviews and a quieter back room for conversations. Let me know if that works for you.*

**Verdict: ✅ Legitimate**


**Why is this legitimate?**

This one is fairly clear, but worth understanding the reasoning:

1. **Internal domain sender** — `jane.doe@tryhackme.com` is a legitimate corporate address with no domain spoofing.
2. **No links, no attachments, no data requests** — The email asks for absolutely nothing other than a casual yes or no reply about lunch. There is no payload here.
3. **Normal, everyday workplace context** — Colleagues inviting each other to lunch is completely routine behaviour in any working environment.
4. **Zero phishing indicators** — No urgency, no fear tactics, no financial element, no suspicious URLs, no credential requests — none of the classic phishing triggers are present anywhere in the message.

---

### 📧 Email 10 — Annual Security Conference Invitation ✅ Legitimate

> **From:** Conference Team `<events@tryhackme.com>`
> **To:** Peter Smith `<peter.smith@tryhackme.com>`
> **Subject:** Invitation: Annual Security Conference
>
> *Dear Peter, you are invited to attend the Annual Security Conference hosted by TryHackMe. The event will feature keynote speakers from leading cybersecurity organisations, hands-on workshops, and networking opportunities. Please RSVP if you plan to attend.*

**Verdict: ✅ Legitimate**

**Why is this legitimate?**

1. **Verified sender domain** — `events@tryhackme.com` is a plausible and entirely consistent internal address for event and conference communications from the organisation.
2. **Professional and detailed content** — The email describes keynote speakers, hands-on workshops, and networking opportunities — these are specific, substantive details that match what a real conference invitation looks like.
3. **Simple, low-friction request** — The only action required is an RSVP. No sensitive data, no payment, no credentials, no file downloads.
4. **Contextually plausible** — A cybersecurity company hosting an Annual Security Conference is completely believable and expected. There is nothing unusual about this communication pattern.

---

### 📧 Email 11 — Weekly Sprint Update ✅ Legitimate

> **From:** Project Updates `<updates@tryhackme.com>`
> **To:** Peter Smith `<peter.smith@tryhackme.com>`
> **Subject:** Weekly team update — sprint progress
>
> *Hello Peter, here is the weekly project update. The development team completed the authentication module and began testing the reporting dashboard. No action is needed on your part; this is for your information only. Let me know if you'd like a deeper status on any task.*

**Verdict: ✅ Legitimate**


**Why is this legitimate?**

1. **Trusted internal sender** — `updates@tryhackme.com` is entirely consistent with internal project management tooling or automated status update systems.
2. **Explicitly states no action is needed** — Legitimate status updates are purely informational. Phishing emails almost always want you to *do* something — click a link, open a file, or provide data. This email deliberately asks for none of that.
3. **Specific, technical content** — Mentioning the *"authentication module"* and *"reporting dashboard"* adds contextual credibility you simply would not find in a generic phishing template.
4. **Professional close with no pressure attached** — *"Let me know if you'd like a deeper status on any task"* is a standard, open-ended professional sign-off. No deadline, no fear, no urgency.

---

## 🏁 Result — Flag Captured!

After successfully identifying all emails with the correct verdicts and reasons, the game concluded and the flag was revealed:


![Flag](flag.png)

But If you ran out of 3 lives.

![Game Over](over.png)

> 🎉 **Room completed!** The flag was retrieved after correctly classifying the emails and finishing the game with no more than 3 mistakes. The key is staying methodical — don't rush, read the sender domain carefully, and look for the tell-tale pressure tactics before clicking anything.

---

## 💡 Key Takeaways

This room is a great reminder of just how effective phishing attacks are and why they remain the **number one initial access vector** in real-world data breaches. These are the indicators I look at first now:

| 🔍 Indicator | 💡 What to Watch For |
|:---|:---|
| **Sender Domain** | Does the domain actually match the organisation it claims to represent? |
| **Urgency Language** | *"Act within 48 hours"* — manufactured pressure is a manipulation tool |
| **Credential Requests** | Legitimate services never ask for passwords via email |
| **Lookalike Domains** | `vendor-payments-secure.com` ≠ `vendor-payments.com` — spot the suffix |
| **Macro Instructions** | *"Enable macros to view"* almost always means malware delivery |
| **Personal Finance Requests** | Gift cards, bank account details, sort codes — always suspicious |
| **Free Email Providers for Business** | `@gmail.com` for HR emails is an immediate disqualifier |
| **Unsolicited Good News** | Random job offers or congratulatory emails with data requests are scams |
| **No Specific Details** | Vague descriptions with no verifiable specifics suggest a template attack |
| **Suspicious URLs** | Hover before you click — does the destination domain actually make sense? |

---

<div align="center">

[![TryHackMe](https://img.shields.io/badge/TryHackMe-The%20Phishing%20Pond-red?style=for-the-badge&logo=tryhackme&logoColor=white)](https://tryhackme.com)

*Writeup by [212-del](https://github.com/212-del) · TryHackMe Room: The Phishing Pond*

</div>
