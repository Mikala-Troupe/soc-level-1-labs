# Week 3 — Email Content Analysis Notes 💬

## Overview
This week I practiced analyzing the **email body and content** to identify phishing tactics. I looked at MIME structure, HTML vs plaintext content, brand spoofing, grammar clues, urgency triggers, and decoding encoded text (Base64, HTML entities, URL encoding). The goal was to understand how attackers hide malicious content and trick users into reacting emotionally.

---

## 📌 Email Content Review

### MIME + HTML Inspection
![Screenshot 1 – Original phishing email](https://github.com/Mikala-Troupe/Soc-Level-1-Labs/blob/0ec9598347adeb6edb70a80c27ccdfb8587e3eeb/Week-03_Email-Content-Analysis/Week-03_Email-Content-Analysis/screenshots/Lab3SS1.png)  
**Screenshot 1** — Initial phishing email view.

![Screenshot 2 – Headers / MIME structure](https://github.com/Mikala-Troupe/Soc-Level-1-Labs/blob/0ec9598347adeb6edb70a80c27ccdfb8587e3eeb/Week-03_Email-Content-Analysis/Week-03_Email-Content-Analysis/screenshots/Lab3SS2.png)  
**Screenshot 2 – MIME analysis in Sublime**
- `MIME-Version` → email uses MIME structure
- `Content-Type` → HTML content type
- `Content-Transfer-Encoding` → `7bit` here (no encoding applied)
- Boundary strings separate email parts (HTML vs plaintext)
- This email only had HTML, no plaintext version (common in phishing)

![Screenshot 3 – Plaintext & HTML parts](https://github.com/Mikala-Troupe/Soc-Level-1-Labs/blob/0ec9598347adeb6edb70a80c27ccdfb8587e3eeb/Week-03_Email-Content-Analysis/Week-03_Email-Content-Analysis/screenshots/Lab3SS3.png)  
![Screenshot 4 – Dual content example](https://github.com/Mikala-Troupe/Soc-Level-1-Labs/blob/0ec9598347adeb6edb70a80c27ccdfb8587e3eeb/Week-03_Email-Content-Analysis/Week-03_Email-Content-Analysis/screenshots/Lab3SS4.png)  
**Screenshots 3 & 4** – Example showing both plaintext + HTML versions

---

### 📌 Branding & Sender Mismatch
![Screenshot 5 – Trustwallet spoof email](https://github.com/Mikala-Troupe/Soc-Level-1-Labs/blob/0ec9598347adeb6edb70a80c27ccdfb8587e3eeb/Week-03_Email-Content-Analysis/Week-03_Email-Content-Analysis/screenshots/Lab3SS5.png)  
**Screenshot 5** – Email pretending to be from "Trustwallet"

![Screenshot 6 – Legit brand check](https://github.com/Mikala-Troupe/Soc-Level-1-Labs/blob/0ec9598347adeb6edb70a80c27ccdfb8587e3eeb/Week-03_Email-Content-Analysis/Week-03_Email-Content-Analysis/screenshots/Lab3SS6.png)  
**Screenshot 6** – Googling real brand (“Trust Wallet” vs “Trustwallet”)
- Missing space = subtle spoofing clue
- Real company is **Trust Wallet**

---

### 📌 Grammar, Tone, and Urgency Cues
![Screenshot 7 – Greeting + formatting issues](https://github.com/Mikala-Troupe/Soc-Level-1-Labs/blob/0ec9598347adeb6edb70a80c27ccdfb8587e3eeb/Week-03_Email-Content-Analysis/Week-03_Email-Content-Analysis/screenshots/Lab3SS7.png)  
**Screenshot 7**
- Generic greeting ("Customer")
- Odd punctuation & formatting

![Screenshot 8 – Urgent tone](https://github.com/Mikala-Troupe/Soc-Level-1-Labs/blob/0ec9598347adeb6edb70a80c27ccdfb8587e3eeb/Week-03_Email-Content-Analysis/Week-03_Email-Content-Analysis/screenshots/Lab3SS8.png)  
**Screenshot 8**
- Strong urgency — pressure to act fast

![Screenshot 9 – Grammar errors](https://github.com/Mikala-Troupe/Soc-Level-1-Labs/blob/0ec9598347adeb6edb70a80c27ccdfb8587e3eeb/Week-03_Email-Content-Analysis/Week-03_Email-Content-Analysis/screenshots/Lab3SS9.png)  
**Screenshot 9** — Another Trust Wallet phish  
- Grammar issues: `NFT's` → `NFTs`, `assistence` → `assistance`
- Still, AI now helps attackers write clean emails, so grammar alone isn’t enough

![Screenshot 10 – Well-designed phishing email](https://github.com/Mikala-Troupe/Soc-Level-1-Labs/blob/0ec9598347adeb6edb70a80c27ccdfb8587e3eeb/Week-03_Email-Content-Analysis/Week-03_Email-Content-Analysis/screenshots/Lab3SS10.png)  
**Screenshot 10**
- Very convincing Amazon-style phishing message
- Real branding & layout copied
- Shows why **content alone is not enough**, must also review headers & links

---

### 📌 Base64 Encoding & Obfuscation
![Screenshot 11 – Base64 encoded content](https://github.com/Mikala-Troupe/Soc-Level-1-Labs/blob/0ec9598347adeb6edb70a80c27ccdfb8587e3eeb/Week-03_Email-Content-Analysis/Week-03_Email-Content-Analysis/screenshots/Lab3SS11.png)  
**Screenshot 11**
- Email body was Base64 encoded
- Used to encode binary data or hide content

![Screenshot 12 – Decoding Base64 in CyberChef](https://github.com/Mikala-Troupe/Soc-Level-1-Labs/blob/0ec9598347adeb6edb70a80c27ccdfb8587e3eeb/Week-03_Email-Content-Analysis/Week-03_Email-Content-Analysis/screenshots/Lab3SS12.png)  
**Screenshot 12**
- Decoded base64 → revealed HTML + CSS markup

![Screenshot 13 – HTML entities](https://github.com/Mikala-Troupe/Soc-Level-1-Labs/blob/0ec9598347adeb6edb70a80c27ccdfb8587e3eeb/Week-03_Email-Content-Analysis/Week-03_Email-Content-Analysis/screenshots/Lab3SS13.png)  
**Screenshot 13**
- Attackers encode HTML characters (like `<` and `&`)
- Helps bypass weak filters & hide malicious strings

---

### 📌 URL Encoding & Multi-layer Encoding
![Screenshot 14 – URL encode in CyberChef](https://github.com/Mikala-Troupe/Soc-Level-1-Labs/blob/0ec9598347adeb6edb70a80c27ccdfb8587e3eeb/Week-03_Email-Content-Analysis/Week-03_Email-Content-Analysis/screenshots/Lab3SS14.png)  
**Screenshot 14**
- URL encoded characters using `%` + hex values

![Screenshot 15 – Encoded malicious link found](https://github.com/Mikala-Troupe/Soc-Level-1-Labs/blob/0ec9598347adeb6edb70a80c27ccdfb8587e3eeb/Week-03_Email-Content-Analysis/Week-03_Email-Content-Analysis/screenshots/Lab3SS15.png)  
**Screenshot 15**
- Found encoded string in phishing email
- Searched for `%2E` to identify encoded domain

![Screenshot 16 – Quoted-printable encoding](https://github.com/Mikala-Troupe/Soc-Level-1-Labs/blob/0ec9598347adeb6edb70a80c27ccdfb8587e3eeb/Week-03_Email-Content-Analysis/Week-03_Email-Content-Analysis/screenshots/Lab3SS16.png)  
**Screenshot 16**
- `Content-Transfer-Encoding: quoted-printable`
- Multiple encoding layers used to hide URL

![Screenshot 17 – Decoded final malicious URL](https://github.com/Mikala-Troupe/Soc-Level-1-Labs/blob/0ec9598347adeb6edb70a80c27ccdfb8587e3eeb/Week-03_Email-Content-Analysis/Week-03_Email-Content-Analysis/screenshots/Lab3SS17.png)  
**Screenshot 17**
- Fully decoded link using CyberChef (Quoted-Printable + HTML entity + URL decode)

---

## 📌 Key Takeaways
- HTML emails can hide scripts, redirects, and obfuscated text
- Grammar + formatting errors are useful but not definitive
- Urgency and generic greetings are strong phishing indicators
- Attackers use:
  - **Base64 encoding**
  - **HTML entity encoding**
  - **URL encoding**
  - **Quoted-printable encoding**
- Always decode suspicious text and inspect all layers
- Content review should be paired with:
  - Header analysis
  - Sender/domain checks
  - URL inspection

---

## 📌 Concepts Introduced
| Concept | Meaning / Use |
|--------|----------------|
MIME structure | Defines how email content is formatted (plaintext vs HTML)  
Base64 | Encodes data in ASCII (commonly abused to hide scripts)  
HTML entities | Encodes characters like `< > &` to hide HTML content  
URL encoding | Converts special characters into `%xx` values (used for obfuscation)  
Quoted-Printable | Encoding method often used to hide malicious URLs  
HTML phishing tricks | Fake forms, hidden links, embedded scripts  

---

## 📌 Skills Learned
- Identifying phishing red flags in email bodies
- Understanding MIME parts & boundary strings
- Recognizing branding mismatches & spoof patterns
- Spotting urgency-based social engineering
- Decoding **Base64**, **URL encoding**, and **HTML entities**
- Using CyberChef to decode multi-layered obfuscation
- Reading email body HTML & spotting suspicious elements
- Differentiating plaintext vs HTML signals
- Knowing that **context + headers + URLs** matter (not just content)





