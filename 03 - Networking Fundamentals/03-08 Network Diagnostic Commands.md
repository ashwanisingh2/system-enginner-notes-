---
tags: [desktop-support, networking, commands]
created: 2026-06-25
status: Complete
difficulty: Beginner
estimated-read-time: 14 mins
---

# 03-08 Network Diagnostic Commands

> [!abstract] Overview
> A reference guide to diagnostic network commands. This note covers using ipconfig, ping, tracert, nslookup, netstat, and route print to isolate and resolve connectivity issues.

---

## 1. What Is It? (Concept Explanation)
Network diagnostic tools are command-line utilities built into the operating system. They allow support engineers to verify IP settings, test latency, trace routing, and check active network connections.
*Seedha simple shabdon mein bole toh: Jab koi user bolta hai "intranet kaam nahi kar raha," toh support engineer sabse pehle Command Prompt kholta hai. `ipconfig` se local IP dekhte hain, `ping` se network connection check karte hain, aur `tracert` se check karte hain ki traffic kis router par ruk raha hai.*

---

## 2. The Network Commands Library

### 1. ipconfig (IP Configuration)
Displays TCP/IP network configuration details for all active adapters and refreshes DHCP and DNS configurations.
- `ipconfig /all` : Shows detailed adapter parameters, including MAC address, DHCP lease times, and DNS servers.
- `ipconfig /flushdns` : Purges the DNS resolver cache.

### 2. ping (Packet Internet Groper)
Sends ICMP Echo Request packets to a target IP or domain to test basic connectivity and measure latency.
- `ping -t 10.10.1.1` : Sends continuous ping requests until stopped using `Ctrl + C`. Useful for monitoring connection drops.

### 3. tracert (Trace Route)
Traces the route path data packets take to reach a destination. It displays each router hop along the way.
- *Tip:* If a ping fails, running a trace route shows if the connection is dropping at the local gateway, the ISP network, or the destination server firewall.

### 4. netstat (Network Statistics)
Displays active TCP connections, listening ports, and ethernet statistics.

---

## 3. Real-World Scenarios

### Scenario 1: Isolating a Slow Corporate Connection
- **Incident Description:** A client reports that accessing the remote database application (`10.50.2.15`) is slow, causing app timeouts and screen locks.
- **Troubleshooting Steps:**
  1. Open Command Prompt.
  2. Run a continuous ping to measure latency and packet loss:
     ```cmd
     ping -n 50 10.50.2.15
     ```
  3. Notice that 8% of packets are lost, and average latency spikes from 15ms to 850ms.
  4. Run a trace route to find where the latency is occurring:
     ```cmd
     tracert 10.50.2.15
     ```
  5. The trace shows low latency (<5ms) up to hop 4 (local firewall), but spikes to 800ms at hop 5 (remote site WAN gateway router).
- **Resolution:** Provided the traceroute logs to the Network Engineering team. They identified an overloaded WAN uplink port on the remote gateway and routed traffic to a backup interface.

---

## 4. Quick Reference Commands Card
```cmd
:: Display network connection details
ipconfig /all

:: Clear the local DNS cache
ipconfig /flushdns

:: Ping a remote host continuously
ping -t 192.168.1.10

:: Trace routing path to a web server
tracert www.google.com

:: Display active TCP connections with processing PIDs
netstat -ano
```

---
## 2. Technical Deep-Dive: ICMP & Name Resolution Routines
Network diagnostic commands allow support engineers to trace packet flow:
- **Ping (ICMP Echo):** Sends an Internet Control Message Protocol (ICMP) Echo Request packet to a target IP. The target responds with an Echo Reply. Ping measures round-trip time and packet loss percentage.
- **Tracert (TTL Expiration):** Traces network paths by incrementing the Time-to-Live (TTL) field of packets by 1 for each sequence. As routers decrement TTL, they discard packets when TTL reaches 0 and send an ICMP Time Exceeded message back to the sender, identifying the router interface.
- **NSLookup (DNS Queries):** Queries the local DNS resolver cache or queries Domain Name System servers directly to locate records (A, CNAME, MX).
### Ticket 1: Local Switch Port Duplex Mismatch
- **Incident ID:** INC103822
- **Priority:** P3
- **Problem Statement:** "My computer is extremely slow accessing local shared folders. Pinging the server succeeds but has high latency."
- **Diagnostics:**
  1. Ran `ping -t fileserver` from the client PC. Observed average response times of 120ms with 5% packet loss (normal LAN ping is <2ms).
  2. Opened CMD as Administrator and ran `netstat -e` to check ethernet statistics. Found a massive count of **Errors** and **Discards** on the network interface.
  3. Checked the switch port configuration. The switch port was set to "100Mbps Full Duplex" while the client network card had auto-negotiated to "100Mbps Half Duplex" (duplex mismatch).
- **Resolution:** Reconfigured the switch port to Auto-Negotiate Duplex and Speed. The client card negotiated to Gigabit Full Duplex, and latency dropped to <1ms.
### Advanced Network Audits Command Table (PowerShell)
```powershell
# Resolve DNS name and query specific IP records
Resolve-DnsName -Name google.com -Type A

# Test network port 3389 (RDP) on a target host
Test-NetConnection -ComputerName 10.100.2.12 -Port 3389
```
**Q1: What does an IP address of `0.0.0.0` in netstat outputs signify?**
A: In `netstat` outputs, an IP address of `0.0.0.0` next to a listening port indicates that the service is bound to "all active network interfaces" on the machine, meaning it is accepting connections from local loopback, local Wi-Fi, and Ethernet adapters.

**Q2: What is the diagnostic difference between a ping timeout and a "Destination Host Unreachable" error?**
A: A "Request Timed Out" means the packet traveled to the target network, but the destination device did not respond within the deadline (often blocked by a firewall or powered off). "Destination Host Unreachable" means the local router could not find a path to the target subnet and dropped the packet locally.

## Related Notes
- [[09-01 CMD Masterclass for Desktop Support]] - CMD terminal commands
- [[09-02 PowerShell for Desktop Support]] - PowerShell cmdlets