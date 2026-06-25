---
tags: [desktop-support, sop, passwords]
created: 2026-06-25
status: Complete
difficulty: Beginner
estimated-read-time: 15 mins
---

# 13-04 Password Reset SOP

> [!abstract] Overview
> Mandated identity verification protocols, resetting Active Directory and M365 passwords, and resolving user MFA lockouts.

---

## 1. What Is It? (Concept Explanation)
A password reset is a high-risk security operation. Hackers often use social engineering (pretending to be an employee) to trick support technicians into resetting passwords, enabling unauthorized access. Support engineers must follow verification standards before modifying account credentials.
*Seedha simple shabdon mein: Password reset ke requests sabse zyada common hote hain par ye sabse bada security risk bhi hain. Koi bhi hacker call karke bol sakta hai ki "Main Finance Manager hoon, mera account open kar do." Isliye password reset karne se pehle humesha user ki identity confirm karein (jaise Employee ID, unka direct manager name, ya register call-back) taaki koi fraud logon na ho sake.*

---

## 2. Password Reset SOP Steps
Follow this process for every credential reset request:

- [ ] **Verify User Identity (Mandatory):**
  1. Ask the user for their Employee ID number, department name, and direct manager's name.
  2. Send an MFA push code to their registered device if possible, or trigger a callback to their registered phone number.
  3. Never accept verification from third-party messages or non-corporate channels.
- [ ] **Reset On-Premises Domain Password:**
  1. Open Active Directory Users and Computers (`dsa.msc`).
  2. Search for the user account. Double-click the user to verify status (is it disabled or locked?).
  3. Right-click the user account and select **Reset Password**.
  4. Enter a complex, temporary password that complies with corporate policy (e.g., minimum 12 characters, uppercase, lowercase, numbers, and symbols).
  5. Check **User must change password at next logon** and click OK.
- [ ] **Unlock Account:** If the account has been locked due to excessive bad password attempts, check the **Unlock user's account** box in the reset dialog.
- [ ] **Reset Microsoft 365 Cloud Passwords:**
  1. For cloud-only accounts, log on to the Microsoft Entra admin center (`entra.microsoft.com`).
  2. Search for the user, click **Reset Password**, and select a temporary password.
- [ ] **Secure Delivery of Credentials:** Communicate the temporary password to the user over a secure channel (e.g., phone call or encrypted MS Teams message). Never send passwords in plain text emails.

---

## 3. Real-World Incident Tickets

### Ticket 1: Active Directory Account Lockout Loop
- **Incident ID:** INC110450
- **Priority:** P3
- **Problem Statement:** "I reset my domain password, but my account keeps getting locked out every 15 minutes, even when I am not typing my password."
- **Diagnostics:**
  1. Opened Event Viewer on the Domain Controller. Filtered Security logs for Event ID 4740 (Account Locked).
  2. Found the caller computer name was `LAP-IT-92`, which was the user's tablet computer.
  3. Explained that the user's tablet had cached their old password in its local Wi-Fi connection settings and was sending failed login attempts in the background, locking the account.
- **Resolution:** Had the user disconnect Wi-Fi on the tablet, cleared the saved corporate Wi-Fi profile, unlocked the account in ADUC, and entered the new password on the tablet to update the cache. The lockout loop was resolved.

### Ticket 2: MFA Lockout after User Replaces Mobile Device
- **Incident ID:** INC110467
- **Priority:** P3
- **Problem Statement:** "I got a new phone, and I cannot log on to Outlook because the MFA push prompt goes to my old device which I no longer have."
- **Diagnostics:**
  1. Logged on to the Microsoft Entra admin portal.
  2. Searched for the user, went to **Authentication methods**.
  3. Confirmed the primary MFA method was linked to the old phone's Microsoft Authenticator app.
- **Resolution:** Initiated a **Require re-register MFA** action in the Entra ID console. Instructed the user to log on to their PC, which prompted them to register their new phone using a QR code. Verified they completed setup and logged on.

---

## 4. Troubleshooting Local Cached Credentials (CMD)
If a user changes their password on a corporate device but cannot log on while working remotely (offline):
1. Connect the user to the corporate network via network cable, or log them in using their old password locally.
2. Connect the laptop to the corporate VPN using the **Sign-in Options** network interface on the Windows lock screen.
3. Once the VPN connection is established, lock the PC (`Win + L`) and have the user log on using their new password. This updates the local cached credentials database.
4. Verify using:
   ```cmd
   :: Force immediate Kerberos ticket renewal
   klist purge
   ```

---

## 5. Study & Interview Q&A
**Q1: How do you handle a request from a manager asking you to reset their employee's password and send the credentials to the manager?**
A: For security and compliance, I cannot send passwords to third parties, even a manager. I explain that the password must be delivered directly to the account owner to maintain accountability. I will reset the password to a temporary string, contact the employee directly via phone to verify their identity, and provide the temporary password to them.

**Q2: What is the significance of Event ID 4740 in Windows Security logs?**
A: Event ID `4740` is logged on Domain Controllers when a user account is locked out due to consecutive failed logon attempts. The event details contain the **Caller Computer Name**, which tells support engineers the exact device (PC, tablet, or phone) where the bad authentication attempts are coming from, helping locate cached credential conflicts.

**Q3: What steps do you take if a user forgets their password while working from home?**
A: I verify their identity via a secondary channel (such as calling their registered personal phone number on file or requesting confirmation from HR). Once verified, I reset the password in ADUC to a temporary string. Since they are remote, I instruct them to connect their laptop to their home internet, launch the corporate VPN client at the Windows login screen, and enter the new credentials to sync the password.

---

## Related Notes
- [[07-06 Password Security & MFA]] - Passwords complexity standards
- [[04-02 User Account Management in AD]] - AD accounts management
- [[12-06 Event Viewer IDs Reference]] - Security logs Event IDs