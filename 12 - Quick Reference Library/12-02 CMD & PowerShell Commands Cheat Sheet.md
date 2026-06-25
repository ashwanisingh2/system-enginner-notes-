---
tags: [desktop-support, cheatsheet, command-line]
created: 2026-06-25
status: Complete
difficulty: Beginner
estimated-read-time: 15 mins
---

# 12-02 CMD & PowerShell Commands Cheat Sheet

> [!abstract] Overview
> A comprehensive diagnostic reference sheet mapping essential Command Prompt (CMD) commands to their modern PowerShell (PS) equivalents. Covers network audits, system repair, configuration management, and file system troubleshooting.

---

## 1. What Is It? (Concept Explanation)
Command-line interfaces allow support engineers to communicate directly with the operating system kernel. Using standard commands enables automated scripting, remote system administration, and rapid gathering of system metadata without navigating multiple GUI configuration menus.
*Seedha simple shabdon mein: Command Prompt aur PowerShell IT support ke brahmastra hain. GUI se settings change karne mein time lagta hai aur remote support ke waqt mouse double-click karna laggy ho sakta hai. Command line ka use karke aap single line input se complex issues (jaise network settings flush karna, corrupt files system repair karna, aur group policies update karna) seconds mein resolve kar sakte hain.*

---

## 2. Command Mappings: Network Diagnostics & Configurations
Diagnose network adapter configurations, routing status, and DNS resolution issues:

| Diagnostic Purpose | Command Prompt (CMD) | PowerShell (PS) Cmdlet | Expected Output / Use Case |
|---|---|---|---|
| **Query IP Address Details** | `ipconfig /all` | `Get-NetIPAddress` | Displays MAC address, active IPv4/IPv6 leases, and local DNS servers. |
| **Renew DHCP IP Lease** | `ipconfig /renew` | `Update-NetIPAddress` | Releases and requests a new IP address allocation from the local DHCP pool. |
| **Clear Local DNS Cache** | `ipconfig /flushdns` | `Clear-DnsClientCache` | Purges local name resolution database to resolve redirect or domain loading errors. |
| **Test Host Connectivity** | `ping -t 8.8.8.8` | `Test-Connection -TargetName 8.8.8.8 -Continuous` | Sends continuous ICMP Echo packets to verify host reachability and latency. |
| **Trace Network Route Hops** | `tracert 8.8.8.8` | `Test-NetConnection -ComputerName 8.8.8.8 -TraceRoute` | Traces every router hop path to locate where network packets are getting dropped. |
| **Verify Port Status** | `telnet 192.168.1.10 443` | `Test-NetConnection -ComputerName 192.168.1.10 -Port 443` | Tests if a specific TCP network port is open and listening on a target server. |
| **List Listening Connections** | `netstat -ano` | `Get-NetTCPConnection` | Lists all active sockets, open ports, and the associated Process IDs (PID). |

---

## 3. Command Mappings: System Repair & GPO Management
Verify operating system file integrity, repair images, and audit applied policies:

### 1. File Integrity Scan
- **CMD / PowerShell**:
  ```cmd
  sfc /scannow
  ```
  *Description:* Scans the integrity of all protected operating system files and replaces corrupted files with healthy cached copies from the WinSxS directory.

### 2. OS Image Health Restore
- **CMD / PowerShell**:
  ```cmd
  dism /online /cleanup-image /restorehealth
  ```
  *Description:* Repairs the local Windows image database using Microsoft Update servers as the payload source. Always run this before SFC.

### 3. Check Disk for Sector Errors
- **CMD / PowerShell**:
  ```cmd
  chkdsk C: /f /r
  ```
  *Description:* Fixes file system errors `/f` and locates bad sectors `/r` on the primary storage drive. Requires a system reboot to run.

### 4. Group Policy Manual Refresh
- **CMD / PowerShell**:
  ```cmd
  gpupdate /force
  ```
  *Description:* Immediately pulls down updated user and computer Group Policy settings from the active Domain Controller.

### 5. Group Policy Diagnostics Report
- **CMD / PowerShell**:
  ```cmd
  gpresult /r
  ```
  *Description:* Generates a console summary of applied GPOs, security group memberships, and processing details for the logged-in user.

---

## 4. Command Mappings: Hardware & System Metadata Queries
Retrieve system serial numbers, memory slot details, and model specifications:

### 1. Get BIOS Serial Number (Service Tag)
- **CMD**:
  ```cmd
  wmic bios get serialnumber
  ```
- **PowerShell**:
  ```powershell
  Get-CimInstance -ClassName Win32_Bios | Select-Object SerialNumber
  ```

### 2. Query Installed RAM Modules
- **CMD**:
  ```cmd
  wmic memorychip get devicelocator, capacity, speed, manufacturer
  ```
- **PowerShell**:
  ```powershell
  Get-CimInstance -ClassName Win32_PhysicalMemory | Select-Object DeviceLocator, Capacity, Speed, Manufacturer
  ```

### 3. Check System Model specifications
- **CMD**:
  ```cmd
  wmic csproduct get name, identifyingnumber
  ```
- **PowerShell**:
  ```powershell
  Get-CimInstance -ClassName Win32_ComputerSystemProduct | Select-Object Name, IdentifyingNumber
  ```

---

## 5. Real-World Diagnostic SOP: Testing Remote Port Connectivity
When a user reports they cannot connect to the primary ERP database server (`10.100.5.25`), follow this diagnostic CLI procedure:
1. Open PowerShell as Administrator.
2. Verify basic layer 3 network connectivity:
   ```powershell
   Test-Connection -ComputerName 10.100.5.25 -Count 4
   ```
3. If ping succeeds, verify the database port (e.g., MS SQL Port `1433`) is not blocked by a local or network firewall:
   ```powershell
   Test-NetConnection -ComputerName 10.100.5.25 -Port 1433
   ```
4. Analyze the output:
   - If `TcpTestSucceeded : True`: The network path is clear. The issue lies within the local database client configuration.
   - If `TcpTestSucceeded : False`: The port is blocked. Escalate to the firewall or database team.

---

## 6. Study & Interview Q&A
**Q1: What is the difference between `ping` and `nslookup` when diagnosing network connectivity?**
A: `ping` is used to test Layer 3 connectivity to an IP address or hostname by measuring packet loss and round-trip delay. It does not verify name resolution. `nslookup` is a dedicated DNS utility that queries local DNS servers to resolve hostnames to IP addresses, verifying if the DNS server itself is functional.

**Q2: Why should you run `dism` before `sfc /scannow` on a corrupted Windows operating system?**
A: `sfc /scannow` relies on a healthy local system image cache stored in the `%systemroot%\WinSxS` folder to replace corrupted files. If the WinSxS image directory itself is corrupt, SFC will fail to repair the files. Running `dism /online /cleanup-image /restorehealth` first repairs the WinSxS cache image by pulling fresh files from Microsoft, ensuring SFC has a healthy source to perform repairs.

**Q3: How do you identify which process is consuming network port bandwidth using CLI?**
A: I run `netstat -ano` in CMD to list all active socket connections, local IP addresses, and active ports, along with the associated Process ID (PID) in the last column. I then match the PID to the running application executable using Task Manager or the CLI command `tasklist /fi "pid eq <PID>"`.

---

## Related Notes
- [[09-01 CMD Masterclass for Desktop Support]] - Advanced command prompt syntax
- [[09-02 PowerShell for Desktop Support]] - PowerShell scripting cmdlets
- [[12-03 Network Ports Reference Table]] - Core network ports listing