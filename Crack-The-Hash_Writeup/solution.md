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

Now lets goto out third hash that is *$2y$12$Dwt1BZj6pcyc3Dy1FWZ5ieeUznr71EeNkJkUlypTsgbX1H68wsRom*

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
