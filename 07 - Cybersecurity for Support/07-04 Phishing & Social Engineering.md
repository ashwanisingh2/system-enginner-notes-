---
tags: [desktop-support, security, phishing]
created: 2026-06-25
status: Complete
difficulty: Beginner
estimated-read-time: 14 mins
---

# 07-04 Phishing & Social Engineering

> [!abstract] Overview
> A guide to identifying and responding to social engineering and phishing attempts. This note covers phishing indicators, social engineering techniques, and incident reporting.

---

## 1. What Is It? (Concept Explanation)
Social engineering is the practice of manipulating people into giving up confidential information (like passwords or MFA codes) or downloading malware.
*Seedha simple shabdon mein bole toh: Social engineering hackers ka sabse aasan rasta hai. Computer systems ko hack karne ke bajaye, woh email ya phone call ke zariye users ko bevkuf banate hain taaki users khud apna password bata dein ya malicious links par click kar dein. Isko pehchanna aur users ko aware rakhna IT support ka kaam hai.*

---

## 2. Common Social Engineering Techniques
- **Phishing:** Sending fraudulent emails that look like they come from legitimate sources (e.g., Microsoft account alert, HR notice, bank update).
- **Spear Phishing:** Highly targeted phishing attacks aimed at specific individuals, using personal or business details to build trust.
- **Pretexting:** Creating a fake scenario to manipulate a victim (e.g., calling and pretending to be a bank auditor or senior executive).
- **Tailgating:** Following an authorized employee through security doors to gain physical access to a secure office area.

---

## 3. Indicators of a Phishing Email
Teach users to look out for these indicators:
- **Suspicious Sender Domain:** The display name says "IT Support", but the sender email domain is `@gmail.com` or `@sec-update-corp.com` (instead of `@company.com`).
- **Urgency or Threats:** "Your account will be suspended in 2 hours. Click here to verify."
- **Suspicious Hyperlinks:** Hovering the mouse over the link reveals a destination URL that doesn't match the company domain.
- **Generic Greetings:** "Dear Customer" or "Dear Employee".

---
## 2. Technical Deep-Dive: Phishing Headers & E-mail Security Protocols
Enterprise email security relies on authentication protocols to block phishing:
- **SPF (Sender Policy Framework):** Specifies which IP addresses are authorized to send email on behalf of a domain.
- **DKIM (DomainKeys Identified Mail):** Adds a digital signature to email headers, verifying the message was not modified in transit.
- **DMARC (Domain-based Message Authentication, Reporting, and Conformance):** Tells the receiving server what to do if SPF or DKIM checks fail (e.g., quarantine or reject the email).
- **Phishing Indicators:** Look for urgent requests, generic greetings, mismatches between the display name and the actual sender address, and suspicious attachments.
### Ticket 1: Phishing Credential Harvest Incident Response
- **Incident ID:** INC107412
- **Priority:** P2 (Security Incident)
- **Problem Statement:** "I received an email saying my mailbox storage was full, clicked the link, and entered my password. Now my screen is lagging."
- **Incident Response Actions:**
  1. Immediately logged into the Microsoft Entra admin portal.
  2. Selected the user's account and revoked all active session tokens to sign them out of all devices.
  3. Reset the user's AD password and enabled a temporary block on sign-ins.
- **Resolution:** Verified no emails were sent from the account, ran a full malware scan on the laptop, and cleared the user for logins with a new password.
### Check Email Authentication Headers (PowerShell)
```powershell
# Query SPF, DKIM, and DMARC TXT records for a domain
Resolve-DnsName -Name company.com -Type TXT
```
**Q1: What is the purpose of DMARC records in email security?**
A: DMARC tells receiving mail servers how to handle emails that fail SPF or DKIM verification, such as moving them to spam (quarantine) or rejecting them outright, protecting corporate domains from spoofing.

## Business Email Compromise (BEC) and Spoofing
Business Email Compromise (BEC) is a phishing attack where hackers compromise corporate executive email accounts to request financial transfers.

### Analyzing Email Headers
Support engineers check email headers to verify authenticity:
1. Open the suspicious email in Outlook, go to File > Properties, and inspect the **Internet Headers** box.
2. Locate the **Authentication-Results** field.
3. Check the SPF, DKIM, and DMARC status values:
   - `spf=pass` : Confirms the sender's IP is authorized.
   - `dkim=pass` : Confirms the email digital signature is valid.
   - `dmarc=fail` : Indicates the email failed verification tests and may be spoofed.
4. If a header shows an IP address mismatch, report the details to the Security Operations Center (SOC) team.

## Additional Pro-Tips for User Verification
Social engineering is not restricted to emails; attackers also use voice calls (Vishing) and SMS messages (Smishing) to target corporate employees. A common scenario involves the attacker calling the Help Desk pretending to be a high-ranking executive who is locked out of their account. Tech support must strictly follow identity verification guidelines (such as verifying employee records or sending a verification code to their registered mobile number) before resetting any credentials.

## Related Notes
- [[07-08 Security Incident Response]] - Incident handling plans
- [[05-07 Communication Skills for Support]] - Handling user reports