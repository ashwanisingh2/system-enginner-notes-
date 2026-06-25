---
tags: [desktop-support, security, fundamentals]
created: 2026-06-25
status: Complete
difficulty: Beginner
estimated-read-time: 12 mins
---

# 07-01 Security Fundamentals (CIA Triad)

> [!abstract] Overview
> An introduction to information security standards for Desktop Support Engineers. This note covers the CIA Triad, Principle of Least Privilege, Access Control models, and basic NTFS vs Share permissions logic.

---

## 1. What Is It? (Concept Explanation)
Security fundamentals form the framework of all enterprise IT security policies. The primary goal of security is to protect assets from unauthorized access, loss, and alteration.
*Seedha simple shabdon mein bole toh: Company ke data ko secure rakhna security fundamentals hai. CIA triad iska root framework hai: Confidentiality (data chori na ho), Integrity (data change na ho), aur Availability (data jarurat padne par mile). Ek support engineer ko permissions set karte waqt in security rules ka dhayan rakhna hota hai.*

---

## 2. Technical Deep-Dive: The CIA Triad & Least Privilege
The **CIA Triad** is the core framework used to design security policies:

- **Confidentiality:** Preventing unauthorized disclosure of information. Maintained using file encryption (BitLocker), Multi-Factor Authentication (MFA), and Access Control Lists (ACLs).
- **Integrity:** Ensuring data remains accurate and unaltered. Maintained using file hashing (SHA-256), version controls, and logging write permissions.
- **Availability:** Ensuring authorized users have uninterrupted access to resources. Maintained using backups, RAID configurations, and server redundancy.

### The Principle of Least Privilege (PoLP)
This principle dictates that users and systems must be granted only the minimum access rights necessary to perform their specific job tasks. For example, a customer support agent does not require administrator access to local machines, and a marketing analyst does not need read access to payroll servers.

### NTFS vs. Share Permissions
When configuring file shares over a corporate network:
- **Share Permissions:** Apply only when accessing files over the network.
- **NTFS Permissions:** Apply whether the user is logged on locally or over the network.
- **Effective Permission:** The most restrictive permission wins (e.g., if Share is Read-Only, but NTFS is Full Control, the network user has only **Read-Only** access).

---

## 3. Real-World Support Scenario (STAR Ticket)
- **Situation:** A manager in the Human Resources department reports that standard employees are able to open and read salary files located in a network folder (`\\fileserver\hr-docs\salaries`).
- **Task:** Verify the permissions conflict, implement the principle of least privilege, and secure the directory.
- **Action:**
  1. Connected to the target folder and checked current NTFS permissions using `icacls`:
     ```cmd
     icacls \\fileserver\hr-docs\salaries
     ```
  2. Identified that the security group `Everyone` had read permissions in the NTFS Access Control List (ACL).
  3. Removed the `Everyone` group from the folder's ACL.
  4. Added the authorized AD security group `SG-HR-Payroll` to the ACL, granting them Read & Write access.
  5. Verified inheritance was disabled on the subfolder to prevent root directory permissions from trickling down.
  6. Tested access with a standard user account to confirm they were blocked with an "Access Denied" message.
- **Result:** Secured the folder, restricting access to authorized HR payroll staff and ensuring confidentiality of company data.

---

## 4. NTFS Security & Permissions Reference Table

| Permission Type | Action Allowed | Applies To |
|---|---|---|
| **Read** | View files and folders. | Network & Local |
| **Write** | Create and modify files. | Network & Local |
| **Modify** | Read, write, and delete files. | Network & Local |
| **Full Control** | Read, write, modify, and change permissions. | Network & Local |
| **Read (Share)** | View files over network. | Network Only |

---

## 5. Frequently Asked Questions (FAQ)

**Q1: What is the difference between Authentication and Authorization?**
A: Authentication verifies *who you are* (e.g., entering a username and password with MFA). Authorization determines *what you can access* (e.g., your account being allowed to read a specific folder).

**Q2: Why is "Deny" permission used carefully in Windows NTFS?**
A: An explicit "Deny" permission always overrides an "Allow" permission. If a user is a member of an allowed group and also a member of a denied group, they will be blocked from accessing the file.

**Q3: How do I view a user's effective permissions for a folder in Windows?**
A: Right-click the folder, go to Properties > Security tab > click Advanced > go to the "Effective Access" tab. Select the user account to view their active access.

**Q4: What is the role of auditing in the CIA Triad?**
A: Auditing logs access and changes to files, contributing to the Integrity and Confidentiality of data by creating an audit trail of who accessed what files and when.

---

## Access Control List (ACL) Inheritance & Permission Creep
NTFS security folders inherit permissions from their parent directories by default:
- **Permission Creep:** Occurs when users change roles in a company but retain access rights to old directories.
- **Auditing Access:** Technicians run regular access audits and disable permission inheritance on sensitive folders, manually allocating specific user groups to prevent security leaks.

## Related Notes
- [[07-05 Encryption & Data Protection]] - BitLocker encryption setups
- [[07-06 Password Security & MFA]] - Passwords management
- [[04-02 User Account Management in AD]] - AD groups settings