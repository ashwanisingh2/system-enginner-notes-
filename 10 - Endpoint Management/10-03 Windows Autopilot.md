---
tags: [desktop-support, endpoint, autopilot]
created: 2026-06-25
status: Complete
difficulty: Intermediate
estimated-read-time: 14 mins
---

# 10-03 Windows Autopilot

> [!abstract] Overview
> A provisioning manual for Windows Autopilot deployment. This note covers hardware hash collection, registering client devices, profile allocations, and debugging Enrollment Status Page (ESP) phases.

---

## 1. What Is It? (Concept Explanation)
Windows Autopilot is a collection of cloud-based technologies used to set up and pre-configure new corporate devices. Instead of re-imaging laptops manually in an IT build room, technicians ship factory-new PCs directly to users, who connect to the internet, log in with their corporate credentials, and let the cloud configure the system automatically.
*Seedha simple shabdon mein bole toh: Autopilot ek cloud zero-touch provisioning tool hai. Hamein hardware image download karne aur manually install karne ki zaroorat nahi hoti. Bas new laptop open karo, client credentials dalo, aur profile automatic setup kar degi.*

---

## 2. Technical Deep-Dive: Autopilot Workflow & Hardware Hashes
To deploy a computer using Autopilot, the hardware identity must be registered in the corporate Intune tenant:

### 1. The Hardware Hash (Hardware ID)
The hardware hash is a unique cryptographic string generated from a computer's motherboard serial number, BIOS details, and network MAC addresses.
- **Collection:** If an OEM vendor did not register the device, a technician must boot the PC into the Windows Out-of-Box Experience (OOBE) screen, press `Shift + F10` to open Command Prompt, run a PowerShell script to extract the hash, and upload it to the Intune admin portal.
- **Script Command:**
  ```powershell
  Set-ExecutionPolicy Bypass -Scope Process -Force
  Install-Script -Name Get-WindowsAutopilotInfo -Force
  Get-WindowsAutopilotInfo.ps1 -OutputFile C:\temp\hw_hash.csv
  ```

### 2. Enrollment Status Page (ESP) Stages
When a user logs on, the Enrollment Status Page (ESP) tracks three distinct configuration phases:
1. **Device Preparation:** Verifies TPM keys, registers MDM profiles, and validates policies.
2. **Device Setup:** Installs security configurations, network profiles, and core applications (like security software and corporate browsers).
3. **Account Setup:** Configures user-specific profiles, maps OneDrive folders, and signs into corporate portals.

---

## 3. Real-World Support Scenario (STAR Ticket)
- **Situation:** A new hire in another city receives their company laptop but is stuck on the Enrollment Status Page (ESP) during the Device Setup phase. The screen displays error: `Installation failed. Contact your IT administrator.`
- **Task:** Troubleshoot the Autopilot installation failure remotely, identify the blocked app, and bypass the page lock to restore functionality.
- **Action:**
  1. Had the user press **`Ctrl + Shift + D`** on the error screen. This shortcut opens the hidden **Autopilot Diagnostics** tool.
  2. Guided the user to read the diagnostics report displayed on screen.
  3. Identified that the Device Setup phase was blocked because a required application (M365 Apps) failed to download due to a temporary network timeout on the user's home Wi-Fi.
  4. Had the user reboot the machine, connect their laptop directly to their home router using an Ethernet cable, and restart the setup process.
  5. If the app failed again, accessed the Intune portal online, temporarily removed the blocked app from the "Block device use until required apps are installed" list in the Autopilot ESP profile settings, and triggered a policy refresh.
- **Result:** The system bypassed the hung setup phase, completed device registration, and booted to the user's desktop, where office apps downloaded in the background.

---

## 4. Autopilot Provisioning Diagnostic Keys

| Key Action / Shortcut | Destination Path | Troubleshooting Focus |
|---|---|---|
| **Ctrl + Shift + D** | ESP Setup Screen | Launching built-in diagnostics menu for app deployment failures. |
| **Shift + F10** | OOBE Setup Screen | Opening administrative Command Prompt for manual command runs. |
| **Autopilot Registry**| `HKLM\SOFTWARE\Microsoft\Provisioning\` | Verifying download URL states and profile registration dates. |
| **Autopilot Event Log**| `Applications and Services Logs > Microsoft > Windows > ModernDeployment-Diagnostics-Provider` | Auditing detailed boot errors and security policy rejections. |

---

## 5. Frequently Asked Questions (FAQ)

**Q1: What is the Out-of-Box Experience (OOBE) screen?**
A: OOBE is the initial setup screen that appears when you power on a factory-new computer for the first time. It asks you to select your region, keyboard layout, and connect to a network.

**Q2: How do I capture a hardware hash from a device in OOBE mode?**
A: At the OOBE region selection screen, press `Shift + F10` to open Command Prompt. Type `powershell`, press Enter, and run the `Get-WindowsAutopilotInfo` script, specifying the output file parameter.

**Q3: Can I run Autopilot on a virtual machine (VM) for testing?**
A: Yes, you can generate a hardware hash from a VM running Hyper-V or VMware, upload it to the Intune portal, and test the provisioning process exactly like a physical laptop.

**Q4: What is Autopilot White Glove (Pre-Provisioning)?**
A: It is an Autopilot mode where a technician runs the initial device setup and application download phases in the IT build room before shipping the device to the end user. This minimizes the setup time required by the user when they first power on the machine.

---

## Related Notes
- [[10-02 Microsoft Intune Basics]] - Cloud MDM configurations
- [[13-02 New PC Setup & Imaging SOP]] - Operating systems deployments
- [[07-06 Password Security & MFA]] - MFA enrollment setups