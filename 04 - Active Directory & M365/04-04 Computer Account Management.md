---
tags: [desktop-support, active-directory, computer-management]
created: 2026-06-25
status: Complete
difficulty: Beginner
estimated-read-time: 14 mins
---

# 04-04 Computer Account Management

> [!abstract] Overview
> A guide to managing computer accounts in Active Directory. This note covers joining domains, prestaging computer objects, resetting computer passwords, and resolving trust errors.

---

## 1. What Is It? (Concept Explanation)
Just like users, computers require accounts to join an Active Directory domain. These computer accounts authenticate the machine with the Domain Controller to establish a secure channel.
*Seedha simple shabdon mein bole toh: Jaise employees ke login accounts hote hain, waise hi business laptops aur computers ke bhi accounts AD database mein hote hain. Jab PC network se connect hota hai, toh woh AD server se verify karta hai ki PC registry and permissions up-to-date hain. Agar computer inactive ho jaye, toh domain connection break ho jata hai.*

---

## 2. Joining a Windows Workstation to the Domain
To join a new or reformatted client workstation to the corporate domain:

1. Connect the computer to the corporate network (or verify VPN connection).
2. Right-click **This PC** -> select **Properties** -> click **Advanced system settings**.
3. Under the **Computer Name** tab, click **Change**.
4. Select **Domain** and type the corporate domain name (e.g., `corp.local`). Click OK.
5. Enter authorized domain join credentials when prompted.
6. Restart the computer to complete the join process.

---

## 3. Real-World Scenarios

### Scenario 1: Resetting an Inactive Computer Account
- **Incident Description:** A workstation that was stored in the inventory build room for six months displays the error "The trust relationship failed" when powered on.
- **Troubleshooting Steps:**
  1. Open ADUC (`dsa.msc`).
  2. Locate the workstation account in the `Workstations` OU.
  3. Right-click the computer account and select **Reset Account**. (This resets the machine account password back to default without deleting the object or its group associations).
- **Resolution:**
  - On the client machine, use local admin credentials to join a temporary workgroup, restart the computer, and then join the domain again.
  - Alternatively, run the PowerShell command: `Reset-ComputerMachinePassword` to synchronize the passwords with the Domain Controller.

---
## 2. Technical Deep-Dive: Computer Password Sync & Secure Channels
Every computer joined to an Active Directory domain has a computer account object (similar to a user account). The workstation and the Domain Controller communicate over a secure channel.
- **Computer Password:** The computer account password is managed automatically by the operating system. By default, the workstation changes its account password with the Domain Controller every 30 days.
- **Out of Sync:** If a workstation is turned off or disconnected from the corporate network for several months, its local cached computer password can get out of sync with the Active Directory database, breaking the secure channel trust relationship.
### Ticket 1: Active Directory Secure Channel Trust Failure
- **Incident ID:** INC104488
- **Priority:** P3
- **Problem Statement:** "When I boot my laptop and try to log in, I get error: 'The trust relationship between this workstation and the primary domain failed'."
- **Diagnostics:**
  1. Logged on to the laptop using the local administrator profile.
  2. Verified network connection: The Domain Controller was reachable.
  3. Opened PowerShell as Administrator and checked the AD secure channel status:
     ```powershell
     Test-ComputerSecureChannel
     ```
     The command returned `False`, indicating the computer password was out of sync.
- **Resolution:**
  - Repaired the secure channel directly via PowerShell:
    ```powershell
    Test-ComputerSecureChannel -Repair -Credential (Get-Credential)
    ```
  - Entered domain administrator credentials, restarted the PC, and successfully logged on using the user's domain profile.
### Diagnose Computer Secure Channel Status (PowerShell)
```powershell
# Test if the workstation secure channel to the DC is functional
Test-ComputerSecureChannel -Verbose

# Repair the secure channel using a domain administrator credential
Test-ComputerSecureChannel -Repair -Credential (Get-Credential)
```
**Q1: What causes a "Trust relationship failed" error during workstation login?**
A: This error occurs when the computer account password stored in the workstation's local registry becomes out of sync with the password registered in the Active Directory database on the Domain Controller. This breaks the secure Kerberos communication channel.

**Q2: What is the purpose of Pre-staging a Computer Account in Active Directory?**
A: Pre-staging involves creating the computer object in Active Directory *before* joining the physical PC to the domain. This allows support engineers to specify which OU the computer will reside in and control which technicians have rights to join the PC to the domain.

## Troubleshooting Broken Secure Channel Trust
When a computer has been offline for a long period, its local password gets out of sync with Active Directory:
- **Symptom:** The user gets error: "The trust relationship between this workstation and the primary domain failed."
- **Resolution:** Reset the secure channel password via PowerShell on the client machine:
  `Reset-ComputerMachinePassword -Server "domaincontroller.company.local" -Credential (Get-Credential)`
  This synchronizes the machine password with the Domain Controller without needing to disjoin and rejoin the computer to the domain.

## Related Notes
- [[13-02 New PC Setup & Imaging SOP]] - Workstation imaging systems
- [[04-05 Group Policy for Support Engineers]] - Workstation policy controls