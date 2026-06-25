---
tags: [desktop-support, azure-ad, hybrid, entra]
created: 2026-06-25
status: Complete
difficulty: Intermediate
estimated-read-time: 14 mins
---

# 04-09 Azure AD Basics (Hybrid Environment)

> [!abstract] Overview
> A guide to Azure Active Directory (Microsoft Entra ID) in hybrid configurations. This note covers Entra Connect sync, device join types, conditional access rules, and managing cloud passwords.

---

## 1. What Is It? (Concept Explanation)
Microsoft Entra ID (formerly Azure AD) is Microsoft's cloud-based identity and access management service. In hybrid environments, an organization's on-premises Active Directory is synchronized with Entra ID.
*Seedha simple shabdon mein bole toh: Hybrid environment ka matlab hai ki user account local server (On-Premises AD) par banta hai, aur Azure AD Connect tool use automatic cloud (Azure AD) mein sync kar deta hai. Isse user ko office workstation aur office cloud web pages dono par ek hi password se access milta.*

---

## 2. Device Join Types in Entra ID
Understanding how corporate computers are registered in the cloud helps isolate device policy issues:

- **Entra Registered (Azure AD Registered):** Typically personal (BYOD) devices or mobile phones. Allows secure access to corporate cloud resources using a personal device.
- **Entra Joined (Azure AD Joined):** Corporate-owned devices that log on using cloud-based accounts (no on-premises AD needed). Configured via Windows Autopilot.
- **Hybrid Entra Joined:** Corporate-owned devices joined to the on-premises Active Directory that sync to Microsoft Entra ID.

---

## 3. Real-World Scenarios

### Scenario 1: User Cannot Access Cloud Resources (MFA Loop)
- **Incident Description:** An employee cannot access their Outlook web portal from home. Every login attempt displays the message: "More information required," and loops them back to the login page without sending an SMS verification code.
- **Troubleshooting Steps:**
  1. Open the **Microsoft Entra admin center** (`entra.microsoft.com`) as Administrator.
  2. Navigate to **Identity** -> **Users** -> **Active Users**. Find the affected user.
  3. Click **Authentication methods**. Check the configured phone numbers and authentication apps.
  4. Note that the user replaced their phone, so their authenticator app configuration is invalid.
- **Resolution:**
  - Click **Require re-register MFA**.
  - This resets the MFA settings for the user. When they next log in, they will be prompted to link their new device to their corporate account.

---

## 4. Diagnostic Commands for Entra Join Status
Run this command in the Windows Command Prompt to check a device's cloud registration status:

```cmd
:: Check Entra / Azure AD join status parameters
dsregcmd /status
```

*In the command output, verify that `AzureAdJoined : YES` and `DomainJoined : YES` are active for hybrid devices. If both are true, the device is synchronizing correctly with the cloud.*

---
## 2. Technical Deep-Dive: Hybrid Identities & SSO Authentication
In a hybrid enterprise environment, identity management links on-premises AD and Microsoft Entra ID (Azure AD):
- **Password Hash Synchronization (PHS):** Entra ID Connect syncs a hash of the user's password from local domain controllers to the cloud, allowing users to log on with the same password.
- **Pass-through Authentication (PTA):** Authentication requests are routed back to on-premises domain controllers using agents, keeping passwords local.
- **Seamless Single Sign-On (SSO):** When users are logged into a domain-joined PC on the LAN, they are automatically authenticated to cloud apps without typing passwords, utilizing Kerberos tokens.
### Ticket 1: Hybrid AD Sync Delay after User Account Update
- **Incident ID:** INC104921
- **Priority:** P3
- **Problem Statement:** "I updated my name in Active Directory yesterday, but my display name in Microsoft Teams still shows my old name."
- **Diagnostics:**
  1. Confirmed the name was updated in local ADUC.
  2. Checked the Entra ID portal. Found the user account had updated.
  3. Explained that Teams uses a separate database (Microsoft Graph) that updates from Entra ID periodically, which can take up to 24 hours to replicate to client applications.
- **Resolution:** Had the user sign out of Microsoft Teams entirely (revoking the active cached token), cleared the Teams app local cache directory, and signed back in. The new name displayed.
### Check Hybrid Domain Connection Status (CMD)
```cmd
:: Output details of Entra ID join state and device registration
dsregcmd /status
```
**Q1: What is the difference between Azure AD Joined and Hybrid Azure AD Joined devices?**
A: Azure AD Joined devices are registered directly in the Entra ID cloud database and managed via MDM (Intune) without local AD servers. Hybrid Azure AD Joined devices are joined to local Active Directory Domain Services and synchronized to Entra ID, supporting both GPOs and cloud policies.

**Q2: What command displays the Entra ID device registration state on a client machine?**
A: I open CMD and run the command `dsregcmd /status`. I check the fields **AzureAdJoined** and **DomainJoined** in the output to verify registration states.

## Related Notes
- [[04-06 Microsoft 365 Admin Center]] - Entra ID cloud setup
- [[04-01 Active Directory Fundamentals]] - Directory architectures