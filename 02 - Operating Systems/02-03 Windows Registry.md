---
tags: [desktop-support, windows, registry]
created: 2026-06-25
status: Complete
difficulty: Intermediate
estimated-read-time: 11 mins
---

# 02-03 Windows Registry

> [!abstract] Overview
> A deep technical guide to the Windows Registry database. This note explains the registry structure, the 5 root hives, critical registry keys used in desktop troubleshooting, and step-by-step configuration and restoration procedures.

---

## Concept Explanation: The Configuration Database
The Windows Registry is a hierarchical database that stores low-level settings for the operating system and for applications that choose to use the registry.
*Seedha simple shabdon mein bole toh: Windows Registry computer ke hardware, software, user preferences aur settings ki master-book hai. Jab bhi aap koi setting change karte hain (jaise desktop background ya mouse speed), woh backend mein registry ke andar ek key ki value change karti hai. Isko direct edit karna powerful hai par galat editing system ko crash kar sakti hai.*

---

## Registry Hives and Structure
The registry is organized into keys, subkeys, and values. There are 5 main root keys (Hives) visible in the Registry Editor (`regedit.exe`):

1. **HKEY_CLASSES_ROOT (HKCR):** Contains file association settings (which app opens `.txt` or `.pdf` files) and OLE registration information.
2. **HKEY_CURRENT_USER (HKCU):** Stores configuration settings for the user currently logged in. Contains custom desktop backgrounds, app configurations, and keyboard mappings.
3. **HKEY_LOCAL_MACHINE (HKLM):** Contains hardware, driver, system software, and security configurations that apply to all users on the machine. (This is the most edited hive in troubleshooting).
4. **HKEY_USERS (HKU):** Contains all actively loaded user profiles on the system (HKCU is actually a link to the current user's profile under HKU).
5. **HKEY_CURRENT_CONFIG (HKCC):** Stores temporary hardware profile information active at startup.

---

## Critical Registry Keys for Support Engineers

| Purpose | Registry Path | Expected Action |
|---|---|---|
| **Startup Programs** | `HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run` | Run programs automatically at user logon. |
| **System Policies** | `HKLM\SOFTWARE\Policies\Microsoft\Windows` | Configure local settings normally enforced by GPO. |
| **User Profile List** | `HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\ProfileList` | Manages user profiles. Used to fix temporary profile errors. |
| **Active Winlogon Shell** | `HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon` | Sets the explorer shell (`explorer.exe`). Malware often targets this. |

---

## Support Scenario: Fixing the Temporary Profile Loop
- **Incident Description:** A user logs on to their corporate computer, but instead of their normal desktop files, they see a clean desktop and a notification: "You have been logged on with a temporary profile." Any files they save are deleted when they log off.
- **Troubleshooting Steps:**
  1. Open Registry Editor (`regedit.exe`) as Administrator.
  2. Navigate to: `HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\ProfileList`.
  3. Look for two subkeys with the user's Security Identifier (SID) (e.g., `S-1-5-21-...` and `S-1-5-21-....bak`).
  4. The key without `.bak` is pointing to the temp folder (e.g., `C:\Users\TEMP`). The key with `.bak` points to the correct profile path (e.g., `C:\Users\rohan`).
- **Resolution:**
  - Rename the key without `.bak` (add `.old` to the end).
  - Rename the key with `.bak` (remove `.bak` from the end).
  - Select the newly restored key and set the value of **RefCount** to `0` and **State** to `0`.
  - Restart the PC and have the user log in again to load their normal profile.

---

## Step-by-Step SOP: Safely Editing the Registry
1. **Always Back Up First:** Before making any change, export the key you are editing.
   - Right-click the target key -> **Export** -> Save as a `.reg` file on your backup drive.
2. **Find the Value Type:** Ensure you use the correct data type:
   - **REG_SZ:** String value (e.g., text, paths).
   - **REG_DWORD:** 32-bit integer (typically `0` for disabled, `1` for enabled).
   - **REG_BINARY:** Raw binary data.
3. **Verify via Command Line:** You can add registry keys using CMD to automate deployment:
   ```cmd
   :: Add a registry key to disable Windows lock screen (CMD)
   reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Personalization" /v NoLockScreen /t REG_DWORD /d 1 /f
   ```

---

## Registry Hives and Backing Files Location
The Windows Registry database is organized into dynamic logical folders called Hives, which are backed by physical storage files on the hard drive.

### Physical Hive Storage File Mappings
- **HKLM\SYSTEM:** Stored physically at `C:\Windows\System32\config\SYSTEM`
- **HKLM\SOFTWARE:** Stored physically at `C:\Windows\System32\config\SOFTWARE`
- **HKLM\SAM:** Stored physically at `C:\Windows\System32\config\SAM` (stores local user passwords)
- **HKCU:** Stored physically at `C:\Users\<UserName>\NTUSER.DAT` (contains user-specific settings)

### Registry Data Types and Value Formats
When modifying registry keys to troubleshoot application issues, support engineers configure specific registry data types:
- **REG_SZ:** Standard text string value, used for file paths and configuration names.
- **REG_DWORD (32-bit):** Binary integer value, commonly used for boolean settings (e.g., `0` for disabled, `1` for enabled).
- **REG_BINARY:** Raw binary data, used for hardware configuration states.
- **REG_MULTI_SZ:** Multi-string text array, used for lists of services or network interfaces.

### Safe Edit Guidelines
1. Always back up the key before making changes: right-click the target folder in `regedit`, select **Export**, and save it as a `.reg` file.
2. If the change causes system instability, double-click the exported `.reg` file to restore the previous database state.

## Related Notes
- [[02-08 User Profiles & Account Management]] - Profile troubleshooting
- [[02-04 Windows Services & Processes]] - Windows OS components
- [[12-04 Common Error Codes & Fixes]] - Registry corruption codes