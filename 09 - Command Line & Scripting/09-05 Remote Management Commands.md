---
tags: [desktop-support, cli, remote]
created: 2026-06-25
status: Complete
difficulty: Intermediate
estimated-read-time: 12 mins
---

# 09-05 Remote Management Commands

> [!abstract] Overview
> An implementation guide for remote CLI tools. This note details PowerShell Remoting (WinRM), utilizing Sysinternals PsExec, and executing remote status checks and silent software deployments.

---

## 1. What Is It? (Concept Explanation)
Remote management commands allow desktop support engineers to troubleshoot and configure corporate workstations without physically visiting the desk or initiating a visual remote desktop (RDP) session that interrupts the user's workflow.
*Seedha simple shabdon mein bole toh: Remote management commands ki madad se hum users ke computer par unka work interrupt kiye bina systems changes kar sakte hain. Hum apne terminal se cmdlets run karte hain, software deploy karte hain, aur system status check kar lete hain.*

---

## 2. Technical Deep-Dive: WinRM, PsSession, and PsExec
Managing computers remotely requires secure, standardized communication frameworks:

### 1. Windows Remote Management (WinRM)
WinRM is Microsoft's implementation of the WS-Management protocol. It operates over specific ports:
- **HTTP (Unencrypted):** Port `5985`
- **HTTPS (Encrypted):** Port `5986`
- **Enable WinRM:** Run `Enable-PSRemoting -Force` on the target client.

### 2. PowerShell Remoting Cmdlets
- **`Enter-PSSession -ComputerName "RemotePC"`:** Launches an interactive command-line session. Your shell commands now run directly on the remote machine's processor.
- **`Invoke-Command -ComputerName "PC1", "PC2" -ScriptBlock { restart-service spooler }`:** Sends the command block to multiple target systems simultaneously, executes it, and returns the results.

### 3. Sysinternals PsExec
`PsExec` is a legacy CLI utility that runs commands on remote systems. It executes by copying a helper service (`psexesvc.exe`) to the remote admin share (`ADMIN$`), starts the service, and routes system input/output streams.

---

## 3. Real-World Support Scenario (STAR Ticket)
- **Situation:** A critical vulnerability is discovered in corporate accounting software, requiring an immediate silent update patch to be deployed to 35 client computers. Standard deployment tools will take 12 hours to cycle.
- **Task:** Deploy the software patch silently and immediately using remote command lines.
- **Action:**
  1. Verified the installer patch (`patch.msi`) was stored on a shared network drive accessible by all client systems: `\\fileserver\deploy\patch.msi`.
  2. Opened PowerShell as Administrator on the management console.
  3. Drafted a script block to install the package silently:
     ```powershell
     $InstallBlock = {
         Start-Process msiexec.exe -ArgumentList '/i "\\fileserver\deploy\patch.msi" /qn /norestart' -Wait
     }
     ```
  4. Executed the installation block across the array of target computers using `Invoke-Command`:
     ```powershell
     $TargetPCs = @("FIN-WS-10", "FIN-WS-11", "FIN-WS-12", "FIN-WS-13")
     Invoke-Command -ComputerName $TargetPCs -ScriptBlock $InstallBlock -ErrorAction SilentlyContinue
     ```
  5. Checked the registry path of the remote machines to verify the new software version version was registered.
- **Result:** Wrote and ran the deployment script in 10 minutes, patching all target machines immediately with zero user disruption.

---

## 4. Remote Management Connection Port Matrix

| Tool / Service | Port Number | Encryption Protocol | Target Use Case |
|---|---|---|---|
| **WinRM (HTTP)** | `5985` | Kerberos (AD Domain) | Standard PowerShell Remoting in domain. |
| **WinRM (HTTPS)** | `5986` | SSL/TLS Certificate | Remoting to non-domain systems securely. |
| **PsExec (SMB)** | `445` | NTLM authentication | Running commands on legacy legacy systems. |
| **RDP Console** | `3389` | TLS | Visual desktop control session. |

---

## 5. Frequently Asked Questions (FAQ)

**Q1: How do I resolve WinRM connection authorization errors?**
A: Ensure the client's WinRM firewall rule is active. In PowerShell, run: `Enable-PSRemoting -Force`. If communicating across workgroups (non-domain), add the target PC to the TrustedHosts list using: `Set-Item WSMan:\localhost\Client\TrustedHosts -Value "Target_IP"`.

**Q2: What is the difference between Enter-PSSession and Invoke-Command?**
A: `Enter-PSSession` is designed for an interactive, single-system troubleshooting session (similar to SSH). `Invoke-Command` is designed for running automated tasks or scripts on multiple remote computers simultaneously.

**Q3: How do I run commands as the SYSTEM account using PsExec?**
A: Use the `-s` switch. For example, run: `psexec -s -i \\remote-computer cmd.exe`. The `-i` switch ensures the command prompt can interact with the desktop.

**Q4: Is WinRM traffic secure over HTTP Port 5985?**
A: Yes. Even though WinRM HTTP uses port 5985, Windows encrypts all payload data automatically using Kerberos tokens before transmitting it over the network, making it secure in Active Directory domains.

---

## Running Non-Interactive Admin Tasks via Task Scheduler
When remote commands fail due to active session locks, technicians execute them using Task Scheduler:

### Using schtasks.exe
- **Create Task:** Create a task that runs under the local system account instantly:
  `schtasks /create /tn "RemApp" /tr "msiexec /i C:\temp\setup.msi /qn" /sc once /sd 06/25/2026 /st 12:00 /ru "SYSTEM"`
- **Run Task Immediately:**
  `schtasks /run /tn "RemApp"`
- **Delete Task:**
  `schtasks /delete /tn "RemApp" /f`

### Securing Remote WinRM Endpoints
To prevent unauthorized remote command execution on client PCs:
- Enforce the HTTPS listener (Port 5986) using local certificates.
- Set up IP address restrictions in GPOs to ensure only the administrator's network subnet can establish remote connection sessions.

## Related Notes
- [[09-02 PowerShell for Desktop Support]] - Cmdlet parameters list
- [[09-03 Useful PowerShell Scripts for Support]] - Command library scripts
- [[12-03 Network Ports Reference Table]] - Connection ports catalog