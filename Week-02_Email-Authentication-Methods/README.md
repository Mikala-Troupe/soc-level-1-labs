# Week 2 — Email Authentication Methods ✉️

## Overview
This week I learned how **SPF**, **DKIM**, and **DMARC** work together to verify that emails actually come from who they claim to — and how to check them manually using tools like `nslookup`, `dig`, and MXToolbox.

---

### Sender Policy Framework (SPF)
SPF lets domain owners specify which mail servers are allowed to send email for their domain.

![Screenshot 1 – SPF nslookup example](https://github.com/Mikala-Troupe/soc-level-1-labs/blob/051ea1c2d252a0baef73ddd27fb5d829b99d9482/Week-02_Email-Authentication-Methods/Week-02_Email-Authentication-Methods/screenshots/Lab2SS1.png)  
- Used `nslookup` on **shodan.io** as an example.  
- `v=spf1` → version indicator.  
- `ip4:216.117.2.180 ip4:69.72.37.146` → authorized sending IPs.  
- `include:spf.google.com` → allows Google’s mail servers to send mail for the domain.  
- `-all` → hard fail (policy = reject unauthorized senders).  
- `~all` → soft fail (accept but treat with suspicion).  

---

### DomainKeys Identified Mail (DKIM)
DKIM is used to verify that an email was really sent from the domain it claims and that the message wasn’t changed in transit.  
It does this through **Public Key Infrastructure (PKI)**, where a private key signs the email and a public key (stored in DNS) verifies it.

- Confirms message integrity and domain ownership.
- Doesn’t guarantee intent, a malicious actor could still register a look-alike domain and configure DKIM/SPF to pass.
- Example: an attacker could register **g00gle-mail.com** and pass DKIM checks, even though it’s fake.

---

### Domain-based Message Authentication, Reporting & Conformance (DMARC)
DMARC builds on SPF and DKIM to improve email validation and reporting.

- Lets domain owners define what happens if messages fail SPF or DKIM.  
  - Policy options: `none`, `quarantine`, or `reject`.
- Provides reports to domain owners for visibility.
- Adds another layer of control, aligning the **From** domain with the results of SPF/DKIM.

---

## Email Analysis

Below is the breakdown of my hands-on analysis and what I observed at each step.

---

![Screenshot 2 – Email opened in Thunderbird](https://github.com/Mikala-Troupe/soc-level-1-labs/blob/051ea1c2d252a0baef73ddd27fb5d829b99d9482/Week-02_Email-Authentication-Methods/Week-02_Email-Authentication-Methods/screenshots/Lab2SS2.png)  
**Screenshot 2** – Opened the email sample in Mozilla Thunderbird to view headers and message details.

---

![Screenshot 3 – Email opened in Sublime Text](https://github.com/Mikala-Troupe/soc-level-1-labs/blob/051ea1c2d252a0baef73ddd27fb5d829b99d9482/Week-02_Email-Authentication-Methods/Week-02_Email-Authentication-Methods/screenshots/Lab2SS3.png)  
**Screenshot 3** – Viewed raw headers in Sublime.  
- The **Received** header shows when and where the message was handed off.  
- The boxed IP address can’t be spoofed because it reflects a real TCP connection between mail servers.  

---

![Screenshot 4 – WHOIS lookup](https://github.com/Mikala-Troupe/soc-level-1-labs/blob/051ea1c2d252a0baef73ddd27fb5d829b99d9482/Week-02_Email-Authentication-Methods/Week-02_Email-Authentication-Methods/screenshots/Lab2SS4.png)  
**Screenshot 4** – Used DomainTools WHOIS on IP address `149.72.142.11`.  
- **SENDGRID** → legitimate marketing platform (commonly abused).  
- **namecheap.com** → appears in reverse DNS (translates an IP address back into a domain name) results.

---

![Screenshot 5 – SPF pass](https://github.com/Mikala-Troupe/soc-level-1-labs/blob/051ea1c2d252a0baef73ddd27fb5d829b99d9482/Week-02_Email-Authentication-Methods/Week-02_Email-Authentication-Methods/screenshots/Lab2SS5.png)  
**Screenshot 5** – Email displayed in Sublime showing SPF: ~pass.

---

![Screenshot 6 – dig SPF lookup](https://github.com/Mikala-Troupe/soc-level-1-labs/blob/051ea1c2d252a0baef73ddd27fb5d829b99d9482/Week-02_Email-Authentication-Methods/Week-02_Email-Authentication-Methods/screenshots/Lab2SS6.png)  
**Screenshot 6** – Used `dig` with `grep -i spf` to check the SPF record for **mailserviceemailout1.namecheap.com**.

---

![Screenshot 7 – SPF record for SendGrid](https://github.com/Mikala-Troupe/soc-level-1-labs/blob/051ea1c2d252a0baef73ddd27fb5d829b99d9482/Week-02_Email-Authentication-Methods/Week-02_Email-Authentication-Methods/screenshots/Lab2SS7.png)  
**Screenshot 7** – Queried **sendgrid.net** using `dig`.  
- Found authorized IP address range: `149.72.0.0/16`. This is a cider block containing all of the IP addresses in the range of `149.72.0.0 - 149.72.255.255`.   
- The sender IP address `149.72.142.11` falls within this range → SPF **passed**.

---

### In Summary (SPF)
- The email originated from `o22.mailservice.namecheap.com` (`149.72.142.11`), which is authorized in the domain’s SPF record.  
- The SPF record includes `sendgrid.net`, confirming emails can be sent through SendGrid’s infrastructure.  
- Google’s receiving mail server verified and accepted the message.

---

![Screenshot 8 – Two DKIM signatures](https://github.com/Mikala-Troupe/soc-level-1-labs/blob/051ea1c2d252a0baef73ddd27fb5d829b99d9482/Week-02_Email-Authentication-Methods/Week-02_Email-Authentication-Methods/screenshots/Lab2SS8.png)  
**Screenshot 8** – Found two DKIM signatures: one for **Namecheap** and one for **SendGrid**.  
- Both use the same body hash and header fields, showing the message passed through multiple mail systems. Each applying its own DKIM signature.

---

![Screenshot 9 – DKIM signature breakdown](https://github.com/Mikala-Troupe/soc-level-1-labs/blob/051ea1c2d252a0baef73ddd27fb5d829b99d9482/Week-02_Email-Authentication-Methods/Week-02_Email-Authentication-Methods/screenshots/Lab2SS9.png)  
**Screenshot 9** – Analyzing DKIM header fields:  
- `v=1` → version  
- `a=rsa-sha256` → signature algorithm  
- `c=relaxed/relaxed` → allows minor whitespace or line changes without invalidating signature  
- `d=namecheap.com  s=s1` → domain and selector (used to find public key)  
- `bh` → body hash (base64-encoded)  
- `b` → DKIM signature value

---

![Screenshot 10 – nslookup DKIM record](https://github.com/Mikala-Troupe/soc-level-1-labs/blob/051ea1c2d252a0baef73ddd27fb5d829b99d9482/Week-02_Email-Authentication-Methods/Week-02_Email-Authentication-Methods/screenshots/Lab2SS10.png)  
**Screenshot 10** – Verified DKIM public key via `nslookup` for `s1._domainkey.namecheap.com`.
- Canonical Name (CNAME) - A type of DNS record that maps an alias name to a true or canonical domain name.

---

![Screenshot 11 – MXToolbox DKIM lookup](https://github.com/Mikala-Troupe/soc-level-1-labs/blob/051ea1c2d252a0baef73ddd27fb5d829b99d9482/Week-02_Email-Authentication-Methods/Week-02_Email-Authentication-Methods/screenshots/Lab2SS11.png)  
**Screenshot 11** – Searched `namecheap.com` with selector `s1` in MXToolbox.

---

![Screenshot 12 – MXToolbox results](https://github.com/Mikala-Troupe/soc-level-1-labs/blob/051ea1c2d252a0baef73ddd27fb5d829b99d9482/Week-02_Email-Authentication-Methods/Week-02_Email-Authentication-Methods/screenshots/Lab2SS12.png)  
**Screenshot 12** – Authentication checks passed; DKIM alignment matched **namecheap.com**.

---

![Screenshot 13 – Authentication results](https://github.com/Mikala-Troupe/soc-level-1-labs/blob/051ea1c2d252a0baef73ddd27fb5d829b99d9482/Week-02_Email-Authentication-Methods/Week-02_Email-Authentication-Methods/screenshots/Lab2SS13.png)  
**Screenshot 13** – Verified results in Sublime Text; DKIM and SPF checks aligned successfully.

---

## Notes / Takeaways
- The analyzed message was **legitimate**.  
- Passing SPF/DKIM/DMARC doesn’t mean an email is safe, it just confirms it passed technical checks.  
- Attackers can still:
  - Register look-alike domains that pass authentication.
  - Abuse legitimate services (like Google or SendGrid) to deliver phishing emails.  
- Always verify:
  - Sending domain and IP address via WHOIS and reverse DNS.
  - The **context** and **content** of the email itself.  
- Some legitimate emails might fail checks due to bad configurations.

---

## Summary of Email Authentication
| Protocol | Purpose | What It Does | Analogy |
|-----------|----------|---------------|----------|
| **SPF** | Defines which servers can send email for a domain | Lists authorized IP addresses in DNS | “Approved sender list” |
| **DKIM** | Verifies message integrity and authenticity | Uses cryptographic signature | “Tamper-evident seal on an envelope” |
| **DMARC** | Aligns SPF/DKIM results and defines failure actions | Adds reporting and enforcement | “Instructions for what to do if the sender or seal looks wrong” |
