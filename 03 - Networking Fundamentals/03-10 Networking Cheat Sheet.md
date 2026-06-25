---
tags: [desktop-support, networking, cheatsheet]
created: 2026-06-25
status: Complete
difficulty: Beginner
estimated-read-time: 14 mins
---

# 03-10 Networking Cheat Sheet

> [!abstract] Overview
> A quick reference guide for network support. This note contains IP class tables, subnet masks, core network port references, and essential commands.

---

## 1. Quick Reference Tables

### IP Class Configurations
| Class | First Octet Range | Default Subnet Mask | Usable Host Range |
|---|---|---|---|
| **Class A** | `1` to `126` | `255.0.0.0` (/8) | ~16 Million Hosts |
| **Class B** | `128` to `191` | `255.255.0.0` (/16) | 65,534 Hosts |
| **Class C** | `192` to `223` | `255.255.255.0` (/24) | 254 Hosts |
| **APIPA** | `169.254` | `255.255.0.0` (/16) | Auto-assigned local link |

### Core Networking Ports Cheat Card
- **SSH (Remote Shell):** Port `22` (TCP)
- **DNS (Domain Resolution):** Port `53` (TCP/UDP)
- **HTTP (Web Traffic):** Port `80` (TCP)
- **HTTPS (Secure Web):** Port `443` (TCP)
- **IMAP (Email Sync):** Port `143` (TCP)
- **LDAP (Active Directory):** Port `389` (TCP)
- **RDP (Remote Desktop):** Port `3389` (TCP)

---

## 2. Essential CLI Diagnostics Reference Card

### Check network parameters
```cmd
ipconfig /all
```

### Flush DNS Resolver Cache
```cmd
ipconfig /flushdns
```

### Ping destination continuously
```cmd
ping -t 8.8.8.8
```

### Trace routing path
```cmd
tracert www.google.com
```

### Check active port states
```cmd
netstat -ano | findstr LISTENING
```

---
## 2. IP Subnetting & CIDR Mappings Table
Support engineers utilize these subnet structures during IP address configurations:

| CIDR Prefix | Subnet Mask | Total IP Block | Usable Hosts | Use Case |
|---|---|---|---|---|
| **/30** | `255.255.255.252` | 4 | 2 | Point-to-point links (WAN connections) |
| **/29** | `255.255.255.248` | 8 | 6 | Small server DMZ subnets |
| **/28** | `255.255.255.240` | 16 | 14 | Small branch office printers / local scopes |
| **/27** | `255.255.255.224` | 32 | 30 | Medium corporate departments |
| **/26** | `255.255.255.192` | 64 | 62 | Large local subnets |
| **/24** | `255.255.255.0` | 256 | 254 | Standard corporate workstation VLANs |
| **/22** | `255.255.252.0` | 1024 | 1022 | Large corporate facility scopes |
### Ticket 1: Wrong Default Gateway in Static Printer Setup
- **Incident ID:** INC104021
- **Priority:** P3
- **Problem Statement:** "A newly configured network printer can print files sent from PCs on the same floor, but cannot receive print jobs from users on other floors."
- **Diagnostics:**
  1. Connected to a PC on another floor. Tried to ping the printer's IP `10.10.40.50` (Request Timed Out).
  2. Connected to a PC on the printer's floor (VLAN 40). Pinged the printer (successful).
  3. Checked the printer panel configuration. Found the printer IP was `10.10.40.50` with mask `255.255.255.0`. The Default Gateway was set to `10.10.40.254` (incorrect - the actual gateway router interface was `10.10.40.1`).
- **Resolution:** Reconfigured the printer Default Gateway to `10.10.40.1` on the control panel, enabling packets to route outside the local subnet.
### Core Ports Quick Reference Table

| Port Number | Protocol | Service | Description |
|---|---|---|---|
| **22** | TCP | SSH | Encrypted CLI access |
| **53** | TCP/UDP | DNS | Domain Name System |
| **67 / 68** | UDP | DHCP | IP Address Allocation |
| **80 / 443** | TCP | HTTP/HTTPS | Web browsing data |
| **445** | TCP | SMB | Shared file folders |
| **3389** | TCP | RDP | Remote Desktop |
**Q1: Why is an IP address of `169.254.x.x` called a "Link-Local" address?**
A: It is assigned via APIPA when a DHCP server is unreachable. It is called link-local because it is only valid for communication within the local subnet link; routers will not forward packets using these addresses to other networks.

**Q2: What command shows the active routing table on a Windows client?**
A: I run the command `route print` or `netstat -r` in CMD to display the local IPv4/IPv6 active routing table, default gateways, and interface metrics.

## Quick Port Scanning & Routing Diagnostics
Desktop support engineers diagnose local and remote network routing states using CMD utility commands:

### Troubleshooting MTU and Packet Fragmentation
If a user reports that they can connect to the corporate network but certain large database files fail to load, the issue is often an incorrect Maximum Transmission Unit (MTU) size. Run a ping sweep to test packet fragmentation limit boundaries:
`ping -f -l 1472 10.10.1.1`
- `-f` : Sets the "Do Not Fragment" flag.
- `-l 1472` : Sends a packet size of 1472 bytes (which, combined with the 28-byte IP/ICMP header, equals the standard ethernet MTU of 1500 bytes).
- If the ping returns "Packet needs to be fragmented but DF set", the network router requires a lower MTU configuration (common on VPN connections, which often require an MTU of 1400 or 1450).

### Routing Table Queries
- **Display active client routing tables:**
  `route print`
- **Add a static persistent route for lab diagnostics:**
  `route add -p 192.168.50.0 mask 255.255.255.0 10.10.1.1`

## Related Notes
- [[03-02 IP Addressing & Subnetting]] - IP calculations details
- [[12-03 Network Ports Reference Table]] - Core network ports