---
tags: [desktop-support, sop, pc-refresh]
created: 2026-06-25
status: Complete
difficulty: Intermediate
estimated-read-time: 15 mins
---

# 13-03 PC Replacement - Refresh SOP

> [!abstract] Overview
> Standard Operating Procedure (SOP) for workstation refreshes and replacements. This guide details user profile backups, OneDrive configurations, local data migrations, and safe decommissioning of older hardware assets.

---

## 1. What Is It? (Concept Explanation)
A PC refresh is the cycle of replacing older, out-of-warranty corporate workstations with newer hardware. The key challenge for support engineers is executing the transition with zero user data loss and minimal downtime.
*Seedha simple shabdon mein: Jab kisi user ka laptop 3-4 saal purana ho jata hai, toh use naya laptop diya jata hai. IT support team purane computer ka data backup karti hai (jaise local files, browser bookmarks, aur outlook signatures), naya laptop configure karti hai, aur saara data successfully naye PC par transfer karti hai taaki user ka kaam distrub na ho.*

---

## 2. PC Refresh Operations Workflow
Execute these phases during a workstation replacement:

### Phase 2.1: Pre-Migration Backup Audit
- [ ] **Verify OneDrive Status:** Have the user open their OneDrive client. Verify that **Folder Backup** is active for Desktop, Documents, and Pictures. Ensure synchronization status is complete (no pending red icons).
- [ ] **Locate Non-Synced Local Directories:** Check for data stored outside the default user folders, such as custom folders in root directories (e.g., `C:\Data` or `C:\Workspace`).
- [ ] **Export Browser Settings:** Export local browser bookmarks to HTML files if cloud sync is disabled by company policy.
- [ ] **Save Outlook Signatures:** Copy the signatures directory:
  `%appdata%\Microsoft\Signatures` to a temporary backup drive.

### Phase 2.2: Staging and Deployment
- [ ] Image the replacement computer using the standard corporate task sequence (see [[13-02 New PC Setup & Imaging SOP]]).
- [ ] Log into the new system with the user's domain account to generate their local profile folders.
- [ ] Configure OneDrive. Allow the cloud folders to sync down to the local drive.
- [ ] Restore Outlook signatures and browser bookmarks to their corresponding paths.

### Phase 2.3: Hardware Decommissioning
- [ ] **Run Secure Data Sanitization:** Wipe the old storage drive using an approved software tool (e.g., DBAN or motherboard BIOS secure erase utility) to comply with data privacy policies.
- [ ] **Update Inventory Asset Tag:** Update the status of the old computer to "Decommissioned / Recycled" in the CMDB system.
- [ ] Place the old hardware in the IT disposal bin.

---

## 3. Real-World Incident Tickets

### Ticket 1: Lost Outlook Autocomplete Cache after PC Refresh
- **Incident ID:** INC110398
- **Priority:** P3
- **Problem Statement:** "I received my new laptop, but when I type email addresses in Outlook, it doesn't auto-complete or show suggestions like my old PC did."
- **Diagnostics:**
  1. Explained that the Outlook AutoComplete cache is stored locally in a file named `Stream_Autocomplete_*.dat` in the local AppData path and is not synced by default exchange profiles.
  2. Connected to the user's old laptop. Navigated to:
     `%localappdata%\Microsoft\Outlook\RoamCache`.
  3. Located the `Stream_Autocomplete` file with the largest file size.
- **Resolution:** Copied the old `Stream_Autocomplete` file to the new PC's `RoamCache` folder. Renamed it to match the exact filename of the newly generated autocomplete file on the new system, overwriting it. Restarted Outlook. The autocomplete address suggestions restored successfully.

### Ticket 2: Missing QuickBooks Local Database Files
- **Incident ID:** INC110412
- **Priority:** P2 (Department Access Down)
- **Problem Statement:** "Accounting staff migrated to a new desktop, but QuickBooks cannot open the company database. We receive error: 'Company file not found'."
- **Diagnostics:**
  1. Verified OneDrive had synced the default user documents folder.
  2. Inspected the old desktop drive. Found the database file was stored in a non-standard local path: `C:\QuickBooks\Data\Company.qbw`.
  3. This folder was not in the user profile directory and was excluded from OneDrive backups.
- **Resolution:** Recovered the old hard drive, copied the database file `Company.qbw` from `C:\QuickBooks\Data` to the new PC's corresponding path, configured the QuickBooks client to point to the folder, and verified the database opened successfully.

---

## 4. Technical File Paths Reference Table
When transferring local files, copy data from these hidden configuration directories:

| Application Profile | Local Profile Source Path | Restore Target Path |
|---|---|---|
| **Outlook Signatures** | `%appdata%\Microsoft\Signatures` | `%appdata%\Microsoft\Signatures` |
| **Outlook Templates** | `%appdata%\Microsoft\Templates` | `%appdata%\Microsoft\Templates` |
| **Chrome Bookmarks** | `%localappdata%\Google\Chrome\User Data\Default\Bookmarks` | Restore file to default directory |
| **Edge Bookmarks** | `%localappdata%\Microsoft\Edge\User Data\Default\Bookmarks` | Restore file to default directory |
| **Sticky Notes Cache**| `%localappdata%\Packages\Microsoft.MicrosoftStickyNotes_8wekyb3d8bbwe\LocalState` | Overwrite folder on new system |

---

## 5. Study & Interview Q&A
**Q1: How do you verify that a user's data is safe to migrate before wiping their old computer?**
A: I perform a dual verification. First, I check that OneDrive is active and has completed synchronization of the Desktop, Documents, and Pictures folders. Second, I perform a manual search on the old hard drive for non-user directories (e.g. folders directly under `C:\` like `C:\ERP` or `C:\Data`) to locate any localized software databases. I also check for browser exports and application caches like Outlook signatures before decommissioning the old PC.

**Q2: What is the difference between OST and PST files when migrating emails?**
A: An OST file is a local cache of the Exchange server mailbox. It does not need to be migrated because Outlook will automatically recreate it and download emails once configured on the new PC. A PST file is a local personal archive that holds emails archived off the server. PST files are not synced to the cloud and must be manually backed up and migrated to the new system, or the data will be lost.

**Q3: Why is drive sanitization (wiping) mandatory during hardware decommissioning?**
A: Wiping is a critical security compliance practice. Simply formatting a drive does not delete data; it only deletes the file allocation table, allowing files to be recovered with forensic software. Wiping overwrites the entire disk with random bits (e.g., using DBAN or DoD standards) to prevent data leaks of proprietary company information.

---

## Related Notes
- [[02-08 User Profiles & Account Management]] - Profile structures
- [[13-02 New PC Setup & Imaging SOP]] - Imaging configurations
- [[10-04 Asset Management]] - Device tracking