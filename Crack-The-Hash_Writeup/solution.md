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
