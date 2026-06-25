---
tags: [desktop-support, interview, qa, reference]
created: 2026-06-25
status: Complete
difficulty: Intermediate
estimated-read-time: 25 mins
---

# 11-01 Top 100 Desktop Support Interview Q&A

> [!abstract] Overview
> Complete reference bank containing 100 essential technical, situational, and behavioral interview questions and model answers for desktop support engineers.

---

## What Is It? (Concept Explanation)
This note acts as a complete certification and career readiness mock engine. It compiles the top technical questions asked during enterprise IT screening rounds.
*Seedha simple shabdon mein: Yeh pure vault ka master interview bank hai. Yahan hardware, OS, network, AD, aur corporate troubleshooting ke 100 questions aur answers diye gaye hain.*

---

## Technical Q&A Bank (All 100 Questions)

### Q1: What is the primary function of the CPU?
**Answer:** The CPU (Central Processing Unit) acts as the brain of the computer, executing program instructions and processing calculations. Key performance metrics include clock speed (GHz), number of cores, and threads.
---

### Q2: What is RAM and is it volatile?
**Answer:** RAM (Random Access Memory) is high-speed temporary storage used by the OS and applications to store active data. It is volatile, meaning all stored data is cleared when the system loses power.
---

### Q3: Difference between HDD and SSD?
**Answer:** HDDs are mechanical drives with spinning platters and moving heads (slow, vulnerable to drops). SSDs use flash memory chips with no moving parts (much faster, silent, and physically durable).
---

### Q4: What does NVMe stand for and why is it faster than SATA?
**Answer:** NVMe stands for Non-Volatile Memory Express. It connects directly to the motherboard via PCIe lanes, bypassing the slower SATA interface and bottleneck, reaching speeds over 7000MB/s.
---

### Q5: Explain what a Motherboard does.
**Answer:** The motherboard is the main printed circuit board (PCB) that physically connects and routes communication between the CPU, RAM, storage, expansion cards, and external peripherals.
---

### Q6: What is the function of the CMOS battery?
**Answer:** The CMOS battery (typically a CR2032 button cell) powers the CMOS RAM chip on the motherboard, saving BIOS settings, hardware configurations, and the system clock when the PC is shut down and unplugged.
---

### Q7: What is POST in computer booting?
**Answer:** POST stands for Power-On Self-Test. It is a diagnostic sequence run by the BIOS/UEFI on startup to verify that core hardware components (CPU, RAM, video card, storage) are functional before booting the OS.
---

### Q8: What do motherboard beep codes indicate?
**Answer:** Beep codes are audio alerts emitted by the motherboard's internal speaker during POST when a critical hardware failure (like missing RAM or GPU failure) prevents displaying error codes on the monitor.
---

### Q9: How do you test a failing desktop PSU?
**Answer:** I perform the 'paperclip test' by shorting the PS_ON pin (pin 16, green) with any Ground pin (black) on the 24-pin connector, or use an LCD PSU tester to verify voltage outputs (+12V, +5V, +3.3V).
---

### Q10: What is the difference between DisplayPort and HDMI?
**Answer:** HDMI is common in consumer electronics. DisplayPort is the corporate standard, offering higher bandwidth, support for high refresh rates, and daisy-chaining multiple monitors from a single port.
---

### Q11: How do you fix a 'USB Device Not Recognized' error?
**Answer:** I check if the port is dirty, swap to a different USB port on the back of the motherboard, uninstall the USB host controllers in Device Manager and restart, or check for missing manufacturer chipset drivers.
---

### Q12: What are the common symptoms of CPU overheating?
**Answer:** Common symptoms include sudden system freezes, cooling fans running at maximum RPM, thermal throttling (sudden slowdown under workload), and automatic shut downs (thermal cut-off at ~100C).
---

### Q13: What is a laptop docking station?
**Answer:** A docking station connects to a laptop via a single cable (typically USB-C or Thunderbolt) and replicates ports like Ethernet, external displays, USB controllers, and charges the laptop simultaneously.
---

### Q14: How do you troubleshoot a flickering monitor?
**Answer:** I check if the display cable is loose or damaged, verify the screen refresh rate settings in Windows Display properties, update the graphics driver, or test the monitor with another system to rule out panel failure.
---

### Q15: What is ESD and why is it dangerous in hardware support?
**Answer:** ESD (Electrostatic Discharge) is the sudden flow of static electricity between two objects. It can destroy sensitive electronic components (like CPU and RAM chips) silently. Engineers must use ESD wrist straps or touch grounded metal before working.
---

### Q16: What is the HAL in Windows?
**Answer:** The Hardware Abstraction Layer (HAL) is a low-level dynamic library (hal.dll) that sits between the OS kernel and motherboard circuits, hiding physical hardware differences from the operating system.
---

### Q17: What is the difference between KMS and MAK activation?
**Answer:** KMS activates Windows clients locally against an internal KMS host server over the LAN every 180 days. MAK is a volume key that activates a device directly against Microsoft servers once via the internet.
---

### Q18: What is the Windows Registry?
**Answer:** The registry is a hierarchical database that stores low-level configuration settings for the Windows operating system, installed drivers, and applications.
---

### Q19: What are the five main registry hives?
**Answer:** HKLM (Local Machine), HKCU (Current User), HKCR (Classes Root), HKU (Users), and HKCC (Current Config).
---

### Q20: How do you stop a service that is stuck on 'Stopping'?
**Answer:** I find the service's Process Identifier (PID) in Task Manager or via CMD (`sc queryex service_name`), then run `taskkill /f /pid <PID>` as Administrator to force terminate it.
---

### Q21: What is the default application order of Group Policies?
**Answer:** GPOs are applied in the order: Local, Site, Domain, and Organizational Unit (LSDOU). Policies applied later override policies applied earlier.
---

### Q22: How do you immediately update GPOs on a client machine?
**Answer:** I open CMD as administrator and run the command `gpupdate /force`.
---

### Q23: What is Event Viewer used for?
**Answer:** It is a native Windows logging utility. Desktop support engineers use it to view System and Application logs to trace the root cause of crashes and errors.
---

### Q24: What is Event ID 41 in Windows?
**Answer:** Event ID 41 (Kernel-Power) indicates the system rebooted or shut down unexpectedly without performing a clean shutdown sequence first (e.g., power loss or BSOD).
---

### Q25: How do you troubleshoot stuck Windows Updates?
**Answer:** I stop the `wuauserv` and `bits` services, delete or rename the `C:\Windows\SoftwareDistribution` download cache folder, restart the services, and check for updates again.
---

### Q26: What is a Windows temporary profile and why does it occur?
**Answer:** A temp profile occurs when Windows fails to load the user's registry hive (ntuser.dat). Windows creates a temporary session and discards any changes when the user logs off. It is fixed by modifying the ProfileList registry registry keys.
---

### Q27: What is UAC and what is its purpose?
**Answer:** UAC (User Account Control) prevents unauthorized changes to the OS. It prompts standard users for admin credentials and asks administrators for approval before executing elevated tasks.
---

### Q28: What does SFC /scannow do?
**Answer:** System File Checker (SFC) scans the integrity of all protected system files and replaces corrupted, modified, or missing files with healthy copies from the local WinSxS cache folder.
---

### Q29: What does DISM do and why run it before SFC?
**Answer:** DISM checks and repairs the local Windows system image cache using files from Windows Update. Running DISM first ensures SFC has a healthy image repository to pull files from during repairs.
---

### Q30: What is Safe Mode in Windows?
**Answer:** Safe Mode is a diagnostic boot state that loads Windows with a minimal set of core drivers and services, bypassing startup apps and third-party drivers to isolate system crashes.
---

### Q31: How do you view which GPOs are applied to a user?
**Answer:** I run the command `gpresult /r` in CMD or generate an HTML report using `gpresult /h report.html`.
---

### Q32: Where is the local user profile folder located?
**Answer:** User profiles are stored in `C:\Users\<username>`. App-specific settings are in the hidden `AppData` subfolder (Local, Roaming, LocalLow).
---

### Q33: What is the Windows Pagefile?
**Answer:** The pagefile (`pagefile.sys`) is virtual memory on the storage drive that Windows uses as an extension of physical RAM when system memory is fully utilized.
---

### Q34: How do you access the Windows Recovery Environment (WinRE)?
**Answer:** I hold the `Shift` key while clicking **Restart** in Windows, or boot the PC using a Windows installation USB and select 'Repair your computer'.
---

### Q35: What is the difference between absolute and relative file paths in Linux?
**Answer:** An absolute path starts from the root directory `/` (e.g., `/var/log/syslog`). A relative path is relative to the current working directory (e.g., `./syslog`).
---

### Q36: Explain the 7 layers of the OSI model.
**Answer:** The 7 layers are: 1. Physical (cables/bits), 2. Data Link (switches/MAC), 3. Network (routers/IP), 4. Transport (TCP/UDP/ports), 5. Session (connections), 6. Presentation (encryption/SSL), 7. Application (HTTP/browser).
---

### Q37: What is the difference between TCP and UDP?
**Answer:** TCP is connection-oriented, reliable, and guarantees packet delivery using error checking and a 3-way handshake. UDP is connectionless, fast, and does not check for packet delivery, used for streaming and VoIP.
---

### Q38: What is a MAC address?
**Answer:** A MAC (Media Access Control) address is a permanent, 48-bit physical hardware address embedded in a network interface card (NIC) by the manufacturer.
---

### Q39: Difference between private and public IP addresses?
**Answer:** Private IP addresses are used within a local network (not routable on the public internet). Public IP addresses are globally unique and routable over the internet, assigned by ISPs.
---

### Q40: What are the RFC 1918 private IP address ranges?
**Answer:** Class A: `10.0.0.0/8`, Class B: `172.16.0.0/12`, Class C: `192.168.0.0/16`.
---

### Q41: What is Subnetting and why is it used?
**Answer:** Subnetting is the practice of dividing a single physical network into smaller, logical sub-networks (subnets) to improve security, reduce traffic congestion, and manage IP allocations efficiently.
---

### Q42: Explain DNS name resolution.
**Answer:** When you enter a URL, the client queries the local DNS cache. If not found, it queries the local DNS server, which queries root servers, Top-Level Domain (TLD) servers, and finally authoritative servers to return the matching IP.
---

### Q43: What is a DNS A record and CNAME?
**Answer:** An A record maps a domain name directly to an IPv4 address. A CNAME (Canonical Name) is an alias record that maps one domain name to another domain name.
---

### Q44: What is DHCP and how does it work?
**Answer:** DHCP dynamically assigns network parameters (IP, Subnet, DNS, Gateway) to clients using the DORA DORA sequence (Discover, Offer, Request, Acknowledge).
---

### Q45: What is NAT?
**Answer:** NAT (Network Address Translation) converts private IP addresses within a local area network (LAN) into a single public IP address to allow internet access.
---

### Q46: What is the default port for HTTP and HTTPS?
**Answer:** HTTP runs on port 80; HTTPS runs on port 443.
---

### Q47: What is the default port for DNS, DHCP, and RDP?
**Answer:** DNS runs on port 53; DHCP uses ports 67 and 68; RDP runs on port 3389.
---

### Q48: What is the default port for SSH, SMTP, and LDAP?
**Answer:** SSH runs on port 22; SMTP runs on port 25; LDAP runs on port 389.
---

### Q49: What is a Default Gateway?
**Answer:** The default gateway is the IP address of the local router interface that client computers use to send traffic outside their local subnet (e.g., to the internet).
---

### Q50: How do you release and renew an IP in Windows?
**Answer:** I run `ipconfig /release` to release the current DHCP IP lease, followed by `ipconfig /renew` to request a new lease from the DHCP server.
---

### Q51: How do you clear the DNS cache on a client?
**Answer:** I run the command `ipconfig /flushdns` in the command prompt.
---

### Q52: What does the ping command do?
**Answer:** Ping sends ICMP Echo Request packets to a target IP or hostname to verify network connectivity and measure latency.
---

### Q53: What is the difference between ping and tracert?
**Answer:** Ping checks basic reachability to a target. Tracert traces the exact path (every router/hop) the packet takes to reach the destination, locating where routing fails.
---

### Q54: What does netstat do?
**Answer:** Netstat displays all active network connections, routing tables, and interface statistics on the local computer. Running `netstat -ano` shows active ports and PIDs.
---

### Q55: What is a VPN split tunnel?
**Answer:** A split tunnel allows the VPN client to route corporate traffic through the encrypted VPN tunnel, while routing standard internet traffic directly through the user's local internet connection.
---

### Q56: What is the logical boundary of Active Directory?
**Answer:** The logical boundary is the Forest, which contains one or more domain trees sharing a common schema, configuration, and global catalog.
---

### Q57: What is an OU in Active Directory?
**Answer:** An Organizational Unit (OU) is a container object within a domain used to organize users, groups, and computers. Group Policies can be linked directly to OUs.
---

### Q58: What is a Domain Controller (DC)?
**Answer:** A Domain Controller is a server running Windows Server that hosts the Active Directory database (ntds.dit) and handles authentication and authorization queries.
---

### Q59: How do you join a workstation to a domain?
**Answer:** I configure the client DNS to point to the DC, go to System Properties > Computer Name, click **Change**, select **Domain**, enter the domain name, and input AD admin credentials.
---

### Q60: How do you fix a trust relationship failure?
**Answer:** I log in as local admin, change the machine to a Workgroup, reboot, and rejoin the domain. Or run: `Test-ComputerSecureChannel -Repair -Credential (Get-Credential)`.
---

### Q61: What is the default port for secure LDAP?
**Answer:** Secure LDAPS runs on port 636.
---

### Q62: What is the difference between Security and Distribution groups?
**Answer:** Security groups assign permissions to shared resources (folders, printers). Distribution groups are strictly used for email lists and cannot be assigned permissions.
---

### Q63: What are group scopes in Active Directory?
**Answer:** Group scopes define where the group can be assigned permissions. Scopes include Domain Local, Global, and Universal.
---

### Q64: What is the utility of gpresult?
**Answer:** GPResult is a command line tool that validates which Group Policies are applied to the active user and computer.
---

### Q65: Explain the GPO processing order.
**Answer:** GPOs apply in the LSDOU sequence (Local, Site, Domain, OU). The last policy applied wins, meaning OU policies override domain policies.
---

### Q66: What is a hybrid AD environment?
**Answer:** A hybrid environment connects local Active Directory to Microsoft Entra ID (Azure AD) using AD Connect to synchronize users, groups, and passwords to the cloud.
---

### Q67: What is Microsoft Entra ID?
**Answer:** Entra ID (formerly Azure Active Directory) is Microsoft's cloud-based identity and access management service, managing cloud logins and single sign-on (SSO).
---

### Q68: What is MFA and why use it?
**Answer:** Multi-Factor Authentication requires users to provide two or more verification factors (e.g., password + authenticator code) to log in, protecting against credential theft.
---

### Q69: What is Microsoft Intune?
**Answer:** Microsoft Intune is a cloud-based Endpoint Manager service used for Mobile Device Management (MDM) and Mobile Application Management (MAM) to manage corporate devices.
---

### Q70: What is a Shared Mailbox in Exchange?
**Answer:** A shared mailbox is a mailbox that multiple users can access to read and send email. It does not require an individual license and has no direct login credentials.
---

### Q71: What is AutoDiscover in Outlook?
**Answer:** AutoDiscover is an Exchange service that allows Outlook clients to automatically locate and configure their mailbox connection settings using the user's email address and password.
---

### Q72: How do you check message tracking logs in Exchange?
**Answer:** I use the Exchange Admin Center or run `Get-MessageTrackingLog` in Exchange Management Shell to trace if an email was sent, received, or blocked.
---

### Q73: What is the global catalog in AD?
**Answer:** The Global Catalog is a Domain Controller that stores a partial replica of all objects in the Active Directory forest, allowing users to search for objects in other domains.
---

### Q74: What is the schema in Active Directory?
**Answer:** The schema defines the classes and attributes of all objects that can be created in the Active Directory forest (e.g., user account attributes like phone number, department).
---

### Q75: How do you trace why an account keeps locking in AD?
**Answer:** I check the Security Logs on the Domain Controllers for Event ID 4740 (Account Locked). The event details specify the caller computer name/IP address where the lock originated.
---

### Q76: How do you run Outlook in Safe Mode?
**Answer:** I run the command `outlook.exe /safe` in the Windows Run dialog or Command Prompt to start Outlook without any third-party add-ins.
---

### Q77: What is the difference between OST and PST?
**Answer:** OST is a local cache of the Exchange server mailbox. Safe to delete. PST is a local personal archive file used to backup email folders. If deleted, data is permanently lost.
---

### Q78: What tool repairs corrupted PST files?
**Answer:** The Inbox Repair Tool (`scanpst.exe`), located in the Office installation folder, is used to scan and repair corrupted `.pst` files.
---

### Q79: How do you clear the Teams cache?
**Answer:** For Classic Teams, I delete files inside `%appdata%\Microsoft\Teams`. For New Teams, I go to Settings > Apps > Installed Apps > Teams > Advanced Options and click **Reset**.
---

### Q80: How do you perform a silent MSI installation?
**Answer:** I open elevated CMD and run: `msiexec /i "setup.msi" /qn /norestart`. `/i` installs, `/qn` runs quiet/no UI, and `/norestart` prevents reboot.
---

### Q81: How do you clean the local Windows print queue?
**Answer:** I stop the print spooler, delete files in `%systemroot%\System32\Spool\PRINTERS\`, and restart the spooler service.
---

### Q82: What is VoIP jitter?
**Answer:** VoIP jitter is the variation in the time delay between packet deliveries over a network, causing voice packet loss, choppy audio, or dropped calls.
---

### Q83: How do you fix browser hardware acceleration crashes?
**Answer:** I go to browser settings, search for 'hardware acceleration,' toggle it to disabled, and restart the browser to force CPU-only rendering.
---

### Q84: What does the office license status tool cscript do?
**Answer:** Running `cscript ospp.vbs /dstatus` queries the active Office suite installation folder and displays the last 5 characters of registered license keys.
---

### Q85: How do you uninstall a corrupted Office activation key?
**Answer:** I run `cscript ospp.vbs /unpkey:<Key>` using the 5-character key returned from the `/dstatus` check, then reboot and sign in again.
---

### Q86: How do you troubleshoot browser proxy settings?
**Answer:** I go to Settings > Internet Options > LAN settings, check if 'Use a proxy server for your LAN' is checked, verify the proxy IP/port, or disable it to test direct connectivity.
---

### Q87: What does a 404 HTTP status code mean?
**Answer:** A 404 HTTP status code indicates the client communicated with the web server successfully, but the server could not find the requested webpage or file.
---

### Q88: What does a 503 HTTP status code mean?
**Answer:** A 503 HTTP status code indicates the web server is temporarily unable to handle the request, usually due to overload or maintenance.
---

### Q89: What is latency in network support?
**Answer:** Latency is the time delay it takes for a data packet to travel from the sender client to the receiver server and back, measured in milliseconds (ms).
---

### Q90: How do you map a network printer using static IP ports?
**Answer:** In Devices & Printers, I choose Add Printer > IP address port, enter the printer's static IP, select the manufacturer driver, and select a port (typically port 9100).
---

### Q91: What is the first step in the 7-step troubleshooting method?
**Answer:** The first step is **Identify the Problem**. I ask open-ended questions, replicate the error, and collect system details (error messages, logs).
---

### Q92: What is the last step in the 7-step troubleshooting method?
**Answer:** The final step is **Document Findings, Actions, and Outcomes** inside the ticketing logs for future reference.
---

### Q93: How do you analyze a Blue Screen of Death minidump?
**Answer:** I open the minidump file (`.dmp`) stored in `C:\Windows\Minidump` using tools like BlueScreenView or WinDbg to identify the stop code and the failing driver file.
---

### Q94: What does stop code INACCESSIBLE_BOOT_DEVICE mean?
**Answer:** This indicates Windows lost access to the system boot partition during startup, often caused by incorrect BIOS SATA mode settings (AHCI vs RAID), or a corrupted boot drive sector.
---

### Q95: What is the purpose of BitLocker Recovery keys?
**Answer:** A 48-digit BitLocker recovery key is requested if security states change (BIOS update, drive moved to a different PC, or TPM reset) to unlock the encrypted partition.
---

### Q96: How do you suspend BitLocker before a BIOS update?
**Answer:** I open CMD as Administrator and run the command: `manage-bde -protectors -disable C:` to prevent the system from requesting the key after reboot.
---

### Q97: What is the DSE daily shift handover process?
**Answer:** A shift handover involves sending a status email to the incoming shift detailing unresolved P1/P2 issues, pending hardware setups, and ongoing server/network outages.
---

### Q98: What is the identity validation protocol before password resets?
**Answer:** I ask the user for verification details (Employee ID, Manager name, department) or trigger a multi-factor authenticator callback before resetting any credentials.
---

### Q99: What is an SLA breach?
**Answer:** An SLA (Service Level Agreement) breach occurs when the IT support team fails to respond or resolve a ticket within the committed deadline agreed upon with the business.
---

### Q100: Why document troubleshooting steps inside ticket logs?
**Answer:** Clear documentation creates a historical record, allows other engineers to understand what was done if the issue escalates, and populates the Knowledge Base (KEDB) for future fixes.
---

## Quick Revision Summary
| Category | Focus Area | Essential Tools |
|---|---|---|
| 1-15 | Hardware | POST, BIOS, PSU, RAM testing |
| 16-35 | Operating Systems | Registry, GPOs, Event log tracking |
| 36-55 | Networking | OSI layers, TCP/UDP, IP addressing |
| 56-75 | Active Directory | DC structures, GPO LSDOU, Domain join |
| 76-90 | Software Support | OST/PST, O365, Print Spooler commands |
| 91-100 | Troubleshooting & SOPs | 7-step method, BSOD, Password security |

---

## Related Notes
- [[11-02 Common Interview Scenarios]] — Real diagnostic scenarios
- [[12-02 CMD & PowerShell Commands Cheat Sheet]] — CMD reference sheet