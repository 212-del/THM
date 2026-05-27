After getting to the room there were no ip given this time as like each time while this time we are being served with a domain

That is marvenly.com

Here is our attempt to find the live target.

for it first we tried to do the DNS resoultion Test

With the commadns 

nslookup marvenly.com && dig marvenly.com

But the both didn't gave me a satisfactory result.

Both failed to find a live host.

tHEN I tried opening the attackbox

In the hope that in attackbox the target will opens and will not gave me the error 

'DNS_PROBE_POSSIBLE'

Next i tried to test the VPN routes with the command *ip route*

Then it gave me the range in which the target could possibly exist

With that command i found the network address/subnet. That was 

10.48.0.0/12


We are now going to check for live host in that entire vpn subnet.

With nmap : nmap -sn 10.48.0.0/24.

But this too did't worked even with 2 ip ranges that are below embedded into the commands 

nmap -sn 10.49.0.0/24
nmap -sn 10.48.0.0/24

So i went into the censys And search for that domain 

And i found 2 subdomains for it

- uat-testing.marvenly.com
- admin.marvenly.com

And as per the 1st question asked too that 

What is the subdomain where the development version of the website is hosted?

So i submit uat-testing.marvenly.com as a answer to this question and it gets corrected.

As the 2nd question was asking about the github that is 

What is the GitHub username of the developer?

So i searched the term "marvenly github"  and got this result.

![reuslt](result.png)

The 2nd result has more interaction and name matches with it perfectly so i opened it.

As i opened i got its username and when i entered that username.

It got acceped meaningly my guess was correct and this is the one that we were finding.

For the next question it was asking that 

What is the developer's email address?

And the email is yet used to commit in a github repo.

If we are viewing any commit just we need to add .patch at the end of url.

And you will get much more detail about that commit even that email too.

and when i got the email from this trick and submit there for the answer of the quesiton

What is the developer's email address?

My answer got accepted.

We're moving to our next question that is 

What reason did the developer mention in the commit history for removing the source code?

We have 4 commits and after checking each commits with the diff window in commit tab.

I got that most recent tab in which source code was not there was the commit

88baf1d

And this commit has the messege that 

The project was marked as abandoned due to a payment dispute

And this messege seems the perfect answer to the question 

What reason did the developer mention in the commit history for removing the source code?

It is also get accepeted as right answer of the question too.

And there was another commit before this commit into that commit

Develooper has leaved messege that he is removing his signatures and in the signatures windows 

The flag was contained as a html comment.

And in this way we got out last flag.

