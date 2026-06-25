---
tags: [desktop-support, networking, vpn]
created: 2026-06-25
status: Complete
difficulty: Intermediate
estimated-read-time: 14 mins
---

# 03-07 VPN for Desktop Support

> [!abstract] Overview
> A guide to Virtual Private Networks (VPNs). This note covers split-tunneling vs. full-tunneling, VPN protocols, troubleshooting connection drops, and resolving client-side authentication errors.

---

## 1. What Is It? (Concept Explanation)
A Virtual Private Network (VPN) creates an encrypted tunnel across the public internet, allowing remote users to connect securely to the internal corporate network.
*Seedha simple shabdon mein bole toh: VPN remote employees ke liye virtual cable ki tarah hai. Iske bina, ghar par baitha employee office ke resources (jaise file share servers ya internal web portals) ko open nahi kar sakta. VPN connection problems split tunneling issue ya network certificate errors se aati hain.*

---

## 2. VPN Routing Methods
- **Full Tunneling:** All network traffic from the client PC (intranet sites, personal browsing, YouTube) is routed through the corporate VPN tunnel. This secures all traffic but consumes corporate network bandwidth.
- **Split Tunneling:** Only traffic bound for internal corporate servers (e.g., `10.x.x.x` addresses) is routed through the VPN tunnel. Web browsing and cloud services go directly through the user's home internet, preserving company bandwidth.

---

## 3. Real-World Scenarios

### Scenario 1: User Connects to VPN but Cannot Access Intranet Pages
- **Incident Description:** An employee working from home connects successfully to Cisco AnyConnect VPN, but they cannot open the internal timesheet page, and their Teams client is showing a "Connecting..." status.
- **Troubleshooting Steps:**
  1. Verify the VPN client connection status. Note the assigned virtual IP address (e.g., `172.16.50.84`).
  2. Test routing to the internal host using a path trace:
     ```cmd
     tracert timesheet.corp.local
     ```
  3. The trace times out at the home router gateway, indicating that corporate traffic is not routing through the VPN virtual adapter.
  4. Check the client routing table:
     ```cmd
     route print
     ```
  5. Check if the default gateway metric is overriding the VPN virtual interface settings.
- **Resolution:**
  - Reinstalled the AnyConnect client profile config files.
  - The corporate profile config files had been corrupted, which disabled split-tunneling rules and blocked intranet DNS resolution. Reinstalling restored normal access.

---

## 4. Common VPN Troubleshooting Checklist
- [ ] **Home Network Conflicts:** Verify the user's home router subnet isn't `192.168.1.x` if the corporate network uses the same subnet range. This IP address conflict blocks traffic routing.
- [ ] **Internet Speed Verification:** VPN tunnels add encryption overhead. Verify the user has at least 15 Mbps download and 5 Mbps upload speeds on their home internet connection.
- [ ] **MFA Token Sync:** Confirm the authentication token is synchronized on the user's mobile device.

---
## 2. Technical Deep-Dive: VPN Tunnels & Encapsulation
A VPN uses tunneling protocols to encapsulate and encrypt data packets:
- **IPsec (Internet Protocol Security):** Operates at the Network Layer (Layer 3). Encapsulates the entire IP packet using Encapsulating Security Payload (ESP). Highly secure, but requires specialized network configurations.
- **SSL/TLS VPN:** Operates at the Application Layer (Layer 7). Routes traffic over standard HTTPS (Port 443). Extremely flexible because it bypasses home router firewall blocks.
- **Split Tunneling Math:** When split tunneling is active, the local routing table is modified:
  - Corporate range (e.g., `10.0.0.0/8`) routes to interface `10.240.12.5` (VPN Virtual Adapter).
  - All other traffic (`0.0.0.0/0`) routes to interface `192.168.1.1` (Local Home Router).
### Ticket 1: VPN Disconnects Immediately upon IP Address Collision
- **Incident ID:** INC103714
- **Priority:** P3
- **Problem Statement:** "I cannot connect to the corporate VPN from my home. The connection progress bar reaches 90% and then closes without an error code."
- **Diagnostics:**
  1. Connected to the user's laptop while they were off the VPN. Checked the local IP address details: `10.1.10.45` with mask `255.255.255.0`.
  2. Discovered their home router was configured to lease IPs in the `10.1.10.x` range.
  3. This conflicted with the primary subnet of the corporate Active Directory server (`10.1.10.x`), creating a routing collision.
- **Resolution:** Instructed the user to log into their home router panel, changed their DHCP range to the standard consumer pool `192.168.1.x`, rebooted the laptop to pull the new IP, and successfully connected to the VPN.
### Verify VPN Virtual Adapter Connection Status (PowerShell)
```powershell
Get-NetAdapter | Where-Object {$_.InterfaceDescription -like "*VPN*"} | Select-Object Name, Status, LinkSpeed
```
**Q1: Why does home network IP address space collide with corporate VPN connections?**
A: If a remote worker's home router leases IPs on the exact same subnet as corporate database servers (e.g., both use `10.1.1.x`), the client operating system routing engine cannot distinguish between local home devices and remote corporate servers. This routing conflict causes data packets to drop.

**Q2: What is the difference between SSL VPN and IPsec VPN?**
A: SSL VPN runs over standard Port 443 (HTTPS) and is easy to set up because home firewalls rarely block web traffic. IPsec VPN runs at Layer 3, securing all network interactions, but requires ports UDP 500 and UDP 4500 to be open, which home ISP routers can sometimes block.

## Related Notes
- [[13-07 VPN Access Request & Troubleshooting SOP]] - VPN provisioning SOP
- [[03-08 Network Diagnostic Commands]] - Remote troubleshooting commands