---
tags: [desktop-support, cli, powershell]
created: 2026-06-25
status: Complete
difficulty: Intermediate
estimated-read-time: 14 mins
---

# 09-02 PowerShell for Desktop Support

> [!abstract] Overview
> A deep dive into Windows PowerShell for system administration. This note covers cmdlets syntax, pipeline objects operations, query hardware via CIM/WMI, and security execution policies.

---

## 1. What Is It? (Concept Explanation)
PowerShell handles administration commands by passing .NET objects through its pipeline.

```mermaid
flowchart LR
    classDef output fill:#cce6ff,stroke:#0066cc,stroke-width:2px;
    classDef filter fill:#fff5cc,stroke:#e6b800,stroke-width:2px;
    classDef format fill:#ccffcc,stroke:#009933,stroke-width:2px;

    A[Get-Service]:::output -->|Service Objects| B[Where-Object Status eq Running]:::filter
    B -->|Filtered Objects| C[Select-Object DisplayName, Name]:::format
    C -->|Formatted Objects| D[Out-GridView]:::format
```

PowerShell is an advanced task automation framework consisting of a command-line shell and a scripting language. Unlike legacy shells that pass data as text, PowerShell is built on the .NET framework and processes data as structured **Objects**.
*Seedha simple shabdon mein bole toh: PowerShell CMD se 10x zyada powerful hai. CMD sirf text print karta hai, lekin PowerShell pure information structures (objects) ko pipeline ke through pass karta hai. Iska use hardware serials, running processes aur AD accounts ko query karne ke liye hota hai.*

---

## 2. Technical Deep-Dive: Cmdlets, Objects, & Execution Policies
To write effective commands, a technician must understand how PowerShell handles data:

### 1. Cmdlet Syntax (Verb-Noun)
PowerShell commands are called **Cmdlets**. They use a strict `Verb-Noun` naming convention:
- **`Get`:** Retrieves data (e.g., `Get-Process`).
- **`Set`:** Modifies data (e.g., `Set-Service`).
- **`New`:** Creates a resource (e.g., `New-Item`).

### 2. The Pipeline Object Model
When you pipe output in PowerShell, you are passing objects.
`Get-Service | Where-Object {$_.Status -eq "Running"}`
In this command:
- `Get-Service` outputs an array of service objects.
- The pipeline (`|`) passes these objects to `Where-Object`.
- The filter selects only objects where the `Status` property equals "Running".

### 3. Execution Policies
Windows blocks PowerShell script execution by default for security:
- **Restricted:** Default setting. No scripts can run.
- **RemoteSigned:** Locally written scripts run; scripts downloaded from the internet must be digitally signed.
- **Bypass:** Nothing is blocked. Used by administrators to run scripts:
  `powershell.exe -ExecutionPolicy Bypass -File script.ps1`

---

## 3. Real-World Support Scenario (STAR Ticket)
- **Situation:** A security auditor requests a CSV report listing all active user accounts belonging to the local Administrators group on 50 workstations, to audit privilege creep.
- **Task:** Retrieve local administrator group memberships from a workstation, format the list as a table, and export the log to a CSV file.
- **Action:**
  1. Logged onto the workstation and opened PowerShell as Administrator.
  2. Tested the query cmdlet on a single machine:
     ```powershell
     Get-LocalGroupMember -Group "Administrators"
     ```
  3. Formulated a script to retrieve the computer name, user account name, principal type, and export to CSV format:
     ```powershell
     Get-LocalGroupMember -Group "Administrators" | 
         Select-Object @{Name="ComputerName"; Expression={$env:COMPUTERNAME}}, Name, PrincipalSource, ObjectClass | 
         Export-Csv -Path "C:\temp\local_admins.csv" -NoTypeInformation
     ```
  4. Verified the output CSV in Notepad to ensure data fields populated correctly.
- **Result:** Delivered the audit-compliant CSV file within 5 minutes, showing all local administrative users.

---

## 4. Essential PowerShell Cmdlets Reference

| Task | PowerShell Cmdlet | CMD Equivalent |
|---|---|---|
| **List Running Processes** | `Get-Process` | `tasklist` |
| **Manage Background Services** | `Get-Service` / `Start-Service` | `sc query` / `net start` |
| **Query WMI/CIM Databases** | `Get-CimInstance -ClassName Win32_BIOS` | `wmic bios get` |
| **Copy Files & Directories** | `Copy-Item -Path C:\src -Destination C:\dst -Recurse` | `xcopy /e` |
| **Test Network Ports** | `Test-NetConnection -ComputerName 10.0.0.1 -Port 3389` | (None builtin) |

---

## 5. Frequently Asked Questions (FAQ)

**Q1: Why is PowerShell preferred over CMD for modern administration?**
A: PowerShell deals with objects rather than plain text. This allows technicians to filter, sort, and select specific properties easily (e.g., getting a process ID and memory usage directly) without using complex text parsing scripts like `findstr`.

**Q2: How do I bypass the PowerShell execution policy temporarily?**
A: You can launch PowerShell from CMD or Run by typing: `powershell.exe -ExecutionPolicy Bypass`. This bypasses restrictions for the active session only, without permanently modifying the OS security configuration.

**Q3: What is the difference between WMI and CIM cmdlets?**
A: WMI cmdlets (e.g., `Get-WmiObject`) are older commands that use DCOM to communicate. CIM cmdlets (e.g., `Get-CimInstance`) are modern commands that use WS-Management (WS-Man) over HTTP/HTTPS, making them faster and firewall-friendly.

**Q4: How do I view all properties of an object in PowerShell?**
A: Pipe the cmdlet output into the `Get-Member` cmdlet or use `Select-Object *`. For example: `Get-Service | Get-Member` will list all properties and methods available for service objects.

---

## PowerShell Pipeline Performance Considerations
When running commands against large databases or lists, structure is important:
- **Filter Left:** Filter data as early as possible in the command chain. Use specific cmdlet parameters instead of piping:
  - *Slow:* `Get-WinEvent -LogName System | Where-Object {$_.Id -eq 6008}`
  - *Fast:* `Get-WinEvent -FilterHashtable @{LogName='System'; Id=6008}`

## Related Notes
- [[09-01 CMD Masterclass for Desktop Support]] - Legacy CLI comparison
- [[09-03 Useful PowerShell Scripts for Support]] - Automation library
- [[09-05 Remote Management Commands]] - Running remote scripts