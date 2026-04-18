## Level 1

### Hash 1 — MD5

The hint for the first hash already tells us it's an MD5 hash. The hash is:

```
48bb6e862e54f2a795ffc4e541caed4d
```

We can search for any MD5 hash decoder online, enter this hash, and get the decoded result.

**easy**

---

### Hash 2 — SHA-1

The hint for the second hash says *Sha... But which version?*

We will now analyse the hash `CBFDAC6008F9CAB4083784CBD1874F76618D2A97`.

```
The key clue is length.

This hash is 40 hexadecimal characters.
Each hex character = 4 bits → 40 × 4 = 160 bits

That matches SHA-1, which produces 160-bit hashes (40 hex chars).
```

I searched online for a SHA-1 decoder and fed it the hash `CBFDAC6008F9CAB4083784CBD1874F76618D2A97`.

This gave the decoded result: *password123*

---

### Hash 3 — SHA-256

The hint for the third hash also says *Sha..*, similar to the previous one, so we need to analyse the hash again: `1C8BFE8F801D79745C4631D09FFF36C82AA37FC4CCE4FC946683D7B336B63032`

```
The key clue is length.

This hash is 64 hexadecimal characters.
Each hex character = 4 bits → 64 × 4 = 256 bits

That matches SHA-256, which produces 256-bit hashes (64 hex chars).
```

Again, we search online for a SHA-256 decoder and decode it.

The decoded result was *letmein*

---

### Hash 4 — bcrypt

The fourth hash is `$2y$12$Dwt1BZj6pcyc3Dy1FWZ5ieeUznr71EeNkJkUlypTsgbX1H68wsRom`

Its hint says:

```
Search the hashcat examples page (https://hashcat.net/wiki/doku.php?id=example_hashes) for $2y$. This type of hash can take a very long time to crack, so either filter rockyou for four character words, or use a mask for four lower case alphabetical characters.
```

The hint points us to the Hashcat examples page to identify the hash type. Here's a breakdown of what each part of the hash means:

```
What each part means:

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

Searching for an online bcrypt decoder, most tools won't crack it directly since bcrypt is intentionally slow by design. Visiting hashes.com and submitting the hash gives us the answer: *bleh*

---

## Level 2

### Hash 5 — MD4

The fifth hash is `279412f945939ba78ce0758d3fd83daa`

The hint already tells us it is MD4, so we can enter it on hashes.com.

After doing so, it gave the result: *Eternity22*

---

### Hash 6 — SHA-256

The sixth hash is `F09EDCB1FCEFC6DFB23DC3505A882655FF77375ED8AA2D1C13F640FCCC2D0C85`

Since there's no hint, we need to identify the hash type first.

```
The key clue is length.

This hash is 64 hexadecimal characters.
Each hex character = 4 bits → 64 × 4 = 256 bits

That matches SHA-256, which produces 256-bit hashes (64 hex chars).
```

We can look it up on hashes.com, which gives us the decoded result: *paule*

---

### Hash 7 — NTLM

The seventh hash is `1DFECA0C002AE40B8619ECF94819CC1B`

The hint tells us it's an NTLM hash, so we can use hashes.com directly.

This gives us the decoded result: *n63umy8lkf4i*

---

### Hash 8 — SHA-512crypt

The eighth hash is `$6$aReallyHardSalt$6WKUTqzq.UQQmrm0p/T7MPpMbGNnzXPMAXi4bJMl9be.cfi3/qxIf.hsGpS41BqMhSrHVXgMpdjS6xeKZAs02.*`

It also comes with a salt: *aReallyHardSalt*

We submit it to hashes.com in the format:
`$6$aReallyHardSalt$6WKUTqzq.UQQmrm0p/T7MPpMbGNnzXPMAXi4bJMl9be.cfi3/qxIf.hsGpS41BqMhSrHVXgMpdjS6xeKZAs02.:aReallyHardSalt`

This gives us the decoded result: *waka99*

In fact, the salt is not required for the submission to work.

---

### Hash 9 — HMAC-SHA1

The ninth hash is `e5d8870e5bdd26602cab8dbe07a942c8669e56d6` with the salt *tryhackme*

Entering the hash into hashes.com reveals the decoded text: *481616481616*
