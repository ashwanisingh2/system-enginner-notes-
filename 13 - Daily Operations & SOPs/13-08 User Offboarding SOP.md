---
tags: [desktop-support, sop, offboarding]
created: 2026-06-25
status: Complete
difficulty: Beginner
estimated-read-time: 15 mins
---

# 13-08 User Offboarding SOP

> [!abstract] Overview
> Standard Operating Procedure (SOP) for secure user offboarding. This document details account disabling, active session revocations, conversion of mailboxes to shared mailboxes, and hardware collections.

---

## 1. What Is It? (Concept Explanation)
User offboarding is the technical decommissioning process executed when an employee leaves the organization. It requires securing corporate assets, disabling account permissions, converting mailboxes, and retrieving physical hardware to prevent data leaks.
*Seedha simple shabdon mein: Jab koi employee company chhodta hai, toh security aur privacy compliance ke mutabik unke saare accounts block karna IT support team ka kaam hota hai. Humein unke AD account ko disable karna hota hai, active cloud sessions terminate karne hote hain, unka email box secure karke manager ko access deni hoti hai, aur unka laptop register system mein check-in karna hota hai.*

---

## 2. Offboarding Steps Workflow
Follow this checklist to complete an offboarding ticket:

- [ ] **Verify HR Offboarding Ticket:** Ensure you have received an approved offboarding request from HR detailing the employee's name, department, departure date, and time.
- [ ] **Disable Accounts in Active Directory:**
  1. Open ADUC (`dsa.msc`).
  2. Locate the user account, right-click, and select **Disable Account**.
  3. Move the user object to the dedicated `OU=Disabled-Users` container.
  4. Reset the account password to a random, complex string.
- [ ] **Revoke Cloud Session Tokens:**
  1. Log on to the Microsoft Entra admin center (`entra.microsoft.com`).
  2. Search for the user, go to the Overview tab, and click **Revoke Sessions**. This forces a logout from all devices (laptops, phones, tablets) within 15 minutes.
  3. Click **Block Sign-in** to reject new cloud logins.
- [ ] **Convert Mailbox to Shared Mailbox:**
  1. Open the Exchange Admin Center (`admin.exchange.microsoft.com`).
  2. Go to Recipients > Mailboxes. Select the user account.
  3. On the properties page, select the **Others** tab and click **Convert to Shared Mailbox**. (Shared mailboxes do not consume Microsoft licenses).
  4. Grant "Full Access" and "Send As" permissions to the departing employee's direct manager or approved replacement.
- [ ] **Retrieve Physical Assets:**
  1. Collect the corporate laptop, charger, monitors, docking stations, and security tokens.
  2. Verify the asset tag serial numbers match the user's inventory card in the CMDB system.
  3. Store the retrieved laptop in the staging bay.

---

## 3. Real-World Incident Tickets

### Ticket 1: Active Session Retention after Employee Termination
- **Incident ID:** INC110640
- **Priority:** P2 (Security Risk)
- **Problem Statement:** "An employee was terminated at 5:00 PM, but their Teams status still shows 'Active' on mobile, and they are replying to group chats."
- **Diagnostics:**
  1. Checked Active Directory. Confirmed the user account was disabled in ADUC at 5:05 PM.
  2. Explained that cloud session tokens can remain cached on mobile apps for hours even if the account is disabled in AD, unless session revocation is manually executed.
- **Resolution:** Logged into Microsoft Entra admin portal, located the user account, clicked **Revoke Sessions**, and blocked cloud logins. Within 5 minutes, the user's mobile device was forced to log out.

### Ticket 2: Recovering Local Critical Files from Retrieved Laptop
- **Incident ID:** INC110655
- **Priority:** P3
- **Problem Statement:** "A Sales Director left last week, and their manager needs to access local marketing plans saved on the returned laptop. The files are not on SharePoint."
- **Diagnostics:**
  1. Retrieved the decommissioned laptop from the IT staging inventory.
  2. Powered on the device. Logged on using local administrator credentials.
  3. Navigated to the user's local directory path:
     `C:\Users\departed.user\Desktop`. Located the required folder.
- **Resolution:** Copied the marketing files to the manager's shared network drive, logged out, and initiated a secure wipe on the laptop storage drive.

---

## 4. Technical Command Reference (PowerShell)
Use this script to disable accounts and move objects programmatically:
```powershell
# Disable AD User account
Disable-ADAccount -Identity "departed.user"

# Move the user object to the Disabled Users OU container
Move-ADObject -Identity "CN=departed.user,OU=Sales,OU=Users,DC=corp,DC=local" `
             -TargetPath "OU=Disabled-Users,DC=corp,DC=local"
```

---

## 5. Common Mistakes & Best Practices
- **Leaving Mailbox Licensed:** Forgetting to convert the user's mailbox to a shared mailbox. Leaving it active consumes corporate licenses, costing the company money.
- **Format vs. Wipe:** Simply formatting returned laptops before returning them to inventory. You must perform a secure multi-pass wipe to destroy old data.

---

## 6. Pro Tips (Senior Engineer Secrets)
> [!tip] Convert First, Disable Second
> When offboarding users with Microsoft 365 cloud mailboxes, always convert the mailbox to a shared mailbox *before* removing the Microsoft license. If you delete the license first, the cloud mailbox is marked for deletion and will be permanently lost after 30 days.

---

## 7. Interview Q&A Bank
**Q1: How do you handle a request from a manager asking you to reset their terminated employee's password so they can log on to their computer?**
A: I explain that logging into another employee's account violates audit compliance. Instead of sharing credentials, I will disable the account in ADUC, convert their mailbox to a shared mailbox, grant the manager "Full Access" permissions, and copy any local files from the PC to a secure team folder.

**Q2: What is the benefit of converting a departing user's mailbox to a Shared Mailbox?**
A: Shared mailboxes do not require an active Microsoft 365 license (if they are under 50GB in size), saving license costs for the company. They also allow managers or team members to access emails without using the user's password.

**Q3: How do you ensure a remote user's mobile device loses access to company data during offboarding?**
A: In Microsoft Entra portal, I block sign-ins for the account, revoke active session tokens, and trigger a **Retire / Wipe** command in Microsoft Intune to remove all corporate apps and data from the mobile device.

---

## Related Notes
- [[13-01 New User Onboarding SOP]] - Onboarding workflows comparison
- [[04-06 Microsoft 365 Admin Center]] - Licensing controls
- [[10-04 Asset Management]] - Device tracking registries