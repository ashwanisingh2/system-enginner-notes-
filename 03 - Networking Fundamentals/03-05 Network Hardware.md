---
tags: [desktop-support, networking, hardware]
created: 2026-06-25
status: Complete
difficulty: Beginner
estimated-read-time: 14 mins
---

# 03-05 Network Hardware

> [!abstract] Overview
> A guide to enterprise network hardware. This note covers the roles of routers, switches, access points, and firewalls, and details physical-layer troubleshooting procedures.

---

## 1. What Is It? (Concept Explanation)
Network hardware comprises the physical devices that connect computers, printers, and servers to allow communication across local networks (LANs) and the internet (WAN).
*Seedha simple shabdon mein bole toh: Cables, Routers, Switches, aur Wireless Access Points humare network ke hardware pillars hain. Jab user offline hota hai, toh software checks karne se pehle hum cable check karte hain aur switch interface check karte hain.*

---

## 2. Core Network Devices

### 1. Switches (Layer 2)
Switches connect devices (PCs, printers, servers) within a Local Area Network (LAN). They read the **MAC address** of incoming data frames and route them directly to the destination device port, preventing network collisions.
- **Managed Switches:** Allow network teams to configure VLANs, monitor port traffic, and enable Port Security.

### 2. Routers (Layer 3)
Routers connect different networks (e.g., connecting your office LAN to the Internet). They read **IP addresses** of data packets and determine the best route to send them across networks.

### 3. Wireless Access Points (WAPs)
WAPs connect wireless devices (laptops, phones) to the wired network via radio signals, acting as wireless network extensions.

---

## 3. Real-World Scenarios

### Scenario 1: Troubleshooting physical line connection issues
- **Incident Description:** A customer reports their desktop computer displays a "Network Cable Unplugged" error, although they can see the Ethernet cable is connected to the wall jack.
- **Troubleshooting Steps:**
  1. Check the back of the PC. Note if the network card port LEDs are lit (solid green for link connection, flashing amber for data activity). Both are off.
  2. Inspect the RJ-45 connector clips on the cable. Replace the patch cable with a new Cat6 Ethernet cable.
  3. Locate the wall outlet ID tag (e.g., `FD-02-B12`).
  4. Trace this ID to the networking rack patch panel. Connect a cable tester to the user's wall jack and the patch panel port.
- **Resolution:**
  - The cable tester indicates a break on pin 3.
  - Replaced the network patch cable and moved the connection to a verified port on the wall outlet. The network link LEDs turned on and connection restored.

---

## 4. Infrastructure Hardware Checklist
- **Cat6 Ethernet Cabling:** Supports up to 10 Gbps speeds at a maximum distance of 55 meters (1 Gbps up to 100 meters).
- **Fiber Optic Cabling:** Used for high-speed backbone connections between switch racks. Resistant to electromagnetic interference.
- **Power over Ethernet (PoE):** Allows switches to send power down Ethernet cables to run access points, VoIP desks, and security cameras.

---
## 2. Technical Deep-Dive: MAC Address Learning & PoE Standards
Managed switches build a Media Access Control (MAC) address table in content-addressable memory (CAM). When a frame enters a switch port, the switch reads the source MAC address and records it along with the port ID. If the destination MAC is unknown, the switch floods the frame to all ports except the source port.
Power over Ethernet (PoE) allows switches to deliver electrical power over standard Cat6 Ethernet cabling. Standard specifications include:
- **802.3af (PoE):** Delivers up to 15.4W of DC power. Suitable for standard IP phones and basic security cameras.
- **802.3at (PoE+):** Delivers up to 30W of DC power. Required for dual-band wireless access points (WAPs) and pan-tilt-zoom cameras.
- **802.3bt (PoE++):** Delivers up to 60W or 90W of DC power. Used for video conferencing screens and building lighting systems.
### Ticket 1: Managed Switch Loop due to Rogue Ethernet Cable
- **Incident ID:** INC103502
- **Priority:** P1 (Network Outage)
- **Problem Statement:** "Users on the 2nd floor are losing local network connection. Pings to the default gateway show 80% packet loss."
- **Diagnostics:**
  1. Checked the 2nd-floor managed switch cabinet. Noticed all port LEDs were flashing rapidly in unison (broadcast storm).
  2. Logged into the switch console. CPU usage was at 100%. Spanning Tree Protocol (STP) logs showed constant topology changes.
  3. Traced ports and found port 12 and 14 had the same MAC address signatures. A user had plugged both ends of a single patch cable into two wall jacks in their office, creating a physical network loop.
- **Resolution:** Disconnected the rogue patch cable from the wall jacks. STP immediately stabilized the loop, switch CPU usage dropped to 2%, and network pings returned to <1ms.
### Switch Interface Status Verification (Cisco CLI)
```text
switch# show interfaces status
Port      Name               Status       Vlan       Duplex  Speed Type
Gi1/0/1   U-Finance-PC       connected    10         a-full  a-1000 10/100/1000BaseTX
Gi1/0/2   U-HR-PC            connected    20         a-full  a-1000 10/100/1000BaseTX
Gi1/0/3   U-Printer-Sales    connected    30         a-full  a-100  10/100/1000BaseTX
```
**Q1: What is the difference between a Layer 2 and a Layer 3 switch?**
A: A Layer 2 switch routes network frames based solely on physical MAC addresses within a single local subnet. A Layer 3 switch combines Layer 2 switching with Layer 3 routing capabilities, allowing it to route packets between different subnets (VLANs) internally using IP addresses, reducing router workloads.

**Q2: What occurs if you connect a PoE device to a non-PoE switch port?**
A: Nothing bad happens. Standard PoE switches use a handshake mechanism called "signature detection" to send a low-voltage test signal down the cable. If the connected device does not signal that it requires power, the switch routes standard data packets only, protecting non-PoE network cards from electrical damage.

## Related Notes
- [[03-01 OSI Model & TCP-IP Suite]] - Layer definitions
- [[03-06 Wi-Fi Troubleshooting Deep Dive]] - Wireless support guide
- [[13-07 VPN Access Request & Troubleshooting SOP]] - Remote infrastructure access