<div align="center">

<img src="https://miro.medium.com/v2/resize:fit:640/format:webp/1*uPrmx2XcdkUuQCZ-T3J4Kg.png" alt="TryHackMe Logo" width="220"/>

<br/>

[![TryHackMe](https://img.shields.io/badge/TryHackMe-AgentT-red?style=for-the-badge&logo=tryhackme&logoColor=white)](https://tryhackme.com)
[![Difficulty](https://img.shields.io/badge/Difficulty-Easy-brightgreen?style=for-the-badge&logo=shield&logoColor=white)]()
[![Technique](https://img.shields.io/badge/Technique-RCE%20%7C%20PHP%208.1.0--dev-blue?style=for-the-badge&logo=php&logoColor=white)]()

</div>

---

## 🕵️ Agent-Sudo — Full Walkthrough

> *"Something seems a little off with the server…"*
> — and it really was.

---

## Step 1 -- Lookup

So after opening the ip in the browser here is the first look of our target!

![look](https://miro.medium.com/v2/resize:fit:640/format:webp/1*NPumNJ_ELFgfmIRsqx2GIA.png)

Now it time for the First Questions.


It goes like 

| Quesiton | Hints
|------|--------------|
| How many open ports are there | Use Nmap |


For this here is the simple loolipoop nmap command to scan for open ports.


> nmap --top-ports 1000 <Ip of room>

Lets Went to our second Question that is 

| Quesiton | Hints |
|------|-------------|
| How Do you redirect Yourself to the secret Page? | Nothing |

Since we need a link that will somehow redirecting us to the secret page 


We just need to find that page(endpoint).

for this we are going to start directory enumuration.


But in our case the directory enumuration tool dirbuster didn't gave anything.



so we need to change our approch.

As it is being said in homepage of target is 

```
Dear agents,

Use your own codename as user-agent to access the site.

From,
Agent R
```

And to answer that question We need to use user agent with the value R

With this command below

> curl -v  -H "User-Agent: R" http://10.49.177.146

Now when we did it we got something special text that is 

```
What are you doing! Are you one of the 25 employees? If not, I going to report this incident
```


