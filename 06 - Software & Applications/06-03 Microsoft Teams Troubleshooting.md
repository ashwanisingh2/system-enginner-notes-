---
tags: [desktop-support, applications, teams]
created: 2026-06-25
status: Complete
difficulty: Beginner
estimated-read-time: 12 mins
---

# 06-03 Microsoft Teams Troubleshooting

> [!abstract] Overview
> A diagnostic manual for Microsoft Teams client issues. This note covers cache directories, clearing account credentials in Credential Manager, firewall ports, and troubleshooting audio/video device disconnects.

---

## 1. What Is It? (Concept Explanation)
Microsoft Teams is a collaboration application that interfaces with Microsoft 365 services. It utilizes local configuration folders to store session data, databases, and user settings.
*Seedha simple shabdon mein bole toh: Teams ek web-app wrapper hai jo background caches use karta hai login aur chats load karne ke liye. Agar passwords change hote hain ya cache file corrupt hoti hai, toh Teams load nahi hota ya white screen par freeze ho jata hai. Hamein local folders delete karke use clean restart karna padta hai.*

---

## 2. Technical Deep-Dive: Teams Cache and Network Protocols
The Microsoft Teams client manages local files in different directories depending on the version installed.

### Teams Client Cache Directories
- **Classic Teams Cache:** Stored in `%appdata%\Microsoft\Teams\` (subfolders include Cache, Databases, GPUCache, IndexedDB, Local Storage).
- **New Teams (Appx Package):** Runs inside a secure sandbox container. Cache files are stored in:
  `%localappdata%\Packages\MSTeams_8wekyb3d8bbwe\LocalState\`
- Clearing this folder deletes temporary databases without affecting user chats (which are stored in the M365 cloud).

### Real-Time Media Network Requirements
For audio and video calls, Teams uses **UDP ports 3478 through 3481** for media transport. If network firewalls block these UDP ports, Teams falls back to TCP Port 443. This fallback can cause poor audio quality, robotic voices, and disconnected calls because TCP enforces packet acknowledgement, introducing network latency.

---

## 3. Real-World Support Scenario (STAR Ticket)
- **Situation:** A department manager reports that their Microsoft Teams app is stuck in a sign-in loop. Every time they open the application, it shows a white screen with "Signing in...", flashes, and restarts, never loading their messages.
- **Task:** Resolve the sign-in loop, clear local application conflicts, and restore Teams services.
- **Action:**
  1. Had the user sign in to Teams Web App (`teams.microsoft.com`) to verify their M365 license and password were functional. The web app worked.
  2. Forcefully terminated all Teams processes running on the machine:
     ```cmd
     taskkill /f /im ms-teams.exe
     taskkill /f /im ms-teams-update.exe
     ```
  3. Cleared the local Teams application cache using PowerShell:
     ```powershell
     $CachePath = "$env:LOCALAPPDATA\Packages\MSTeams_8wekyb3d8bbwe\LocalState"
     if (Test-Path $CachePath) {
         Remove-Item -Path "$CachePath\*" -Recurse -Force -ErrorAction SilentlyContinue
     }
     ```
  4. Opened **Credential Manager** in Windows, searched for generic credentials starting with `Microsoft_Teams`, and removed them to clear corrupt login tokens.
  5. Restarted the Microsoft Teams client and prompted the user to log in again.
- **Result:** Teams successfully prompted for password and MFA, and loaded the user's workspace without looping.

---

## 4. Teams Port & Directory Diagnostics

| Parameter | Value / Location | Function / Troubleshooting |
|---|---|---|
| **UDP Media Ports** | `3478-3481` | Real-time audio, video, and screen sharing packets. |
| **New Teams Path** | `%localappdata%\Packages\MSTeams_8wekyb3d8bbwe` | Containment sandbox directory. |
| **Teams Log Path** | `%localappdata%\Packages\MSTeams_8wekyb3d8bbwe\LocalState\diag` | Diagnostic text files for login crashes. |
| **TCP Port** | `443` | Fallback connection port (causes lag on calls). |

---

## 5. Frequently Asked Questions (FAQ)

**Q1: How do I fix a microphone or camera that is not recognized in Teams?**
A: First, verify Windows Privacy Settings (`Settings > Privacy & Security > Microphone/Camera`) has "Allow desktop apps to access your microphone/camera" turned ON. Second, open Teams device settings and select the correct audio interface/camera from the dropdown lists.

**Q2: What is the fastest way to clear Teams cache in Windows 11?**
A: Right-click the Windows Start button, select Settings > Apps > Installed Apps, search for "Microsoft Teams", click the three dots, select **Advanced Options**, and click **Reset**.

**Q3: Why does Teams show status as "Offline" even when connected to the internet?**
A: This is usually caused by outdated login tokens stored in Windows Credential Manager or local time synchronization mismatches. Ensure Windows Time is synced automatically under Time & Language settings.

**Q4: How do we fix Teams screen sharing showing a black screen to other users?**
A: This is often caused by hardware graphics acceleration conflicts. Open Teams Settings, navigate to General, check the box for "Disable GPU hardware acceleration", and restart the application.

---

## Troubleshooting Teams Ringing & Peripheral Latency
If Teams fails to recognize USB audio hardware:
- **Sound Settings:** Open Windows Sound Control Panel (`mmsys.cpl`), select the USB headset, click Properties, and ensure the sample rate matches the hardware baseline (typically 16-bit, 48000Hz).
- **USB Hubs:** Avoid connecting headsets to unpowered USB hubs, which can cause connection drops due to power limits.

## Related Notes
- [[04-08 Microsoft Teams Administration]] - Server admin panels
- [[06-01 Microsoft Office 365 Troubleshooting]] - Licensing issues
- [[12-07 HTTP Status Codes]] - Web error codes reference