---
tags: [desktop-support, networking, subnetting]
created: 2026-06-25
status: Complete
difficulty: Intermediate
estimated-read-time: 15 mins
---

# 03-02 IP Addressing & Subnetting

> [!abstract] Overview
> An in-depth guide to IP addressing, subnetting, and CIDR notation in enterprise networks. This note covers IPv4 and IPv6 structures, classful vs. classless addressing, subnet mask calculations, and resolving IP address conflicts.

---

## 1. What Is It? (Concept Explanation)
An Internet Protocol (IP) address is a unique numerical label assigned to each device participating in a computer network that uses the Internet Protocol for communication. Subnetting is the practice of dividing a single physical network into multiple logical sub-networks (subnets).
*Seedha simple shabdon mein bole toh: IP address aapke computer ka network address hai, jaise post delivery ke liye aapka ghar ka address hota hai. Subnetting ka matlab hai ek bade network ko chote blocks (departments) mein divide karna, jaise ek company building mein Floor 1 HR ke liye, Floor 2 IT ke liye, aur Floor 3 Finance ke liye partition kiya jata hai. Isse security aur management dono strong hote hain aur network traffic control mein rehta hai.*

---

## 2. Technical Deep Dive: IPv4 Structure & CIDR
An IPv4 address consists of 32 bits, divided into four 8-bit octets (separated by dots, e.g., `192.168.1.1`). Each octet can have a decimal value from `0` to `255`. The address is divided into two parts: the **Network ID** and the **Host ID**.

### Classful IP Addressing
Historically, IP addresses were divided into classes based on the first octet:
- **Class A (1.0.0.0 to 126.0.0.0):** Default subnet mask `255.0.0.0` (/8). Designed for extremely large networks (16 million hosts per network).
- **Class B (128.0.0.0 to 191.255.0.0):** Default subnet mask `255.255.0.0` (/16). Designed for medium networks (65,534 hosts).
- **Class C (192.0.0.0 to 223.255.255.0):** Default subnet mask `255.255.255.0` (/24). Designed for small networks (254 hosts).
- **Loopback IP (127.0.0.1):** Reserved for internal host testing.
- **APIPA (169.254.0.0 to 169.254.255.255):** Automatically assigned by Windows when a DHCP server cannot be reached.

### Classless Inter-Domain Routing (CIDR)
CIDR replaces classful masking, allowing networks to be split into custom sizes. The slash notation (e.g., `/24`) indicates the number of bits turned "on" (1s) in the subnet mask:
- `/24` = `255.255.255.0` (256 IPs, 254 usable for hosts)
- `/25` = `255.255.255.128` (128 IPs, 126 usable hosts)
- `/26` = `255.255.255.192` (64 IPs, 62 usable hosts)
- `/30` = `255.255.255.252` (4 IPs, 2 usable hosts - used for point-to-point links)

---

## 3. Real-World Support Scenarios

### Scenario 1: Troubleshooting APIPA (169.254.x.x) Isolation
- **Incident Description (Situation):** A remote worker in the branch office connects their desktop to the wall outlet, but cannot access any local servers or the internet. `ipconfig` returns an IP address of `169.254.88.102`.
- **Diagnosis (Action):**
  1. Identify that the IP is in the APIPA range, meaning the client is isolated and did not receive a response from the DHCP server.
  2. Verify the physical link layer: Check if the network port status LEDs are active. They are lit.
  3. Release and request a new IP lease:
     ```cmd
     ipconfig /release
     ipconfig /renew
     ```
  4. The command times out with: "Unable to contact your DHCP server". Check if the wall outlet port is patched to the correct switch VLAN.
- **Resolution (Result):**
  - Traced the wall port ID to the network patch panel in the server cabinet.
  - Found the port was patched to a disabled testing switch port. Moved the cable to an active VLAN 10 (Workstations) port.
  - Ran `ipconfig /renew` on the client PC. It successfully obtained a valid IP of `10.10.10.45` and resolved connectivity.

### Scenario 2: Resolving a Static IP Conflict
- **Incident Description (Situation):** A network printer (`192.168.5.50`) randomly goes offline, and several users receive a Windows warning: "There is an IP address conflict with another system on the network."
- **Diagnosis & Resolution (Action):**
  1. Ping the printer's IP address: `ping 192.168.5.50`. It responds.
  2. Check the MAC address of the responding device:
     ```cmd
     arp -a
     ```
  3. Compare the MAC address returned in the ARP table with the printer's physical MAC address tag. They do not match.
  4. The conflicting device is a user's laptop that was manually configured with the printer's static IP address instead of using DHCP.
- **Resolution:**
  - Locate the conflicting laptop, open network adapter settings, and change the IPv4 configuration back to "Obtain an IP address automatically (DHCP)".
  - Add a DHCP reservation for the printer's MAC address on the DHCP server to reserve `192.168.5.50` exclusively for the printer.

---

## 4. SOP: Configuring a Static IP Address
Use this procedure when manually setting IP details for network resources:
1. Open the Network Connections window: Press `Win + R`, type `ncpa.cpl`, and press Enter.
2. Right-click the active network adapter -> select **Properties**.
3. Select **Internet Protocol Version 4 (TCP/IPv4)** -> click **Properties**.
4. Select **Use the following IP address** and input:
   - **IP Address:** `192.168.1.50` (Ensure this IP is excluded from the DHCP lease pool).
   - **Subnet Mask:** `255.255.255.0`
   - **Default Gateway:** `192.168.1.1` (The local router interface IP).
5. Select **Use the following DNS server addresses**:
   - **Preferred DNS:** `10.10.1.10` (Primary Domain Controller).
   - **Alternate DNS:** `8.8.8.8` (Public backup DNS).
6. Click OK to save. Open CMD and run `ping 192.168.1.1` to verify connectivity.

---

## 5. Pro Tips (Senior Engineer Secrets)
> [!tip] From the Field
> When reserving static IPs for corporate hardware (like servers or printers), never configure them on the client device without also reserving them on the DHCP server. This prevents address conflicts if the DHCP scope configuration changes in the future.

---

## 6. Interview Q&A Bank
**Q1: What does an IP address of 169.254.x.x mean?**
A: It indicates an APIPA (Automatic Private IP Addressing) address. Windows assigns this address when a device is configured for DHCP but cannot communicate with the DHCP server to obtain an IP lease.

**Q2: What is the difference between a Default Gateway and a Subnet Mask?**
A: A subnet mask determines which portion of the IP address represents the network ID and which represents the host ID, defining local network boundaries. The Default Gateway is the router interface IP used to send traffic outside the local network.

**Q3: How do you check if another device is using a specific IP address?**
A: Run a ping test to the IP address, and then run the `arp -a` command to check the ARP cache. Compare the returned MAC address with your device list to locate the conflicting hardware.

---

## Related Notes
- [[03-08 Network Diagnostic Commands]] - Network debugging commands
- [[03-03 Core Network Protocols]] - DHCP and DNS details
- [[12-09 IP Subnetting Quick Reference]] - Subnet calculation tables