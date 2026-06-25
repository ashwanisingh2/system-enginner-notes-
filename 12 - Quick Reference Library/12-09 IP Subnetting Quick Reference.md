---
tags: [desktop-support, cheatsheet, subnetting]
created: 2026-06-25
status: Complete
difficulty: Intermediate
estimated-read-time: 15 mins
---

# 12-09 IP Subnetting Quick Reference

> [!abstract] Overview
> A production-grade IP subnetting and CIDR quick reference guide. This note explains subnetting binary math, provides a complete CIDR translation chart, and details host range calculation workflows.

---

## 1. What Is It? (Concept Explanation)
IP Subnetting is the mathematical process of dividing a physical network into smaller, logical sub-networks (subnets). In IP address configurations, the **Subnet Mask** determines which portion of the 32-bit IP address belongs to the Network identifier and which belongs to the Host identifier.
*Seedha simple shabdon mein: Subnetting ek bade network ko chote departments mein divide karne ka tarika hai. Jaise ek office mein department-wise IP ranges allot ki jati hain (jaise Sales ke liye 192.168.1.x aur IT ke liye 192.168.2.x), taaki system security aur network speed behtar rahe. Subnet mask computer ko batata hai ki uske local network ka boundary area kahan tak hai.*

---

## 2. Complete CIDR & Subnet Mappings Chart
This reference table lists IP mask configurations:

| CIDR Prefix | Subnet Mask | Total IP Addresses | Usable Host IP Addresses | Network Range Focus |
|---|---|---|---|---|
| **/30** | `255.255.255.252` | 4 | 2 | Point-to-point links (Router connections) |
| **/29** | `255.255.255.248` | 8 | 6 | Small server group |
| **/28** | `255.255.255.240` | 16 | 14 | Remote branch office devices |
| **/27** | `255.255.255.224` | 32 | 30 | Medium department |
| **/26** | `255.255.255.192` | 64 | 62 | Large department |
| **/25** | `255.255.255.128` | 128 | 126 | Large subnet |
| **/24** | `255.255.255.0` | 256 | 254 | Standard corporate office LAN segment |
| **/23** | `255.255.254.0` | 512 | 510 | Enterprise floor network |
| **/22** | `255.255.252.0` | 1024 | 1022 | Large corporate facility |
| **/16** | `255.255.0.0` | 65536 | 65534 | Global WAN infrastructure segment |

---

## 3. How to Calculate Usable Host Ranges
For any subnet, two IP addresses are reserved and cannot be assigned to host devices:
1. **Network Address:** The first IP address in the block. Identifies the subnet.
2. **Broadcast Address:** The last IP address in the block. Used to send packets to all hosts on the subnet.

### Host Calculation Formula:
$$\text{Usable Hosts} = 2^{(32 - N)} - 2$$
*(where $N$ represents the CIDR prefix number).*

### Example Calculation:
For a `/26` prefix network:
- Number of host bits = $32 - 26 = 6$ bits.
- Total IPs = $2^6 = 64$.
- Usable Hosts = $64 - 2 = 62$.

---

## 4. Diagnostics SOP: Verifying IP Subnet Mismatch
When a client machine can ping local resources but cannot access the Internet:
1. Open Command Prompt and check IP configurations:
   ```cmd
   ipconfig /all
   ```
2. Note the IPv4 Address (e.g., `192.168.1.150`), Subnet Mask (e.g., `255.255.255.0`), and Default Gateway (e.g., `192.168.2.1`).
3. Analyze the boundary:
   - For Subnet Mask `255.255.255.0` (/24), all local hosts must be in the `192.168.1.x` range.
   - The Default Gateway is `192.168.2.1` (on a different subnet).
4. Since the gateway is outside the local subnet boundary, the client cannot route packets outside the LAN.
5. Set the Default Gateway to the correct local router IP (e.g., `192.168.1.1`) to restore access.

---

## 5. Study & Interview Q&A
**Q1: If a workstation has an IP address of `192.168.1.50` and a subnet mask of `255.255.255.240`, can it communicate directly with `192.168.1.60`?**
A: No. A subnet mask of `255.255.255.240` (/28) divides the network into blocks of 16 IPs (e.g., `0-15`, `16-31`, `32-47`, `48-63`). The IP `192.168.1.50` falls into the fourth subnet block (`48-63`), while `192.168.1.60` also falls inside this range, so they *can* communicate. However, if the IP was `192.168.1.65`, it would belong to the fifth block (`64-79`), requiring a router to communicate.

**Q2: What is the purpose of APIPA range `169.254.0.0/16`?**
A: APIPA (Automatic Private IP Addressing) is a fail-safe mechanism in Windows. When a device is configured for DHCP but fails to reach a DHCP server, Windows automatically assigns it an IP address in the `169.254.0.1` to `169.254.255.254` range with a `/16` mask. This allows local link-local communication but does not support routing to the internet.

**Q3: How do you find the broadcast address of a `/24` network starting at `10.10.1.0`?**
A: A `/24` network has a subnet mask of `255.255.255.0`, meaning the first three octets identify the network and the last octet is for hosts (256 addresses). The network address is `10.10.1.0`. The broadcast address is the last IP address in the block, which is `10.10.1.255`.

---

## Related Notes
- [[03-02 IP Addressing & Subnetting]] - Detailed network boundary concepts
- [[03-08 Network Diagnostic Commands]] - Checking active IP adapter configurations
- [[12-02 CMD & PowerShell Commands Cheat Sheet]] - Diagnostic commands