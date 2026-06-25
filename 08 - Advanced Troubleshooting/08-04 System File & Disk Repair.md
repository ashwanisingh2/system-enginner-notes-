---
tags: [desktop-support, troubleshooting, repair]
created: 2026-06-25
status: Complete
difficulty: Intermediate
estimated-read-time: 12 mins
---

# 08-04 System File & Disk Repair

> [!abstract] Overview
> A guide to repairing corrupted operating system files and verifying physical disk sectors. This note covers using SFC, DISM, and CHKDSK diagnostic utilities.

---

## 1. What Is It? (Concept Explanation)
System File and Disk Repair utilities are built-in Windows tools used to fix corrupted files, repair the local component store, and check physical storage drives for write errors and bad sectors.
*Seedha simple shabdon mein bole toh: Jab OS ke system files corrupt ho jate hain (jiski wajah se updates fail hote hain ya crashes aate hain), tab hum DISM aur SFC use karte hain Windows files ko repair karne ke liye. Agar problem physical hard drive mein hai (bad sectors), toh hum CHKDSK command run karke drive errors repair karte hain.*

---

## 2. Technical Deep-Dive: SFC, DISM, and CHKDSK Operations
When diagnosing Windows system instability, run these utilities in a specific order:

### 1. DISM (Deployment Image Servicing and Management)
DISM verifies and repairs the local **Windows Component Store** (located in `%systemroot%\WinSxS`), which stores the backup copy of all system files.
- If the Component Store itself is corrupted, running SFC will fail.
- **Command:** `dism /online /cleanup-image /restorehealth`
- **Mechanism:** Compares local files against the official Windows Update cloud catalog and downloads replacements for corrupted files.

### 2. SFC (System File Checker)
Once the component store is healthy, SFC scans all protected system files.
- **Command:** `sfc /scannow`
- **Mechanism:** Compares running system files against the backup files in the WinSxS directory. If a mismatch is detected, SFC replaces the active file with the healthy backup copy.

### 3. CHKDSK (Check Disk)
CHKDSK operates at the file system and physical sector layer of the storage drive.
- `/f` : Fixes logical file system errors on the drive.
- `/r` : Locates bad sectors on the physical drive, recovers readable information, and marks those sectors as unusable so the OS stops writing to them.

---

## 3. Real-World Support Scenario (STAR Ticket)
- **Situation:** A user reports their Windows 11 PC crashes when launching Microsoft applications, showing error code `0xC0000022`. They cannot install Windows Updates, which fail with error `0x800f081f`.
- **Task:** Repair the operating system's system files and verify storage health.
- **Action:**
  1. Booted the workstation and opened Command Prompt as Administrator.
  2. First ran the **DISM ScanHealth** command to check for component store corruption:
     ```cmd
     dism /online /cleanup-image /scanhealth
     ```
  3. The scan reported component store corruption. Initiated the **RestoreHealth** command:
     ```cmd
     dism /online /cleanup-image /restorehealth
     ```
  4. Verified the command finished successfully (100% completed).
  5. Ran the **System File Checker** to repair running OS files:
     ```cmd
     sfc /scannow
     ```
  6. SFC completed with message: *"Windows Resource Protection found corrupt files and successfully repaired them."*
  7. Checked the physical drive sectors to ensure hardware stability:
     ```cmd
     chkdsk C: /f
     ```
     (Pressed **Y** to schedule the scan on next reboot, and restarted the PC).
- **Result:** The system completed the disk check, booted into Windows, and updates installed successfully without further app crashes.

---

## 4. Disk & System Repair Commands Reference

| Utility Command | Purpose | When to Use | Execution Safety |
|---|---|---|---|
| `dism /online /cleanup-image /restorehealth` | Repairs local system image using Windows Update cloud catalog. | When Windows updates fail or SFC crashes. | Safe to run online (requires internet). |
| `sfc /scannow` | Scans and replaces corrupted system files from local component store. | After DISM finishes / general app errors. | Safe to run online. |
| `chkdsk C: /f` | Scans and repairs logical file system errors on C: drive. | When files are missing or drive is marked dirty. | Requires volume lock/reboot. |
| `chkdsk C: /f /r` | Scans logical errors, detects physical bad sectors, recovers data. | HDD slow performance / active sector errors. | Long execution time / high disk wear. |

---

## 5. Frequently Asked Questions (FAQ)

**Q1: Why should I run DISM before running SFC?**
A: SFC relies on the local Windows Component Store (`WinSxS` folder) as its source directory for replacement files. If the Component Store itself is corrupted, SFC will fail to repair files. Running DISM first ensures the source directory is clean and updated.

**Q2: What is the difference between CHKDSK /f and CHKDSK /r?**
A: `CHKDSK /f` repairs logical errors in the file allocation table. `CHKDSK /r` does everything `/f` does, but also performs a sector-by-sector scan of the physical disk platters, locating bad sectors and recovering readable data (which takes significantly longer).

**Q3: Where can I view the logs generated by the SFC /scannow command?**
A: SFC logs details to the `CBS.log` file. You can extract SFC-specific entries to a separate text file by running:
`findstr /c:"[SR]" %windir%\Logs\CBS\CBS.log > "%userprofile%\Desktop\sfcdetails.txt"`.

**Q4: Can I run CHKDSK on a Solid State Drive (SSD)?**
A: Yes, you can run `chkdsk /f` to resolve logical file system errors. However, running `chkdsk /r` (which scans physical sectors) is generally not recommended for SSDs, as it causes unnecessary write cycles, wearing down the drive flash memory.

---

## Related Notes
- [[08-03 Slow PC Diagnosis & Optimization]] - Resource troubleshooting
- [[08-05 Boot Issues Troubleshooting]] - System startup repairs
- [[12-05 BSOD Stop Codes Reference]] - Stop codes index