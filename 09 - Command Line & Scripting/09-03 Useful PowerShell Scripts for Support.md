---
tags: [desktop-support, cli, automation]
created: 2026-06-25
status: Complete
difficulty: Intermediate
estimated-read-time: 15 mins
---

# 09-03 Useful PowerShell Scripts for Support

> [!abstract] Overview
> A library of production-ready PowerShell scripts for common desktop support operations. This note details scripts for directory cleanups, service recovery, asset inventory audits, and system diagnostic routines.

---

## 1. What Is It? (Concept Explanation)
Automating routine tasks using PowerShell reduces administrative overhead and minimizes manual entry errors. This library contains scripts designed to run locally or remotely to speed up troubleshooting and maintenance.
*Seedha simple shabdon mein bole toh: Ek desktop support engineer ko bar-bar wahi tasks karne padte hain - jaise cache clear karna, services restart karna, ya diagnostics check karna. In scripts ko run karke aap hours ka manual kaam seconds mein kar sakte hain.*

---

## 2. Technical Deep-Dive: Script Structure & Error Handling
Enterprise administration scripts should include safety controls:

- **Param Blocks:** Allow technicians to pass arguments (e.g. computer names or user profiles) when calling the script.
- **Try-Catch Blocks:** Enforces structured error handling. If a command fails (e.g., Access Denied when deleting a system file), the script catches the exception, logs it, and continues running instead of crashing.
- **Logging:** Writing script execution events to a file (`C:\temp\script_log.txt`) or the Windows Event Log for audit tracking.

---

## 3. Production Scripts Library

### Script 1: Automated Temp & Cache Cleaner
This script terminates browser processes and purges user temp files and system log folders:
```powershell
# Stop browsers
$ProcessList = @("chrome", "msedge", "teams")
foreach ($Proc in $ProcessList) {
    Stop-Process -Name $Proc -Force -ErrorAction SilentlyContinue
}

# Purge Temp Directories
$TempPaths = @(
    "$env:TEMP\*",
    "C:\Windows\Temp\*"
)

foreach ($Path in $TempPaths) {
    try {
        Remove-Item -Path $Path -Recurse -Force -ErrorAction Stop
        Write-Host "Cleared path: $Path" -ForegroundColor Green
    } catch {
        Write-Warning "Failed to clear $Path: $_"
    }
}
```

### Script 2: Local Hardware Audit & BIOS Info
This script queries motherboard details and serial numbers, and exports them to a formatted file:
```powershell
$Serial = (Get-CimInstance -ClassName Win32_Bios).SerialNumber
$Model  = (Get-CimInstance -ClassName Win32_ComputerSystem).Model
$RAM    = [Math]::Round(((Get-CimInstance -ClassName Win32_ComputerSystem).TotalPhysicalMemory / 1GB), 0)
$CPU    = (Get-CimInstance -ClassName Win32_Processor).Name

$AuditData = [PSCustomObject]@{
    ComputerName = $env:COMPUTERNAME
    Serial       = $Serial
    Model        = $Model
    RAM_GB       = $RAM
    CPU          = $CPU
}

$AuditData | Format-List | Out-File -FilePath "C:\temp\audit_report.txt"
Write-Host "Hardware audit saved to C:\temp\audit_report.txt" -ForegroundColor Green
```

---

## 4. Real-World Support Scenario (STAR Ticket)
- **Situation:** Users on a development floor report that their local C: drives are running out of space, which causes compilers to crash. Helpdesk technicians are spending 30 minutes per machine manually locating and deleting cache folders.
- **Task:** Automate disk space cleanup and generate a report showing disk space usage.
- **Action:**
  1. Formulated a PowerShell cleanup script with integrated logging.
  2. Included logic to query free disk space before and after the cleanup.
  3. Added code to wipe the Windows Update cache directory (`C:\Windows\SoftwareDistribution\Download`) which often accumulates GBs of old updates:
     ```powershell
     Stop-Service -Name wuauserv -Force
     Remove-Item -Path "C:\Windows\SoftwareDistribution\Download\*" -Recurse -Force -ErrorAction SilentlyContinue
     Start-Service -Name wuauserv
     ```
  4. Deployed the script to target machines using remote execution.
- **Result:** Automated the cleanup process, freeing up an average of 15GB of disk space per workstation and reducing technician handling time from 30 minutes to 30 seconds.

---

## 5. Frequently Asked Questions (FAQ)

**Q1: How do I schedule a PowerShell script to run daily?**
A: Use Windows Task Scheduler. Create a task, set the action to "Start a program", set the program path to `powershell.exe`, and set the arguments to: `-ExecutionPolicy Bypass -File C:\Scripts\cleanup.ps1`.

**Q2: What is the purpose of the "-ErrorAction SilentlyContinue" switch?**
A: This parameter tells PowerShell to ignore any non-terminating errors during execution (e.g. attempting to delete a file that is currently in use) and proceed with the remaining lines of the script.

**Q3: How do I export my script output to a CSV file?**
A: Collect the script output objects into a variable and pipe it to `Export-Csv`. For example: `$data | Export-Csv -Path "C:\temp\report.csv" -NoTypeInformation`.

**Q4: How do I run a local script against a remote computer?**
A: Use the `Invoke-Command` cmdlet. For example, run: `Invoke-Command -ComputerName "RemotePC" -FilePath "C:\Scripts\diagnostic.ps1"`. This sends and executes the script on the remote machine.

---

## Automating Script Execution via GPO Startup Scripts
Support scripts can be deployed at system startup:

### Step-by-Step GPO Deployment
1. Save the PowerShell script in the AD SYSVOL share:
   `\\company.local\sysvol\company.local\scripts\Startup\`
2. Create a GPO, navigate to `Computer Configuration > Policies > Windows Settings > Scripts (Startup/Shutdown)`.
3. Select the **PowerShell Scripts** tab, add the script file path, and set execution parameters.
4. **Structured Logging:** Add robust logging to your scripts by writing output directly to the Windows System Event Log:
   `Write-EventLog -LogName Application -Source "IT_Automation" -EventId 1001 -EntryType Information -Message "Cleanup script completed successfully."`

## Related Notes
- [[09-02 PowerShell for Desktop Support]] - Cmdlet syntax and logic
- [[09-05 Remote Management Commands]] - Remoting setup guides
- [[08-03 Slow PC Diagnosis & Optimization]] - Performance diagnostics