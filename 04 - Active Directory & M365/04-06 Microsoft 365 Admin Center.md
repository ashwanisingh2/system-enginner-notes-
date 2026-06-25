---
tags: [desktop-support, m365, admin-center]
created: 2026-06-25
status: Complete
difficulty: Beginner
estimated-read-time: 14 mins
---

# 04-06 Microsoft 365 Admin Center

> [!abstract] Overview
> An operations manual for the Microsoft 365 Admin Center. This note covers user license management, group administration, checking service health status, and checking sign-in logs.

---

## 1. What Is It? (Concept Explanation)
The Microsoft 365 Admin Center (`admin.microsoft.com`) is the central cloud portal used to manage user accounts, assign software licenses, configure shared mailboxes, and monitor service health.
*Seedha simple shabdon mein bole toh: M365 Admin Center cloud dashboard hai jahan se employees ko Outlook, Teams, Word, aur OneDrive ka access diya jata hai. Naye users ko add karna, unhe licenses assign karna, password reset karna, aur mail settings modify karna, is dashboard se aasan ho jata hai.*

---

## 2. Essential Cloud Administration Tasks

### 1. Assigning Licenses
- In the M365 Admin Center, navigate to **Users** -> **Active Users**.
- Search for the user, open their profile, and go to the **Licenses and apps** tab.
- Select the appropriate license level (e.g., *Microsoft 365 E5* or *Business Premium*) and click Save.

### 2. Managing Shared Mailboxes
- Shared mailboxes allow multiple users to access a single inbox (e.g., `support@company.com`).
- Navigate to **Teams & groups** -> **Shared mailboxes**.
- Click **Add a shared mailbox**, set the email alias, and configure member read/write permissions.

### 3. Monitoring Service Health
- Before troubleshooting local application issues, check the M365 dashboard for active outages.
- Go to **Health** -> **Service health** to review outages affecting Exchange, SharePoint, or Teams.

---

## 3. Real-World Scenarios

### Scenario 1: Outlook License Expired Error
- **Incident Description:** An office worker reports their Outlook client shows a banner: "Product Deactivated: Your Microsoft 365 subscription has expired," blocking them from editing documents.
- **Troubleshooting Steps:**
  1. Open M365 Admin Center, search for the user under **Active Users**.
  2. Check their license status. The E3 license is checked, but the status shows a warning.
  3. Navigate to **Billing** -> **Licenses**. The available license pool shows 0 licenses free, and some accounts are unlicensed due to payment processing issues.
- **Resolution:**
  - Temporarily re-assign an available license from a test pool.
  - Advise the billing administrator to update the corporate credit card settings to restore the normal subscription status.

---
## 2. Technical Deep-Dive: Entra ID Sync & Licensing
The Microsoft 365 Admin Center (`admin.microsoft.com`) is the centralized SaaS portal:
- **Directory Synchronization:** User identities are created in local Active Directory and synchronized to the cloud using Entra ID Connect sync. Sync cycles run every 30 minutes.
- **License Pool Management:** Licenses (e.g., Microsoft 365 E3, E5, F3) are allocated to synced users. If a user loses their license, their Exchange mailbox is disabled and marked for deletion after a 30-day grace period.
- **Admin Roles:** Permissions are assigned using RBAC (Role-Based Access Control): Global Administrator, Helpdesk Administrator, Billing Administrator, and User Administrator.
### Ticket 1: OneDrive Synchronization Access Blocked
- **Incident ID:** INC104612
- **Priority:** P3
- **Problem Statement:** "My OneDrive client shows a red cross icon and error: 'Your account is blocked from syncing files'."
- **Diagnostics:**
  1. Opened M365 Admin Center and searched for the user account.
  2. The user's account was marked "Blocked Sign-in: Yes" in the portal.
  3. Explained that the user's AD account was disabled last week due to a brief medical leave, and AD Connect synchronized the block status to the cloud.
- **Resolution:** Opened local ADUC, moved the user account out of the disabled OU, enabled the account, ran a delta sync command on the sync server, verified the cloud status updated to "Sign-in allowed", and restarted the OneDrive client.
### Force Directory Sync via PowerShell (Run on Sync Server)
```powershell
# Force immediate delta synchronization of local AD modifications to M365 cloud
Start-ADSyncSyncCycle -PolicyType Delta
```
**Q1: What is the role of Entra ID Connect (Azure AD Connect) in M365 administrations?**
A: Entra ID Connect is a synchronization utility installed on-premises that replicates user accounts, passwords, groups, and computer objects from local Active Directory Domain Services to Microsoft Entra ID in the cloud, enabling Single Sign-On (SSO).

**Q2: What is the difference between Global Administrator and Helpdesk Administrator roles in M365?**
A: A Global Administrator has full administrative permissions across all M365 cloud services (billing, domains, users, settings). A Helpdesk Administrator has restricted permissions, limited to resetting passwords, managing service requests, and locking user sign-in tokens.

## Microsoft 365 Admin Center Licensing Audit & Billing
IT managers control corporate spending by running monthly license audits in the M365 Admin Center:
- **Unassigned Licenses:** Locate active licenses (e.g. M365 E5) that are paid for but not assigned to any user accounts.
- **Group-Based Licensing:** Automate assignments by linking M365 licenses to specific Entra ID groups. When a user is added to the group, the license maps automatically.

## Related Notes
- [[04-09 Azure AD Basics (Hybrid Environment)]] - Hybrid sync setups
- [[13-01 New User Onboarding SOP]] - User licensing SOP