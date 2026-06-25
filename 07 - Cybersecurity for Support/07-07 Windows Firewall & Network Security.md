---
tags: [desktop-support, security, firewall]
created: 2026-06-25
status: Complete
difficulty: Intermediate
estimated-read-time: 14 mins
---

# 07-07 Windows Firewall & Network Security

> [!abstract] Overview
> A guide to configuring Windows Defender Firewall and securing local networks. This note covers firewall profiles, inbound/outbound rules, and auditing blocked network connections.

---

## 1. What Is It? (Concept Explanation)
Windows Defender Firewall is a host-based firewall included with the operating system. It filters network traffic entering or leaving the system based on configured security rules.
*Seedha simple shabdon mein bole toh: Windows Firewall system ke portals (ports) ko control karta hai. Agar koi third-party software network connectivity chahta hai, toh uski settings firewall rule book mein check karni hoti hain. Domain computers par rules local level par edit nahi hote, balki GPO se deploy hote hain.*

---

## 2. Windows Firewall Profiles
The firewall applies rules based on three network connection profiles:
- **Domain Profile:** Applied when the computer is connected to the corporate Active Directory domain network.
- **Private Profile:** Applied when connected to trusted, private networks (like a user's home network).
- **Public Profile:** Applied when connected to untrusted public networks (like airport Wi-Fi). This profile applies the strictest security rules.

---

## 3. Real-World Scenarios

### Scenario 1: Adding an Exception for a Testing Utility
- **Incident Description:** An engineering testing tool needs to receive data on TCP port `9090` from external devices, but it fails to communicate when run on corporate laptops.
- **Troubleshooting Steps:**
  1. Open **Windows Defender Firewall with Advanced Security** (`wf.msc`) as Administrator.
  2. Select **Inbound Rules** -> click **New Rule...** in the Actions pane.
  3. Choose **Port** -> click Next. Select **TCP** and specify port `9090`.
  4. Select **Allow the connection**.
  5. Select the profiles where the rule applies (e.g., Domain and Private profiles). Name the rule "Engineering Port 9090 Exception".
- **Resolution:** Created the inbound firewall exception. The testing utility successfully connected and received data on port 9090.

---
## 2. Technical Deep-Dive: Firewall Profiles & Port Rules
The Windows Defender Firewall filters network traffic entering and leaving client systems:
- **Firewall Profiles:**
  - **Domain Profile:** Applied when the computer is connected to the corporate network domain controller.
  - **Private Profile:** Applied when connected to trusted home networks.
  - **Public Profile:** Applied when connected to untrusted public Wi-Fi networks (highest security settings).
- **Inbound vs. Outbound Rules:**
  - *Inbound Rules:* Filter traffic entering the PC (e.g. blocking port 3389 to stop RDP attacks).
  - *Outbound Rules:* Filter traffic leaving the PC.
### Ticket 1: RDP Connections Blocked by Local Firewall
- **Incident ID:** INC107712
- **Priority:** P3
- **Problem Statement:** "I cannot connect to a test desktop in the lab using Remote Desktop, although pinging the IP succeeds."
- **Diagnostics:**
  1. Ran a port check from the technician client: `Test-NetConnection -ComputerName 10.10.20.45 -Port 3389` (failed).
  2. Logged onto the lab PC locally. Opened Windows Defender Firewall settings.
  3. Found the Remote Desktop inbound rule was disabled under the active Domain Profile.
- **Resolution:** Enabled the Remote Desktop inbound rule, verified the port test succeeded, and successfully established an RDP connection.
### Enable Local Windows Firewall (PowerShell)
```powershell
# Enable the firewall for all profiles (Domain, Private, Public)
Set-NetFirewallProfile -Profile Domain, Private, Public -Enabled True
```
**Q1: Why does the Windows Firewall have three distinct profiles?**
A: To apply appropriate security settings based on the network connection type. The Domain profile allows administrative traffic on corporate networks, the Private profile supports device sharing at home, and the Public profile blocks inbound connections on untrusted public Wi-Fi.

## Advanced Logging of Blocked Packets
Technicians can trace port blocks by enabling local firewall drop logging:

### Enabling Firewall Logs
1. Press `Win + R`, type `wf.msc` to open Windows Defender Firewall with Advanced Security.
2. Click **Properties** on the right actions panel.
3. Select the active profile tab (Domain, Private, or Public).
4. Click Customize under Logging, and set **Log dropped packets** to **Yes**.
5. Save the configuration and view the output log file:
   `%systemroot%\System32\LogFiles\Firewall\pfirewall.log`

### Auditing Firewall Logs
Open the `pfirewall.log` in Notepad to audit blocked connections:
- **`DROP`:** Indicates a blocked connection.
- **`SEND` / `RECEIVE`:** Displays the source IP, destination IP, and target port.
If the log shows dropped packets to port 3389, it indicates a local firewall rule is blocking RDP traffic.

## Additional Pro-Tips for Firewall Rule Hardening
When configuring Windows Firewall policies for corporate systems, it is best practice to enforce the 'Default Block' rule for inbound connections while allowing outbound traffic. This blocks unauthorized external access to local resources (like files or remote desktop services) while allowing standard web browsing. If a specific business application requires external access, technicians create inbound rules restricted to the application's executable path, minimizing security risks.

## Related Notes
- [[03-09 Proxy & Firewall Basics]] - Network security routing
- [[12-02 CMD & PowerShell Commands Cheat Sheet]] - Diagnostic commands