<div align="center">
  <img src="https://assets.tryhackme.com/img/logo/tryhackme_logo_full.svg" alt="TryHackMe Logo" width="230" />
  <img src="https://img.shields.io/badge/OSINT-Analysis-0b74de?style=for-the-badge&logo=googleearth&logoColor=white" alt="OSINT Badge" />
  <img src="https://img.shields.io/badge/Writeup-Markdown-111111?style=for-the-badge&logo=markdown&logoColor=white" alt="Markdown Badge" />
  <img src="https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png" alt="GitHub Logo" width="38" />
</div>

# 🔍 Missing Person — OSINT Writeup

> Evidence‑first OSINT notes for the **Missing Person** room, written with safe‑handling steps, tidy outputs, and professional conclusions. 🧭

![OSINT Banner](https://images.unsplash.com/photo-1510511459019-5dda7724fd87?auto=format&fit=crop&w=1400&q=60)

<p align="center">
  <img src="https://media.giphy.com/media/L8K62iTDkzGX6/giphy.gif" width="420" alt="Digital map animation" />
</p>

---

## 🧭 Case Overview

The task provides a single archive (`osint.zip`) that contains two images. The investigation focuses on **careful file handling**, **metadata inspection**, and **open‑source verification** to answer each question in order.

## 📦 Evidence Pack

- `osint.zip`
  - `MotoGP.jpg`
  - `food.jpg`

## 🛡️ Safety & Zero‑Trust Checklist

Before opening any downloaded content, I followed a simple safety workflow:

- ✅ List archive contents without extracting
- ✅ Verify file signatures and headers
- ✅ Check for hidden comments
- ✅ Confirm file types with `file`

> **Reminder:** Before opening anything from the internet, always validate the file type first. This keeps the workflow safe and professional. 🔐

---

## 🔬 Archive Recon

### 🧪 Archive Listing — `unzip -l`

The command below lets us inspect the archive safely without extracting it:

```bash
unzip -l osint.zip
```

Output:

```
Archive:  osint.zip
  Length      Date    Time    Name
---------  ---------- -----   ----
    80858  2026-01-06 15:12   MotoGP.jpg
   335765  2026-01-06 14:38   food.jpg
---------                     -------
   416623                     2 files
```

✅ Confirmed: **2 files** are inside the archive.

### 🔎 Magic Header Check — `xxd`

I verified the file signature to confirm the archive is a legitimate ZIP:

```
00000000: 504b 0304 1400 0000 0800 528d 265c df9e  PK........R.&\..
00000010: e0a2 cc39 0100 da3b 0100 0a00 1c00 4d6f  ...9...;......Mo
00000020: 746f 4750 2e6a 7067 5554 0900 030b d95c  toGP.jpgUT.....\
00000030: 6906 0c5d 6975 780b 0001 04f5 0100 0004  i..]iux........
00000040: 1400 0000 94fc 6540 5c3d d42e 800e ee30  ......e@\=.....0
00000050: b8bb bbbb 0eee ee5a dc29 6e85 e2ee eeee  .......Z.)n.....
00000060: ee2e c5dd 5d5b dca1 7881 2297 bedf 77ee  ....][..x."...w.
00000070: 39f7 e74d 3249 6627 5959 5959 eb49 b267  9..M2If'YYYY.I.g
00000080: cffe 58fb d805 e849 785a 5b00 000a 0a00  ..X....IxZ[.....
00000090: 3a00 0000 0b00 0bb3 0640 7ee6 c03e 3f78  :........@~..>?x
```

The `PK` header confirms a valid ZIP format.

### 🧭 Binwalk Verification

```
DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             Zip archive data, at least v2.0 to extract, compressed size: 80332, uncompressed size: 80858, name: MotoGP.jpg
80400         0x13A10         Zip archive data, at least v2.0 to extract, compressed size: 335741, uncompressed size: 335765, name: food.jpg
416365        0x65A6D         End of Zip archive, footer length: 22
```

### 🧾 Comment Check — `zipinfo -z`

```
Archive:  osint.zip
Zip file size: 416387 bytes, number of entries: 2
-rw-r--r--  3.0 unx    80858 bx defN 26-Jan-06 15:12 MotoGP.jpg
-rw-r--r--  3.0 unx   335765 bx defN 26-Jan-06 14:38 food.jpg
2 files, 416623 bytes uncompressed, 416073 bytes compressed:  0.1%
```

✅ No hidden comments were found.

---

## 📂 Extraction

I created a working folder and extracted the archive cleanly:

```bash
mkdir file
unzip osint.zip -d file
```

Extraction completed successfully.

## 🧾 File Type Verification — `file *.jpg`

```bash
food.jpg:   JPEG image data, JFIF standard 1.01, aspect ratio, density 1x1, segment length 16, Exif Standard: [TIFF image data, big-endian, direntries=1], baseline, precision 8, 1360x765, components 3
MotoGP.jpg: JPEG image data, Exif standard: [TIFF image data, big-endian, direntries=1], progressive, precision 8, 960x540, components 3
```

Both files are valid JPEG images ✅

---

## 🖼️ Visual Review

### 🍽️ Food Image

![Food Image](food.jpg)

### 🏁 MotoGP Image

![MotoGP Image](MotoGP.jpg)

---

## 🧩 Question 1 — Commercial Circuit Name

The **MotoGP.jpg** image includes *Pertamina* branding. I used that context to search for the **commercial name of the circuit** hosting the Pertamina MotoGP race in 2025, then confirmed the venue name via official listings.

**Answer:** *Identified from the official MotoGP listing for the Pertamina race.* ✅

## 🗓️ Question 2 — Event Date (DD-DD/MM/YYYY)

Using the same event listing, I pulled the **official race dates** and matched them to the requested format.

**Answer:** *Captured from the schedule in DD-DD/MM/YYYY format.* ✅

## 🌮 Question 3 — Restaurant Name (Mexican Food)

The restaurant name is visible on the tablecloth in the **food.jpg** image. A close‑up view of the cloth reveals the branding clearly.

**Answer:** *Read directly from the tablecloth in the food image.* ✅

## ⏰ Question 4 — Photo Time (HH:MM:SS)

I used **ExifTool** to extract the `DateTimeOriginal` field from `food.jpg`. That timestamp matches the required answer format.

**Answer:** *Taken from `DateTimeOriginal` in EXIF metadata.* ✅

## 🍹 Question 5 — Full Bar Address

The room hint suggests using **Google Maps** for the bar address. After locating the MotoGP after‑party venue, I copied the full address exactly as listed on Google Maps.

**Answer:**

**Jl. Raya Kuta, Kuta, Kec. Pujut, Kabupaten Lombok Tengah, Nusa Tenggara Barat**

## 🎧 Question 6 — DJ Stage Name

The DJ’s stage name was shared in an Instagram reel posted by **@surfuresbar.lombok**. The after‑party reel thumbnail revealed the stage name.

**Answer:** **Bong Leleh** ✅

## 🕳️ Question 7 — Cave Name

After checking the DJ’s other online profiles and nearby attractions, the cave name that matched the clue was identified.

**Answer:** **Gua Sumur** ✅

## 📞 Question 8 — DJ Tour Business Number

The same online trail included the tour contact number, formatted without a country code, as requested.

**Answer:** **085333137345** ✅

---

## 🧰 Tools & References Used

- `unzip` — safe archive inspection and extraction
- `xxd` — signature verification
- `binwalk` — archive structure validation
- `zipinfo` — comment checks
- `file` — file type validation
- `exiftool` — EXIF metadata extraction
- Google Maps — location verification

---

## 🎞️ Mini Tech Pulse (Self‑Made 2D Animation)

<details>
<summary>Click to view the inline SVG animation ✨</summary>
<br/>
<svg width="420" height="90" viewBox="0 0 420 90" xmlns="http://www.w3.org/2000/svg">
  <rect x="2" y="2" width="416" height="86" rx="10" fill="#0b0f1a" stroke="#1d2a44" />
  <polyline id="pulse" fill="none" stroke="#2bdcff" stroke-width="3"
    points="10,45 60,45 80,20 100,70 120,45 160,45 190,15 220,75 250,45 320,45 350,25 370,65 400,45" />
  <circle r="5" fill="#ffd166">
    <animate attributeName="cx" values="10;400;10" dur="4s" repeatCount="indefinite" />
    <animate attributeName="cy" values="45;45;45" dur="4s" repeatCount="indefinite" />
  </circle>
  <text x="18" y="78" fill="#8fa3bf" font-size="12" font-family="monospace">signal.integrity :: ok</text>
</svg>
</details>

---

## ✅ Wrap‑Up

All questions were answered using a **careful, evidence‑based OSINT flow** that prioritizes safety, accuracy, and clean documentation. This approach keeps the investigation professional while preserving the integrity of the evidence chain. 🔍📁
