---
tags: [desktop-support, networking, ports]
created: 2026-06-25
status: Complete
difficulty: Beginner
estimated-read-time: 14 mins
---

# 03-04 Network Ports Master Reference

> [!abstract] Overview
> A master reference guide to network ports and protocols. This note covers port concepts, lists essential ports for desktop support, and details how to audit active ports using CMD and PowerShell.

---

## 1. What Is It? (Concept Explanation)
A network port is a logical endpoint for communication, represented by a number from `0` to `65535`. While an IP address identifies a device on a network, a port number identifies the specific application or service running on that device.
*Seedha simple shabdon mein bole toh: Agar IP address kisi office building ka physical address hai, toh Port Number building ka specific office room number hai. Mail delivery room 25 (SMTP) par jayegi, web queries room 443 (HTTPS) par hongi, aur remote access control room 3389 (RDP) par. Support engineers ko network connection issues troubleshoot karte waqt ye check karna hota hai ki specific port open aur traffic allow kar raha hai ya nahi.*

---

## 2. Port Categories
Port numbers are categorized by the Internet Assigned Numbers Authority (IANA):
- **Well-Known Ports (0 to 1023):** Reserved for system services and core network protocols (e.g., HTTP, DNS, SSH).
- **Registered Ports (1024 to 49151):** Assigned to specific applications and software services (e.g., Microsoft SQL, RDP).
- **Dynamic/Private Ports (49152 to 65535):** Used as temporary source ports by client applications when communicating with a server.

---

## 3. Essential Ports Reference Table

| Port Number | Protocol | Common Name | Service Description |
|---|---|---|---|
| **21** | TCP | FTP | File Transfer Protocol. Used for file transfers. |
| **22** | TCP | SSH | Secure Shell. Secure remote terminal access. |
| **25** | TCP | SMTP | Simple Mail Transfer Protocol. Sends emails. |
| **53** | TCP/UDP | DNS | Domain Name System. Translates URLs to IP addresses. |
| **80** | TCP | HTTP | Hypertext Transfer Protocol. Web browsing (Unsecure). |
| **110** | TCP | POP3 | Post Office Protocol v3. Retrieves emails locally. |
| **123** | UDP | NTP | Network Time Protocol. Synchronizes system clocks. |
| **143** | TCP | IMAP | Internet Message Access Protocol. Synced email retrieval. |
| **389** | TCP/UDP | LDAP | Lightweight Directory Access Protocol. Active Directory queries. |
| **443** | TCP | HTTPS | Hypertext Transfer Protocol Secure. Web browsing (Secure). |
| **445** | TCP | SMB | Server Message Block. Accesses corporate file shares. |
| **3389** | TCP | RDP | Remote Desktop Protocol. Windows remote desktop access. |

---

## 4. Real-World Support Scenarios

### Scenario 1: Troubleshooting Blocked Remote Desktop Connections
- **Incident Description (Situation):** A system administrator reports they cannot connect to a staging server (`192.168.5.110`) using Remote Desktop, receiving a connection timeout error.
- **Diagnosis (Action):**
  1. Verify the staging server is online by running a ping test: `ping 192.168.5.110`. It responds.
  2. Verify if port **3389** is open and listening on the server:
     ```powershell
     # Run in PowerShell to test TCP port connectivity
     Test-NetConnection -ComputerName 192.168.5.110 -Port 3389
     ```
  3. The cmdlet returns: `TcpTestSucceeded : False`. This confirms the port is blocked or the service is stopped.
- **Resolution (Result):**
  - Log on to the server console, open the Services console (`services.msc`), and verify **Remote Desktop Services** is running.
  - Open **Windows Defender Firewall** and enable the rule: **Remote Desktop - User Mode (TCP-In)** to allow inbound connections on port 3389.

---

## 5. Active Port Diagnostic Commands
Run these commands to audit network port states on client workstations:

```cmd
:: List all active network connections and listening ports with PIDs (CMD)
netstat -ano

:: Filter netstat output to check if RDP port 3389 is active (CMD)
netstat -ano | findstr 3389

:: Test port connection on remote target (PowerShell)
Test-NetConnection -ComputerName 10.10.1.20 -Port 443
```

---

## Secure vs Insecure Port Mappings
IT compliance rules require replacing legacy, unencrypted connection protocols with secure versions that encrypt data payloads in transit:

| Legacy Protocol (Insecure) | Port | Modern Protocol (Secure) | Port | Cryptographic Advantage |
|---|---|---|---|---|
| **Telnet** | 23 | **SSH** | 22 | Telnet sends credentials in plaintext. SSH encrypts the session. |
| **HTTP** | 80 | **HTTPS** | 443 | HTTPS uses SSL/TLS certificates to encrypt web traffic. |
| **LDAP** | 389 | **LDAPS** | 636 | LDAPS encrypts Active Directory queries, protecting user tokens. |
| **FTP** | 21 | **SFTP** | 22 | SFTP transfers files over an encrypted SSH connection tunnel. |
| **SMTP** | 25 | **SMTPS** | 465 | SMTPS encrypts email transmissions, blocking mail snooping. |

### Firewall Handling of Ports
Network firewalls inspect packet headers to filter incoming (inbound) and outgoing (outbound) network traffic:
- **Inbound Filtering:** Blocks unsolicited external traffic from entering the local network (e.g., blocking port 3389 inbound to stop external RDP attacks).
- **Outbound Filtering:** Restricts internal client computers from accessing unauthorized external ports, preventing malware from establishing outbound command-and-control connection sockets.

## Related Notes
- [[12-03 Network Ports Reference Table]] - Complete port reference table
- [[03-08 Network Diagnostic Commands]] - Network debugging CLI
- [[05-06 Remote Support Tools]] - Desktop remote control software