---
tags: [desktop-support, active-directory, group-management]
created: 2026-06-25
status: Complete
difficulty: Beginner
estimated-read-time: 14 mins
---

# 04-03 Group Management

> [!abstract] Overview
> A guide to managing security and distribution groups in Active Directory. This note covers group types, scopes, membership rules, and access control lists.

---

## 1. What Is It? (Concept Explanation)
Groups simplify directory administration. Instead of assigning permissions (like folder access or printer mapping) to individual users, you assign permissions to a group and add users to that group.
*Seedha simple shabdon mein bole toh: Agar har user ko individual files access denge, toh network manage karna impossible ho jayega. Isliye hum department-wise groups (jaise Finance-Team, HR-Team) banate hain. Jab naya employee aata hai, toh use bas us department group ka member bana dete hain, aur use access mil jata hai.*

---

## 2. Group Types & Scopes

### Group Types
- **Security Groups:** Used to assign permissions to network resources (shared folders, printers, SharePoint sites).
- **Distribution Groups:** Used solely as email distribution lists (not for security permissions).

### Group Scopes
- **Global Groups:** Group users with similar access needs. Can only contain members from the local domain.
- **Domain Local Groups:** Used to assign permissions to resources (like folder shares). Can contain members from any trusted domain.
- **Universal Groups:** Used to group accounts across multiple domains in a forest.

---

## 3. Real-World Scenarios

### Scenario 1: User Cannot Access Shared Folder
- **Incident Description:** An HR manager says they cannot open the shared payroll folder `\\server01\HR-Payroll`. They get an "Access Denied" error, despite being added to the HR department yesterday.
- **Troubleshooting Steps:**
  1. Open ADUC (`dsa.msc`) and verify the user is a member of the security group `SG-HR-Payroll-Read`. The membership is configured correctly.
  2. Explain that Windows only updates a user's security token (which lists their group memberships) when they log on. Group additions made while a user is logged on do not apply immediately.
- **Resolution:**
  - Ask the user to log off their computer, log back on, and try accessing the folder again.
  - Verify the security token updated using the `whoami /groups` command.

---

## 4. Group Management PowerShell Commands
```powershell
# List all members of a specific AD security group
Get-ADGroupMember -Identity "SG-Marketing-Share" | Select-Object Name, SamAccountName

# Create a new Global Security Group in Active Directory
New-ADGroup -Name "SG-Finance-Read" -GroupScope Global -GroupCategory Security -Path "OU=Groups,OU=Corp,DC=corp,DC=local"
```

---
## 2. Technical Deep-Dive: AGDLP & Group Nesting
Enterprise Active Directory architectures manage resource permissions using the **AGDLP** strategy:
- **A (Account):** Add user accounts to a Global Group.
- **G (Global Group):** Group users by job role or department (e.g., `GG-Finance-Staff`).
- **DL (Domain Local Group):** Group resources and assign permissions (e.g., `DL-Finance-Folder-RW`). Nest the Global Group inside this Domain Local Group.
- **P (Permissions):** Assign permissions (Read, Write, Full Control) directly to the Domain Local Group.
- **Group Scopes:**
  - **Global:** Can contain accounts from the same domain, but can be assigned permissions in any domain.
  - **Domain Local:** Can contain members from any domain, but can only be assigned permissions in its local domain.
  - **Universal:** Can contain members from any domain, and can be assigned permissions in any domain. Requires Global Catalog replication.
### Ticket 1: Active Directory Group Nesting Security Breach
- **Incident ID:** INC104390
- **Priority:** P2
- **Problem Statement:** "An intern in the Sales department has suddenly gained read and write access to the main HR shared folder."
- **Diagnostics:**
  1. Checked the HR folder permissions. Access was restricted to Domain Local group `DL-HR-Share-RW`.
  2. Checked the members of `DL-HR-Share-RW`. Found it contained the Global Group `GG-HR-Staff`.
  3. Audited the members of `GG-HR-Staff`. Found a nested Global Group: `GG-Sales-Interns` had been added inside `GG-HR-Staff` by mistake during a recent provisioning request.
- **Resolution:** Removed the nested `GG-Sales-Interns` group from `GG-HR-Staff`, restoring appropriate access boundaries. Logged the change in the security ticket.
### Manage AD Group Membership (PowerShell)
```powershell
# Add an AD user to a security group
Add-ADGroupMember -Identity "DL-Finance-Folder-RW" -Members "rohan.sharma"

# Get all members of a specific active directory group
Get-ADGroupMember -Identity "GG-Finance-Staff" | Select-Object Name, SamAccountName
```
**Q1: What is the difference between a Security Group and a Distribution Group in Active Directory?**
A: A Security Group is assigned a Security Identifier (SID) and is used to assign access permissions to network resources (files, folders, printers). A Distribution Group is not assigned a SID and cannot be used for permissions; it is strictly used for email distribution lists in Exchange.

**Q2: Explain the AGDLP strategy for group nested configuration.**
A: AGDLP stands for Account, Global Group, Domain Local Group, Permission. User **Accounts** are placed in **Global groups** (based on role). These Global groups are nested inside **Domain Local groups** (based on resource access). Finally, resource **Permissions** are assigned to the Domain Local groups, simplifying permissions administration.

## Related Notes
- [[04-02 User Account Management in AD]] - User account settings
- [[04-05 Group Policy for Support Engineers]] - Group configurations