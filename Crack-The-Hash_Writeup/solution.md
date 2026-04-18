for the first hash its hint already says that its a md5 hash. the hash is 

```
48bb6e862e54f2a795ffc4e541caed4d
```

Now we can search for any md5 hash decoder and enter this hash and we will get the decoded result.

**easy**

For the second hash its hint says *Sha..But which version*

We will now analyse the hash *CBFDAC6008F9CAB4083784CBD1874F76618D2A97*

```
The key clue is length.

This hash is 40 hexadecimal characters
Each hex character = 4 bits → 40 × 4 = 160 bits

That matches SHA-1, which produces 160-bit hashes (40 hex chars).
```

Now i seached onlinne for sha1 decoder and feed it the sha 1 hash *CBFDAC6008F9CAB4083784CBD1874F76618D2A97*

This gave me the decoded result *password123*


Now lets go onto the 3rd hash its hint says *Sha..*

But it is similar like the above one so we need to ananlyse the hash again *1C8BFE8F801D79745C4631D09FFF36C82AA37FC4CCE4FC946683D7B336B63032*

```
 The key clue is length.

 This hash is 64 hexadecimal characters
 Each hex character = 4 bits → 64 × 4 = 256 bits

 That  matches SHA-256, which produces 256-bit hashes (64 hex chars).
```

Again we will seach onlien for sha 256 decoder and decode it online.

The decoded result was *letmein*

Now lets goto out fourth hash that is *$2y$12$Dwt1BZj6pcyc3Dy1FWZ5ieeUznr71EeNkJkUlypTsgbX1H68wsRom*

And its hint says 

```
Search the hashcat examples page (https://hashcat.net/wiki/doku.php?id=example_hashes) for $2y$. This type of hash can take a very long time to crack, so either filter rockyou for four character words, or use a mask for four lower case alphabetical characters.
```

The hint is saying to analyse the hash from that specific url but we gonna use the chatgpt for analysign that hash.

```
What each part means
$2y$
This indicates the bcrypt algorithm variant.
2y is commonly used in PHP implementations of bcrypt.
12$
This is the cost factor (work factor).
It means the hashing process runs 2^12 = 4096 iterations.
Higher = slower but more secure.
Dwt1BZj6pcyc3Dy1FWZ5ie (22 chars)
This is the salt.
Random data added to the password before hashing.
Ensures that identical passwords produce different hashes.
eUznr71EeNkJkUlypTsgbX1H68wsRom
This is the actual hashed output.
```


So now we will seach for online bcrypt online decoder.

But as per my search i cant find any online tool for it.

Then i did chatgpt and it told me to visit hashses.com and there i hit that hash and got the answer *bleh*


Now Lets go to our fifth hash *279412f945939ba78ce0758d3fd83daa*

Its hint already says that it is md4 so we can enter it in the same website hashes.com. 

After doing the same it gave me the result *Eternity22*

Now lets goto our sixth hash *F09EDCB1FCEFC6DFB23DC3505A882655FF77375ED8AA2D1C13F640FCCC2D0C85*

Since we dont know the hash and it has also no hint we will first find what it is.

```
  The key clue is length.

  This hash is 64 hexadecimal characters
  Each hex character = 4 bits → 64 × 4 = 256 bits

That  matches SHA-256, which produces 256-bit hashes (64 hex chars).
```

For its decoding too we can refer to hashes.com and that will gave us the decoded result *paule*

For the seventh hash *1DFECA0C002AE40B8619ECF94819CC1B*

We already have the hint available that its a NTLM. and we can do it with the same website hashes.com

And this will gives us the decoded hash *n63umy8lkf4i*

Now  lets goto our eighth hash *$6$aReallyHardSalt$6WKUTqzq.UQQmrm0p/T7MPpMbGNnzXPMAXi4bJMl9be.cfi3/qxIf.hsGpS41BqMhSrHVXgMpdjS6xeKZAs02.*

It has a salt too *aReallyHardSalt*

Now lets enter it to the website hashes.com in the format $6$aReallyHardSalt$6WKUTqzq.UQQmrm0p/T7MPpMbGNnzXPMAXi4bJMl9be.cfi3/qxIf.hsGpS41BqMhSrHVXgMpdjS6xeKZAs02.:aReallyHardSalt

And this will gave us the decoded result of it *waka99*

Infact it doesn't requires the salt.

Now lets moveon to our nineth hash *e5d8870e5bdd26602cab8dbe07a942c8669e56d6* with the salt *tryhackme*

Now we will enter the hash into the same website and this reveals the decoded text *481616481616*


