---
tags: [desktop-support, sop, vpn]
created: 2026-06-25
status: Complete
difficulty: Intermediate
estimated-read-time: 15 mins
---

# 13-07 VPN Access Request & Troubleshooting SOP

> [!abstract] Overview
> Standard Operating Procedure (SOP) for provisioning remote secure access and troubleshooting VPN client connectivity drops. Covers active directory permissions group mapping, VPN client profile installation, and network routing diagnostics.

---

## 1. What Is It? (Concept Explanation)
A Virtual Private Network (VPN) creates a secure, encrypted tunnel over public networks, allowing remote employees (working from home or travel locations) to access internal corporate servers, shared drives, and databases safely.
*Seedha simple shabdon mein: Jab koi employee ghar se kaam karta hai, toh use company ke local files aur database servers ko open karne ke liye ek secure connection chahiye hota hai. VPN client (jaise Cisco AnyConnect ya GlobalProtect) internet connectivity ke upar ek encrypted path bana deta hai. support engineers ko iski access permissions AD se deni hoti hain aur connectivity drops ko troubleshoot karna hota hai.*

---

## 2. VPN Provisioning & Diagnostics Workflow
Follow these steps to configure and resolve VPN access issues:

- [ ] **Verify VPN Request Authorization:** Ensure the ticket has approvals from the employee's business unit manager and corporate security team.
- [ ] **Assign AD Group Permissions:**
  1. Open ADUC (`dsa.msc`).
  2. Locate the user, go to properties, and add them to the security group `SG-VPN-Users`.
  3. Verify the sync to Microsoft Entra ID is complete (if using cloud-based authentication).
- [ ] **Install VPN Client Profile:**
  1. Download and install the corporate VPN client package (e.g., *Cisco AnyConnect Secure Mobility Client*).
  2. Import the corporate connection profile containing the gateway address (e.g., `vpn.company.com`).
- [ ] **Configure MFA Authentication:**
  1. Instruct the user to log on to the VPN client using their corporate email and password.
  2. Verify that they receive and accept the MFA push notification on their mobile device.
- [ ] **Troubleshoot Connection Loops & Drops:**
  1. If the VPN client gets stuck in a "Connecting..." loop, verify the user's home internet connection is active.
  2. Run network diagnostics to check if corporate firewall ports are accessible.
  3. Flush client DNS resolver cache:
     ```cmd
     ipconfig /flushdns
     ```

---

## 3. Real-World Incident Tickets

### Ticket 1: VPN Authentication Fails due to MFA Code Out of Sync
- **Incident ID:** INC110590
- **Priority:** P3
- **Problem Statement:** "I cannot connect to the corporate VPN from home. The client accepts my password but returns error: 'Authentication Failed' before showing the MFA push notification."
- **Diagnostics:**
  1. Checked the user's account in Active Directory. Confirmed the account was active and not locked.
  2. Checked the MFA logs in the Microsoft Entra portal. Found multiple "Authentication request timed out" logs.
  3. Discovered the user was manually typing verification codes from their authenticator app, but the app clock was out of sync with network servers, causing the codes to expire instantly.
- **Resolution:** Had the user go to their mobile device settings, enabled "Set Time Automatically" to synchronize the clock, and retry. The authentication completed successfully and connected.

### Ticket 2: Split Tunneling Configuration Mismatch (IP Conflict)
- **Incident ID:** INC110605
- **Priority:** P2
- **Problem Statement:** "When I connect to the VPN from my home office, my internet connection drops completely, and I cannot access external sites like Google, although I can open corporate folders."
- **Diagnostics:**
  1. Connected to the user's system. Checked routing tables using `route print` in CMD.
  2. Discovered that the VPN adapter was configured as the default gateway, routing all public internet traffic through the secure corporate tunnel (Force Tunneling).
  3. Verified the corporate policy was configured for **Split Tunneling**, which routes only corporate traffic (`10.0.0.0/8`) through the VPN, leaving public traffic on the local home internet.
- **Resolution:** Cleaned up local network interface settings, uninstalled outdated VPN adapters, and reinstalled the corporate VPN client configuration package to download the correct split-tunneling policies.

---

## 4. Technical Diagnostic Commands
Identify routing and connection errors using these commands:
```cmd
:: Test reachability to the corporate VPN gateway server
ping vpn.company.com

:: Display active network interface routing tables
route print
```

---

## 5. Common Mistakes & Best Practices
- **Using Obsolete Protocols:** Never configure VPN profiles using unsecure PPTP or L2TP/IPsec protocols without certificates. Always use modern SSL VPN or IKEv2 protocols with MFA.
- **Ignoring Local IP Address Conflicts:** If a remote user's home router uses the same IP subnet as the corporate local network (e.g., `192.168.1.x`), IP routing conflicts will block access. Instruct the user to change their home router subnet (e.g., to `192.168.10.x`).

---

## 6. Pro Tips (Senior Engineer Secrets)
> [!tip] VPN Pre-Logon Configuration
> Enable the **Start Before Logon (SBL)** option in Cisco AnyConnect settings. This adds a VPN login tile to the Windows lock screen, allowing remote users to connect to the VPN *before* logging in to Windows. This is crucial for remote password resets and domain updates.

---

## 7. Interview Q&A Bank
**Q1: What is the difference between split tunneling and force tunneling on a VPN?**
A: Split tunneling routes only corporate-bound network traffic (e.g., directed to `10.x.x.x` addresses) through the secure VPN tunnel, while standard internet traffic (e.g., Google or YouTube) goes directly through the user's home ISP. Force tunneling routes all network traffic through the corporate firewall, which secures all data but consumes corporate internet bandwidth.

**Q2: A user can connect to the VPN but cannot access corporate shared folders. What is your troubleshooting plan?**
A: First, I verify if they have active name resolution by running `ping fileserver.corp.local`. If it fails, I ping the file server's IP address directly. If the IP ping succeeds, it indicates a DNS resolution issue. If both fail, I check if their AD account security group membership for the file share has expired, or check if the port is blocked on their home network firewall.

**Q3: Which network ports must be open on a home router to establish an IPsec VPN connection?**
A: IPsec VPNs require Port UDP `500` for ISAKMP negotiations and Port UDP `4500` for NAT Traversal (NAT-T). If the user's home router has a firewall blocking these ports, the VPN client will fail to connect.

---

## Related Notes
- [[03-07 VPN for Desktop Support]] - VPN routing concepts
- [[03-08 Network Diagnostic Commands]] - Checking routing hops
- [[13-04 Password Reset SOP]] - Resetting passwords for remote users