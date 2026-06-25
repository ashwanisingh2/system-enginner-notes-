---
tags: [desktop-support, cheatsheet, bsod]
created: 2026-06-25
status: Complete
difficulty: Intermediate
estimated-read-time: 15 mins
---

# 12-05 BSOD Stop Codes Reference

> [!abstract] Overview
> An advanced diagnostic reference guide for Windows Blue Screen of Death (BSOD) stop codes. This guide defines hex stop codes, identifies system driver culprits, and details minidump debug steps.

---

## 1. What Is It? (Concept Explanation)
A Blue Screen of Death (BSOD), technically called a Bugcheck or Stop Error, occurs when the Windows kernel encounters a critical error that it cannot recover from. To prevent hardware damage and system corruption, the operating system halts execution, flushes active RAM memory state data to a minidump file (`.dmp`), and displays a stop screen containing a specific hex code.
*Seedha simple shabdon mein: BSOD computer ka safety switch hai. Jab Windows Kernel koi aisi situation face karta hai jo use damage kar sakti hai (jaise corrupt driver, failing RAM, ya hardware mismatch), toh system crash ho jata hai aur humare liye ek diagnostic code (jaise INACCESSIBLE_BOOT_DEVICE) generate karta hai. Debugging ke liye hum dump files aur stop codes analyze karte hain.*

---

## 2. Critical BSOD Stop Codes Diagnostic Key
Use this reference table to isolate system crashes:

| Bugcheck Name | Hex Stop Code | Probable Root Cause | Troubleshooting Focus Area |
|---|---|---|---|
| **INACCESSIBLE_BOOT_DEVICE** | `0x0000007B` | Storage controller driver mismatch, boot files corruption, or SATA mode configuration error. | Check BIOS SATA/NVMe settings (AHCI vs. RAID). Run `bootrec` commands. |
| **PAGE_FAULT_IN_NONPAGED_AREA** | `0x00000050` | Corrupted physical RAM module, faulty system driver, or pagefile corruption. | Run MemTest86 diagnostic. Reseat memory modules. |
| **DRIVER_IRQL_NOT_LESS_OR_EQUAL** | `0x0000000A` | A hardware driver (video, network, audio) attempted to write to protected memory. | Identify the faulting `.sys` file in dump logs. Update driver. |
| **SYSTEM_SERVICE_EXCEPTION** | `0x0000003B` | Operating system file system corruption or graphics card driver conflict. | Run `sfc /scannow` and `dism`. Reinstall graphics drivers. |
| **UNEXPECTED_STORE_EXCEPTION** | `0x00000154` | Hardware failure of the system SSD or corruption in the cache store. | Run drive health audits. Check S.M.A.R.T. status. Replace SSD. |
| **KERNEL_SECURITY_CHECK_FAILURE** | `0x00000139` | Corrupt Windows registry hives, memory corruption, or bad system files. | Roll back recently installed updates. Run memory tests. |
| **CRITICAL_PROCESS_DIED** | `0x000000EF` | A critical operating system process (like `csrss.exe` or `svchost.exe`) terminated. | Check Event Viewer application logs. Scan for malware. |

---

## 3. SOP: Troubleshooting BSOD from Windows Recovery Environment
When a client machine crashes repeatedly during boot:
1. Boot the PC to the Windows Recovery Environment (WinRE) by interrupting startup three times.
2. Select **Troubleshoot** -> **Advanced Options** -> **Command Prompt**.
3. **Check Storage Mode:** Verify if the BIOS SATA mode matches the image installation settings. If the motherboard has reset to default settings, it may change from RAID to AHCI, triggering `0x7B` boot loops.
4. **Repair Boot Files:**
   ```cmd
   bootrec /fixmbr
   bootrec /fixboot
   bootrec /rebuildbcd
   ```
5. **Uninstall Buggy Update:**
   ```cmd
   wmic qfe list brief
   wusa /uninstall /kb:XXXXXXX
   ```

---

## 4. SOP: Analyzing Minidump Files using BlueScreenView
To isolate a driver crash:
1. Copy the latest dump file from `C:\Windows\Minidump` to a technician workstation.
2. Open **BlueScreenView** (or WinDbg).
3. Import the `.dmp` file.
4. Locate the highlighted red rows. These show the active driver modules involved in the crash (e.g., `rtwlanu.sys` for Realtek Wireless or `nvlddmkm.sys` for NVIDIA Graphics).
5. Boot the user's PC into Safe Mode, open Device Manager, right-click the target device, and uninstall or roll back the driver.

---

## 5. Study & Interview Q&A
**Q1: How do you troubleshoot the stop code `INACCESSIBLE_BOOT_DEVICE`?**
A: This error means the Windows kernel has lost access to the system boot partition during startup. I start by checking the BIOS storage controller settings to ensure it has not changed between AHCI, RAID, or IDE. If settings are correct, I boot to WinRE, open CMD, check the disk integrity using `chkdsk`, and rebuild the boot loader configurations using `bootrec /rebuildbcd`.

**Q2: What is the significance of a `.dmp` file in Windows crash analysis?**
A: When a bugcheck occurs, the Windows kernel writes active RAM memory contents, running threads, loaded driver files, and CPU register states to a memory dump file (either a full dump or a minidump in `C:\Windows\Minidump`). Opening this dump using WinDbg or BlueScreenView allows support engineers to read the stack trace and identify the exact device driver file causing the system to crash.

**Q3: A user reports their PC crashes with error `0x50` (PAGE_FAULT_IN_NONPAGED_AREA). What is your next step?**
A: Stop code `0x50` indicates the OS requested data from a memory address that does not exist in physical memory. This is usually caused by failing RAM modules. I would run Windows Memory Diagnostic or create a bootable USB with MemTest86 to scan the physical RAM modules for sector errors. If errors are found, I reseat or replace the failing RAM stick.

---

## Related Notes
- [[08-02 BSOD (Blue Screen of Death) Analysis]] - Minidump analysis procedures
- [[02-10 System Recovery Tools]] - Recovery environment setups
- [[12-06 Event Viewer IDs Reference]] - System logs key