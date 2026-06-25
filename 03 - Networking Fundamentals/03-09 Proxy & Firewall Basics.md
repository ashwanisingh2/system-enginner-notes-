---
tags: [desktop-support, networking, proxy, firewall]
created: 2026-06-25
status: Complete
difficulty: Intermediate
estimated-read-time: 14 mins
---

# 03-09 Proxy & Firewall Basics

> [!abstract] Overview
> A guide to network proxies and firewalls. This note covers proxy configurations (PAC files), hardware vs. software firewalls, inbound/outbound rules, and troubleshooting blocked traffic.

---

## 1. What Is It? (Concept Explanation)
Proxies and firewalls act as security barriers that control traffic flowing between internal networks and the internet.
- **Firewall:** Monitors and filters network traffic based on security rules.
- **Proxy Server:** Acts as an intermediary between clients and the internet, caching data and filtering web requests.
*Seedha simple shabdon mein bole toh: Firewall system ka security guard hai jo rules ke mutabik bad traffic ko andar aane se rokta hai. Proxy ek mediator hai jo network speed badhane aur certain websites ko block karne ke liye use hota hai. Agar user ka internet browser open nahi ho raha par ping chal raha hai, toh setting mein proxy configuration check karni chahiye.*

---

## 2. Hardware vs. Software Firewalls
- **Software (Host-Based) Firewalls:** Installed directly on the operating system (e.g., Windows Defender Firewall). They protect individual host computers by filtering inbound and outbound connections.
- **Hardware (Network-Based) Firewalls:** Dedicated physical devices placed between the local network and the internet (e.g., Palo Alto, Fortinet). They filter traffic for the entire organization.

---

## 3. Real-World Scenarios

### Scenario 1: Browser Fails to Load Web Pages
- **Incident Description:** A user reports they cannot open external websites in Chrome or Edge, receiving a "Proxy Tunnel Connection Failed" error. However, they can ping external IP addresses (like `8.8.8.8`).
- **Troubleshooting Steps:**
  1. Open Windows Control Panel and search for **Internet Options**.
  2. Click the **Connections** tab, then select **LAN settings**.
  3. Notice that "Use a proxy server for your LAN" is checked, pointing to an obsolete proxy address (`192.168.10.200:8080`).
  4. Uncheck the proxy option or verify if **Use automatic configuration script (PAC)** is checked, pointing to the correct corporate PAC file URL (e.g., `http://config.corp.local/proxy.pac`).
- **Resolution:**
  - The proxy settings were manually changed by an installer application.
  - Reset the configuration to "Automatically detect settings", ran `gpupdate /force` to re-apply corporate proxy policies, and verified the browser could load web pages.

---

## 4. Firewall Troubleshooting Commands (PowerShell)
```powershell
# Get active Windows Firewall profiles (Domain, Private, Public)
Get-NetFirewallProfile | Select-Object Name, Enabled

# Create a rule to block port 80 outbound traffic locally
New-NetFirewallRule -DisplayName "Block HTTP Outbound" -Direction Outbound -LocalPort 80 -Protocol TCP -Action Block

# Show active firewall rules affecting RDP ports
Get-NetFirewallRule -DisplayGroup "Remote Desktop" | Select-Object DisplayName, Enabled, Action
```

---
## 2. Technical Deep-Dive: PAC Files & Firewall Filtering
Corporate security architectures route internet traffic through proxies and firewalls:
- **Proxy Servers:** Act as intermediaries, requesting web content on behalf of client browsers. They filter web traffic, block unauthorized categories, and cache pages to save bandwidth.
- **PAC (Proxy Auto-Config) Files:** Written in JavaScript. They tell the browser which proxy server to use based on the destination URL (e.g., internal URLs bypass the proxy, external URLs route to `proxy.company.com:8080`).
- **Firewall Rules:** Filter traffic based on ACLs (Access Control Lists). They inspect packet headers (Source IP, Destination IP, Protocol, Port Number) and apply a "Permit" or "Deny" action.
### Ticket 1: Stale PAC File Caching after Gateway Migration
- **Incident ID:** INC103940
- **Priority:** P3
- **Problem Statement:** "I cannot open external websites in Chrome. The browser displays error: 'ERR_PROXY_CONNECTION_FAILED'."
- **Diagnostics:**
  1. Pinged `8.8.8.8` (successful). Pinged `google.com` (successful), verifying DNS and Layer 3 routing were functional.
  2. Opened Chrome proxy settings. Found "Use automatic configuration script" was checked, pointing to `http://config.corp.local/proxy.pac`.
  3. Opened the PAC file URL in a text editor. Found it directed traffic to the old proxy server IP `10.10.1.100`, which was decommissioned last night.
- **Resolution:** Cleared the browser's proxy cache, forced a group policy update (`gpupdate /force`) to push the new PAC script URL pointing to the active proxy host, and restarted Chrome.
### Query Active Proxy Server Registry Configurations (CMD)
```cmd
reg query "HKCU\Software\Microsoft\Windows\CurrentVersion\Internet Settings" /v ProxyServer
```
**Q1: How does a proxy server differ from a network firewall?**
A: A firewall filters network packets at Layers 3 and 4 based on IP addresses and ports, blocking unauthorized traffic. A proxy server operates at the Application Layer (Layer 7), intercepting and modifying web traffic (HTTP/HTTPS) to apply content filtering, caching, and user authentication.

**Q2: What is the purpose of the "Automatically detect settings" option in Windows LAN Settings?**
A: This option utilizes the Web Proxy Auto-Discovery (WPAD) protocol. The client computer sends a DHCP or DNS query to locate a local configuration file (PAC) to automatically configure its proxy settings without manual administration.

## Related Notes
- [[06-04 Browser Troubleshooting]] - Browsers proxy settings
- [[07-07 Windows Firewall & Network Security]] - Local firewall settings