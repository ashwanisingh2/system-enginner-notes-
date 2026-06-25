---
tags: [desktop-support, troubleshooting, network]
created: 2026-06-25
status: Complete
difficulty: Intermediate
estimated-read-time: 12 mins
---

# 08-06 Network Connectivity Troubleshooting

> [!abstract] Overview
> A diagnostic manual for isolating network connectivity failures. This note covers Layer 1 to Layer 3 troubleshooting, DNS resolution, IP conflicts, and diagnostic command execution.

---

## 1. What Is It? (Concept Explanation)
Network connectivity troubleshooting involves systematically isolating why a workstation cannot communicate with local servers or external internet gateways. Technicians follow the bottom-up approach of the OSI Model to verify physical connections, IP addressing, and DNS routing.
*Seedha simple shabdon mein bole toh: Network issue tab aate hain jab email connect na ho ya sites open na hon. Sabse pehle physical cable check karo (Layer 1), fir check karo router se IP mila ya nahi (Layer 2-3), aur fir check karo domain name search (DNS) chal raha hai ya nahi. Ping aur nslookup commands iske diagnostic keys hain.*

---

## 2. Technical Deep-Dive: Layer 1-3 Diagnostic Steps
A support engineer must systematically isolate issues using the OSI model:

### Layer 1: Physical Layer
Verify the network cable (RJ-45) is plugged in firmly and the link LED lights on the computer's network interface card (NIC) are active (green for connection, amber flashing for activity).
- **Wi-Fi:** Ensure the wireless adapter is turned on and not in Airplane Mode.

### Layer 2: Data Link Layer (ARP & Switches)
Ensure the client's MAC address is registered and the network switch port is active.
- **Troubleshooting:** Run `getmac` to verify local adapter status. If a static IP conflict occurs (two devices sharing an IP), the local switch will drop packets.

### Layer 3: Network Layer (IP & Routing)
Verify the computer has a valid IP address.
- **APIPA Address:** If the PC displays an IP address starting with `169.254.x.x`, it means the client could not contact the DHCP server.
- **Diagnostics:**
  - `ping 127.0.0.1` (tests local NIC functionality).
  - `ping 10.0.0.1` (tests gateway router reachability).
  - `ping 8.8.8.8` (tests external internet connectivity).

### Layer 4-7: Transport to Application (DNS & Ports)
Verify domain name resolution is working. If you can ping `8.8.8.8` but cannot open `google.com`, the issue is a DNS resolution failure.
- **Diagnostics:** Run `nslookup google.com` to query the active DNS server.

---

## 3. Real-World Support Scenario (STAR Ticket)
- **Situation:** A sales director reports that they cannot access the local intranet portal or open Outlook. They are connected via Ethernet cable at their office desk. Pinging `8.8.8.8` returns timeouts.
- **Task:** Troubleshoot local network parameters, identify the connection failure, and restore internet access.
- **Action:**
  1. Opened Command Prompt on the user's PC and checked the IP configuration:
     ```cmd
     ipconfig /all
     ```
  2. Noticed the IPv4 Address showed `169.254.120.45` with a subnet mask of `255.255.0.0`. This indicated the computer was using an **APIPA address** and failing to reach the DHCP server.
  3. Inspected physical Layer 1 connections. Checked the network cable; it was connected, but plugged into a VoIP phone bridge port marked "Internal Use Only".
  4. Replugged the Ethernet cable directly into the wall ethernet jack.
  5. Cleared local network address cache and requested a new IP allocation:
     ```cmd
     ipconfig /release
     ipconfig /renew
     ```
  6. Verified the IP address changed to a valid corporate subnet IP `10.12.4.108` and the default gateway showed `10.12.4.1`.
  7. Pinged the gateway and corporate DNS server to verify.
- **Result:** Pings resolved successfully, Outlook reconnected, and the intranet portal loaded, restoring the user's workflow.

---

## 4. Network Diagnostic Commands Reference

| Command | Function | Target Troubleshooting |
|---|---|---|
| `ipconfig /all` | Displays complete network settings. | Checking IP address, DHCP server status, and DNS servers. |
| `ping -t <IP>` | Sends persistent ping requests. | Tracking intermittent network drops or latency spikes. |
| `tracert <domain>` | Traces the network path to a server. | Locating which router or gateway is dropping packets. |
| `nslookup <domain>` | Queries DNS server for records. | Diagnosing DNS server failures or missing domain entries. |
| `netstat -ano` | Lists active network connections and ports. | Identifying if an application port (like 3389) is active. |

---

## 5. Frequently Asked Questions (FAQ)

**Q1: What does a 169.254.x.x IP address mean?**
A: This is an Automatic Private IP Addressing (APIPA) address. It means the computer's network card is working, but it could not reach a DHCP server to get an IP address. Check network cabling or DHCP server status.

**Q2: What is the difference between ping and tracert?**
A: **Ping** sends a quick test packet to verify if a destination host is online and measures latency. **Tracert** maps the entire route path, listing every router (hop) the packet passes through, helping pinpoint where a connection drops.

**Q3: How do I release and renew a DHCP IP address?**
A: Open Command Prompt as Administrator and run:
`ipconfig /release` (drops the current IP assignment) followed by
`ipconfig /renew` (requests a new IP address from the DHCP server).

**Q4: How do I clear the local DNS resolver cache on Windows?**
A: Run the command `ipconfig /flushdns` in Command Prompt. This clears the local DNS database, forcing the computer to request fresh IP addresses for domains from the DNS server.

---

## Related Notes
- [[03-01 OSI Model & TCP-IP Suite]] - Network fundamentals
- [[12-03 Network Ports Reference Table]] - Ports directory
- [[09-05 Remote Management Commands]] - Scripting network tools