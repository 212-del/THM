<div align="center">

<img src="https://assets.tryhackme.com/img/THMlogo.png" alt="TryHackMe Logo" width="220"/>

<br/><br/>

[![TryHackMe](https://img.shields.io/badge/TryHackMe-c4ptur3--th3--fl4g-red?style=for-the-badge&logo=tryhackme&logoColor=white)](https://tryhackme.com/room/c4ptur3th3fl4g)
[![Category](https://img.shields.io/badge/Category-Encoding%20%26%20Cryptography-blue?style=for-the-badge&logo=gnuprivacyguard&logoColor=white)]()
[![Difficulty](https://img.shields.io/badge/Difficulty-Easy-brightgreen?style=for-the-badge&logo=target&logoColor=white)]()

# 🔐 c4ptur3-th3-fl4g — Writeup

### *Encoding · Decoding · Steganography · Cipher Analysis*

</div>

---

> 💡 **Room Overview:** Unlike most TryHackMe rooms, this one does not require a machine IP. There is no active target to exploit — instead, the challenge is entirely text and file-based. Each question presents an encoded or obfuscated piece of data that you must decode to retrieve the flag.

---

## 📑 Table of Contents

| # | Question |
|:---:|:---|
| 1 | [🔤 Leet Speak](#-question-1--leet-speak) |
| 2 | [🖥️ Binary](#️-question-2--binary) |
| 3 | [🔡 Base32 / Encoded String](#-question-3--base32--encoded-string) |
| 4 | [📦 Base64](#-question-4--base64) |
| 5 | [🔢 Hexadecimal](#-question-5--hexadecimal) |
| 6 | [🔁 ROT13](#-question-6--rot13) |
| 7 | [🌀 ROT47](#-question-7--rot47) |
| 8 | [📡 Morse Code](#-question-8--morse-code) |
| 9 | [🔢 Decimal / ASCII](#-question-9--decimal--ascii) |
| 10 | [🧅 Multi-Layer Encoding](#-question-10--multi-layer-encoding) |
| 11 | [🎵 Spectrogram (Audio File)](#-question-11--spectrogram-audio-file) |
| 12 | [🖼️ Steghide (JPEG Steganography)](#️-question-12--steghide-jpeg-steganography) |
| 13 | [📂 Binwalk (Embedded Files)](#-question-13--binwalk-embedded-files) |
| 14 | [📁 Hidden Text in Archive](#-question-14--hidden-text-in-archive) |

---

## 🔤 Question 1 — Leet Speak

**Decode the following text:**

```
c4n y0u c4p7u23 7h3 f149?
```

This one is immediately readable to anyone who has spent time in hacker culture. **Leet speak** (also written as 1337 speak) is a substitution cipher where letters are replaced with visually similar numbers or symbols — `4` for `a`, `0` for `o`, `3` for `e`, `7` for `t`, and so on. Just reading it phonetically gives you the answer.

```
can you capture the flag?
```

---

## 🖥️ Question 2 — Binary

**Decode the following binary string:**

```
01101100 01100101 01110100 01110011 00100000 01110100 01110010 01111001 00100000 01110011 01101111 01101101 01100101 00100000 01100010 01101001 01101110 01100001 01110010 01111001 00100000 01101111 01110101 01110100 00100001
```

Each group of 8 bits (a byte) maps to a character in the ASCII table. You can use any binary-to-text converter — I used the **binary decoder** section in my [Cybersecurity Calculator](https://github.com/212-del/Cybersecurity-calculator) repository.

```
Redacted
```

---

## 🔡 Question 3 — Base32 / Encoded String

**Decode the following encoded text:**

```
MJQXGZJTGIQGS4ZAON2XAZLSEBRW63LNN5XCA2LOEBBVIRRHOM======
```

The first step is identifying the encoding type. Using the hash/encoding identifier in my [Cybersecurity Calculator](https://github.com/212-del/Cybersecurity-calculator), the tool flagged this as a potential hash. However, when we take a closer look at the character set — all uppercase letters, digits 2–7, and `=` padding — it is clearly **Base32**.

Since Base32 is a reversible encoding (not a one-way hash), decoding is straightforward. Paste it into a Base32 decoder or use a trusted online platform like **[hashes.com](https://hashes.com)** if you need to cross-reference it.

```
redacted
```

---

## 📦 Question 4 — Base64

**Decode the following encoded text:**

```
RWFjaCBCYXNlNjQgZGlnaXQgcmVwcmVzZW50cyBleGFjdGx5IDYgYml0cyBvZiBkYXRhLg==
```

The `==` padding at the end and the mixed alphanumeric character set immediately identify this as **Base64**. My [Cybersecurity Calculator](https://github.com/212-del/Cybersecurity-calculator) includes a Base64 decoder — paste it in and the plaintext comes right out.

```
Redacted
```

---

## 🔢 Question 5 — Hexadecimal

**Decode the following encoded text:**

```
68 65 78 61 64 65 63 69 6d 61 6c 20 6f 72 20 62 61 73 65 31 36 3f
```

Anyone with experience in low-level computing or networking will spot this instantly. The values are hexadecimal — each two-character group (0–9, a–f) represents a single byte. My [Cybersecurity Calculator](https://github.com/212-del/Cybersecurity-calculator) has a hex decoder built in.

```
redacted
```

---

## 🔁 Question 6 — ROT13

**Decode the following encoded text:**

```
Ebgngr zr 13 cynprf!
```

With a little patience and a good eye for patterns, you can spot that each letter has been shifted exactly 13 positions in the alphabet — that's the classic **ROT13** cipher. It is symmetric, meaning encoding and decoding use the same operation. My [Cybersecurity Calculator](https://github.com/212-del/Cybersecurity-calculator) includes a ROT13 decoder.

```
redacted
```

---

## 🌀 Question 7 — ROT47

**Decode the following encoded text:**

```
*@F DA:? >6 C:89E C@F?5 323J C:89E C@F?5 Wcf E:>6DX
```

This one is a bit trickier to identify by eye because **ROT47** rotates all 94 printable ASCII characters (from `!` to `~`), not just the 26 letters. The presence of symbols like `*`, `:`, and `>` mixed with regular-looking text is a useful giveaway. Since my [Cybersecurity Calculator](https://github.com/212-del/Cybersecurity-calculator) does not currently include a ROT47 option, I used an online ROT47 decoder to retrieve the result.

```
Redacted
```

---

## 📡 Question 8 — Morse Code

**Decode the following encoded text:**

```
- . .-.. . -.-. --- -- -- ..- -. .. -.-. .- - .. --- -.. -. -.-. --- -.. .. -. --.
```

Dashes and dots — **Morse code**. One of the most recognisable encodings in existence. My [Cybersecurity Calculator](https://github.com/212-del/Cybersecurity-calculator) already has a Morse code decoder built in. Paste it in and you have your answer.

```
Redacted
```

---

## 🔢 Question 9 — Decimal / ASCII

**Decode the following encoded text:**

```
85 110 112 97 99 107 32 116 104 105 115 32 66 67 68
```

When trying to identify the encoding type, a few things stand out:

```
All values fall in the range 32–126 → the printable ASCII range
Numbers are 2–3 digit integers separated by spaces
No 0x prefix (not hex), and no values are exclusively 0 or 1 (not binary)
```

These are plain **decimal ASCII values**. Convert each number to its corresponding ASCII character and you have the flag.

```
redacted
```

---

## 🧅 Question 10 — Multi-Layer Encoding

**Decode the following encoded text:**

```
LS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLi0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLS0tLS0gLi0tLS0gLi0tLS0gLi0tLS0gLi0tLS0gLi0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLi0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLi0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLS0tLS0gLi0tLS0gLi0tLS0gLi0tLS0gLi0tLS0gLi0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLS0tLS0gLi0tLS0gLi0tLS0gLi0tLS0gLi0tLS0gLi0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLS0tLS0gLi0tLS0gLi0tLS0gLi0tLS0gLi0tLS0gLi0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLi0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLi0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLS0tLS0gLi0tLS0gLi0tLS0gLi0tLS0gLi0tLS0gLi0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLi0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLS0tLS0gLi0tLS0gLi0tLS0gLi0tLS0gLi0tLS0gLi0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLi0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLi0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLi0tLS0gLi0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLS0tLS0gLi0tLS0gLi0tLS0gLi0tLS0gLi0tLS0gLi0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLi0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLi0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLi0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLi0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLi0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLi0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLi0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLi0tLS0KLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLS0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLi0tLS0KLS0tLS0gLi0tLS0gLi0tLS0gLS0tLS0gLS0tLS0gLi0tLS0gLS0tLS0gLi0tLS0=
```

This is a heavy one. The character set — `A–Z`, `a–z`, `0–9`, `+`, `/` — and the trailing `=` padding make it unmistakably **Base64**. After decoding with my [Cybersecurity Calculator](https://github.com/212-del/Cybersecurity-calculator), the output revealed a **Morse code** string. Decoding that Morse code gave **binary**. Decoding that binary revealed **ROT47**. Decoding the ROT47 produced **decimal ASCII values**. Finally, converting those ASCII values gave the plaintext flag.

Layer by layer:

```
Base64 → Morse Code → Binary → ROT47 → ASCII Decimal → Plain Text
```

---

## 🎵 Question 11 — Spectrogram (Audio File)

The room provides a `.wav` audio file and hints at **Audacity** — a popular open-source audio editor. Downloading and setting up Audacity can be a hassle, so I used the browser-based alternative instead:

```
https://wavacity.com/
```

Upload the audio file, then switch the waveform view to **Spectrogram** mode. The flag is hidden in the frequency spectrum and becomes visible as text when rendered as a spectrogram.

![Spectrogram showing the hidden flag](audio.png)

---

## 🖼️ Question 12 — Steghide (JPEG Steganography)

The room provides a `.jpg` file that has data hidden inside it using **steghide** — a command-line steganography tool that embeds secrets within image or audio files at the bit level, making the change visually undetectable.

To extract the hidden content, run:

```bash
steghide extract -sf file.jpg
```

When prompted for a passphrase, just press **Enter** (empty passphrase). The extracted data is saved as a text file in the current directory — and that file contains the flag.

---

## 📂 Question 13 — Binwalk (Embedded Files)

**Task:** Download the file and get 'inside' it. What is the first filename and extension?
*(Hint: Obscure Steg)*

This time we are looking for files embedded *inside* the image, rather than a passphrase-protected payload. **Binwalk** is the go-to tool for this — it scans a file for known file signatures and can extract any embedded content it finds.

```bash
binwalk -e meme_1559010886025.jpg
```

After extraction, a new folder is created named after the file. Inside that folder you will find the embedded files, and the first filename (with its extension) is your answer.

---

## 📁 Question 14 — Hidden Text in Archive

**Task:** Get inside the archive and inspect the file carefully. Find the hidden text.
*(Hint: Answer is case sensitive)*

After a fair amount of digging, the flag turned up using `xxd` — a hex dump utility. Inside the extracted archive folder is a `.rar` file, and the flag is embedded in the **tail end** of that file's raw bytes:

```bash
xxd <filename>.rar | tail
```

Scan those hex values carefully — the flag is hiding in plain sight among the raw data at the end of the file.

---

<div align="center">

---

*Room complete — all 14 challenges solved.* 🏁

[![TryHackMe](https://img.shields.io/badge/TryHackMe-LinuxX-red?style=for-the-badge&logo=tryhackme&logoColor=white)](https://tryhackme.com/p/LinuxX)
[![GitHub](https://img.shields.io/badge/GitHub-212--del-black?style=for-the-badge&logo=github&logoColor=white)](https://github.com/212-del)

*Made with ❤️ and a lot of late-night decoding sessions.*

</div>
