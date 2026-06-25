---
tags: [desktop-support, troubleshooting, boot]
created: 2026-06-25
status: Complete
difficulty: Intermediate
estimated-read-time: 14 mins
---

# 08-05 Boot Issues Troubleshooting

> [!abstract] Overview
> A diagnostic guide for resolving Windows boot failures. This note details the bootloader sequence, UEFI vs. Legacy BIOS modes, rebuilding the BCD configuration, and using Windows Recovery Environment (WinRE).

---

## 1. What Is It? (Concept Explanation)
Boot failures occur when a computer cannot successfully load the Windows operating system kernel into memory. This can be caused by physical disk disconnection, corrupted Boot Configuration Data (BCD), or bad driver files loaded during startup.
*Seedha simple shabdon mein bole toh: Boot issues tab aate hain jab PC start karne par error dikhata hai "No bootable device found" ya loading screen par loop ho jata hai. Hamein Windows Recovery Environment (WinRE) mein jana padta hai aur BCD store ya master boot record ko commands ke zariye repair karna hota hai.*

---

## 2. Technical Deep-Dive: UEFI vs. Legacy BIOS Boot Sequences
To troubleshoot boot issues, a support engineer must understand the boot sequence:

### Legacy BIOS (MBR Partitioning)
1. System power-on -> BIOS performs POST.
2. BIOS reads the Master Boot Record (MBR) on the first sector of the boot drive.
3. MBR points to the Active Partition, loading the **NTLDR** or **bootmgr** bootloader.
4. `bootmgr` reads the BCD and loads `winload.exe` to boot the kernel.

### UEFI (GPT Partitioning)
1. System power-on -> UEFI firmware performs POST.
2. UEFI reads the GUID Partition Table (GPT) and locates the **EFI System Partition (ESP)** (typically formatted as FAT32).
3. UEFI directly launches the EFI bootloader file `\EFI\Microsoft\Boot\bootmgfw.efi` stored in the ESP.
4. The EFI bootloader reads the BCD configuration and loads `winload.efi`.
- **Note:** UEFI is the modern standard, offering Secure Boot which blocks unsigned rootkit drivers from loading during boot.

---

## 3. Real-World Support Scenario (STAR Ticket)
- **Situation:** Following a sudden building power outage, a financial workstation fails to boot, displaying a black screen with error: `An operating system wasn't found. Try disconnecting any drives.`
- **Task:** Recover the boot partition, rebuild the BCD configuration, and restore the OS to working order.
- **Action:**
  1. Booted the workstation using a Windows 11 USB installation media.
  2. Clicked **Repair your computer** on the installation screen to enter the **Windows Recovery Environment (WinRE)**.
  3. Selected Troubleshoot > Advanced Options > **Command Prompt**.
  4. Checked active partitions using diskpart to verify if the disk was recognized:
     ```cmd
     diskpart
     list disk
     list volume
     exit
     ```
  5. Confirmed the main partition was healthy, but the BCD configuration was missing or corrupted.
  6. Attempted to scan for Windows installations:
     ```cmd
     bootrec /rebuildbcd
     ```
  7. The command found the Windows installation at `C:\Windows`. Pressed **Y** to add the installation to the boot configuration list.
  8. Rebuilt the Master Boot Record and boot sector just in case:
     ```cmd
     bootrec /fixmbr
     bootrec /fixboot
     ```
  9. Restarted the PC.
- **Result:** The system successfully initialized, showed the Windows logo, and booted to the login screen, resolving the issue without data loss.

---

## 4. Boot Repair Commands Reference (WinRE)

```cmd
:: Scan all disks for Windows installations and rebuild the BCD file
bootrec /rebuildbcd

:: Writes a new Master Boot Record (MBR) compatibility sector to system partition
bootrec /fixmbr

:: Writes a new boot sector to the system partition (Legacy BIOS)
bootrec /fixboot

:: Scan disks for compatible Windows installations (displays count only)
bootrec /scanos
```

### Accessing WinRE Options
- **F8 / Shift + F8:** Pressed rapidly during boot (older systems).
- **Hard Reboot Method:** Force turn off the PC three times consecutively while booting. On the fourth boot, Windows will automatically load into WinRE.

---

## 5. Frequently Asked Questions (FAQ)

**Q1: What does "No bootable device found" indicate?**
A: It indicates the BIOS/UEFI cannot find a hard drive containing an active boot sector or EFI partition. Check physical power/data cables on the drive or verify the BIOS boot priority list has the drive set as #1.

**Q2: How do I resolve "bootrec /fixboot" returning "Access is Denied" on a UEFI GPT system?**
A: On UEFI GPT systems, you must manually mount the hidden FAT32 EFI System Partition using diskpart, assign it a drive letter (e.g., `Z:`), format it, and rebuild the boot loader files using the `bcdboot` command:
`bcdboot C:\Windows /s Z: /f ALL`.

**Q3: What is Secure Boot?**
A: Secure Boot is a UEFI security standard that verifies the digital signature of the operating system bootloader and drivers. It prevents rootkits and unauthorized software from taking control of the PC during the boot phase.

**Q4: How do I boot into Safe Mode from Windows Recovery Environment?**
A: In WinRE, select Troubleshoot > Advanced Options > Startup Settings > click **Restart**. Once the PC reboots, press `4` or `F4` to boot into Safe Mode, which loads only essential drivers.

---

## Related Notes
- [[08-02 BSOD (Blue Screen of Death) Analysis]] - Crash logs parsing
- [[02-10 System Recovery Tools]] - WinRE tools menu
- [[12-05 BSOD Stop Codes Reference]] - Stop codes reference