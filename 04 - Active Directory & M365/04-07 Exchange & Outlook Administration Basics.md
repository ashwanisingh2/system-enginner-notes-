---
tags: [desktop-support, m365, exchange, outlook]
created: 2026-06-25
status: Complete
difficulty: Intermediate
estimated-read-time: 14 mins
---

# 04-07 Exchange & Outlook Administration Basics

> [!abstract] Overview
> A support guide for Exchange Online and Microsoft Outlook. This note covers configuring email profiles, shared mailboxes, calendar delegation, and troubleshooting mailbox connection issues.

---

## 1. What Is It? (Concept Explanation)
Exchange Online is Microsoft's cloud-based messaging platform. Outlook is the desktop client application users use to access their mailboxes.
*Seedha simple shabdon mein bole toh: Company ke saare emails Exchange Server par receive aur store hote hain. Outlook client bas us data ko display karta hai. Desktop support mein Outlook profile reset karna, calendar sharing configure karna, aur shared inboxes access provide karna regular kaam hote hain.*

---

## 2. Mailbox Permissions Configuration
To allow users to access another mailbox (like a manager's calendar or a shared department inbox), configure permissions in the Exchange Admin Center:

- **Send As:** Allows a delegate to send emails that appear to come directly from the mailbox. The recipient cannot tell the mail was sent by a delegate.
- **Send on Behalf:** Allows a delegate to send emails. The recipient sees the sender as: "*Delegate on behalf of Mailbox Owner*".
- **Full Access:** Allows a user to open the mailbox and manage its folders, calendar, and emails. It does not grant permission to send mail unless combined with "Send As" permissions.

---

## 3. Real-World Scenarios

### Scenario 1: Outlook Fails to Open / Profile Corrupted
- **Incident Description:** A user reports Outlook hangs on the splash screen displaying "Loading Profile..." for 10 minutes and refuses to load their inbox.
- **Troubleshooting Steps:**
  1. Close Outlook. Open Task Manager and terminate any active `outlook.exe` processes.
  2. Open the Control Panel and search for **Mail (Microsoft Outlook)**.
  3. Click **Show Profiles**.
  4. Select **Add...**, type a profile name (e.g., `OutlookNew`), and click Next.
  5. The setup wizard autodetects the user's M365 login credentials. Complete the wizard.
  6. In the Mail dialog, select **Always use this profile** and choose `OutlookNew`.
- **Resolution:** Re-created the Outlook profile. Outlook started successfully and synchronized the user's mailbox files from the Exchange server.

---

## 4. Diagnostic Commands for Mailbox support
```cmd
:: Launch Outlook in Safe Mode to disable buggy add-ins (Run / CMD)
outlook.exe /safe

:: Open Windows Mail profile manager directly
control mlcfg32.cpl
```

---
## 2. Technical Deep-Dive: Mail Flow & Exchange Admin Center
Email routing in M365 runs over Exchange Online:
- **MX Record:** Mail Exchanger DNS record that points domain email traffic to Microsoft servers (e.g., `company-com.mail.protection.outlook.com`).
- **Shared Mailbox:** A mailbox accessible by multiple delegated users. It requires no license if the storage size remains under 50GB and does not support direct login.
- **Outlook Connectivity (MAPI over HTTP):** Modern transport protocol used by Outlook to communicate with Exchange Online. AutoDiscover is used to query configurations.
### Ticket 1: Calendar Delegation Permission Sync Delay
- **Incident ID:** INC104719
- **Priority:** P3
- **Problem Statement:** "The CEO granted me delegate access to edit their calendar, but when I try to save an appointment, I get 'You do not have permission'."
- **Diagnostics:**
  1. Opened Exchange Admin Center and checked the CEO's mailbox properties.
  2. Verified the assistant was granted "Full Access" and delegate rights.
  3. Discovered the delegation settings were applied in Outlook local client only 10 minutes ago.
  4. Explained that permission modifications in hybrid Exchange environments require database replication sync cycles, which can take up to 2 hours.
- **Resolution:** Instructed the assistant to restart Outlook to force a folder profile check. The calendar opened and allowed edits.
### Verify Outlook Exchange Connection Status (CMD)
```cmd
:: Launch Outlook connection status diagnostic overlay
:: (Hold Ctrl and right-click the Outlook icon in the Windows taskbar system tray, select Connection Status)
```
**Q1: What is the difference between an Outlook OST file and a PST file?**
A: An OST file is a local cache of the cloud Exchange mailbox, which is safe to delete as it will rebuild from the server. A PST file is a local archive file that contains emails downloaded off the server; if deleted, the archived emails are permanently lost.

**Q2: How do shared mailboxes differ from user mailboxes in Exchange Online?**
A: Shared mailboxes do not require a Microsoft 365 license (under 50GB size limit) and have their login credentials disabled; users access them via delegation permissions linked to their primary account. User mailboxes require active licenses and support direct login authentication.

## Advanced Exchange Shared Mailboxes and Delegation
When setting up delegator permissions in the Exchange Admin Center (EAC):
- **Full Access:** Allows the delegate to open the mailbox and view all folder logs, but not send emails.
- **Send As:** Allows the delegate to send emails that appear to come directly from the shared mailbox.
- **Send on Behalf:** Allows the delegate to send emails, but recipients see the message sent by the delegate on behalf of the shared mailbox.

## Related Notes
- [[06-02 Outlook Deep Dive Troubleshooting]] - Client email troubleshooting
- [[13-08 User Offboarding SOP]] - Decommissioning mailboxes