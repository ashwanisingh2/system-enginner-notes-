---
tags: [desktop-support, hardware, cheatsheet]
created: 2026-06-25
status: Complete
difficulty: Beginner
estimated-read-time: 12 mins
---

# 01-11 Hardware Cheat Sheet

> [!abstract] Overview
> A quick reference guide for Desktop Support Engineers. This note contains comprehensive summaries of hardware form factors, socket configurations, diagnostic LED charts, RAM speeds, motherboard bus configurations, and key commands for day-to-day hardware support.

---

## 1. What Is It? (Concept Explanation)
This cheat sheet is a quick reference library for desktop support engineers to diagnose and verify hardware specifications on corporate clients.
*Seedha simple shabdon mein bole toh: Yeh cheat sheet ek quick guide hai. Jab user ke desk par ya build room mein kaam chal raha ho, toh details yaad rakhne ki jagah aap yahan speed ranges, socket types, diagnostic LEDs aur commands check kar sakte hain.*

---

## 2. RAM Generations & Detailed Specifications
Memory configurations require exact matches for speed, pin counts, and voltages to run in dual-channel mode:

| Generation | Desktop Pins | Laptop Pins (SO-DIMM) | Standard Voltages | Speed Ranges (MT/s) | Transfer Rate (PC Rating) |
|---|---|---|---|---|---|
| **DDR3** | 240 Pins | 204 Pins | 1.5V (Standard) / 1.35V (DDR3L) | 800 - 2133 | PC3-6400 to PC3-17000 |
| **DDR4** | 288 Pins | 260 Pins | 1.2V | 1600 - 3200 | PC4-12800 to PC4-25600 |
| **DDR5** | 288 Pins | 262 Pins | 1.1V | 4800 - 8400 | PC5-38400 to PC5-67200 |

### Dual-Channel Configuration Rules
To enable dual-channel memory architecture (which doubles the memory communication bandwidth):
1. **Matching Capacity:** Install modules of identical capacity in matching colored slots (e.g., 8GB + 8GB).
2. **Matching Speed:** Ensure memory frequencies match. If you mix 2666MHz and 3200MHz RAM, both will run at the slower speed (2666MHz).
3. **Correct Slot Placement:** Typically slots A2 and B2 (Slots 2 and 4 from the CPU socket) must be populated first. Check motherboard manual.

---

## 3. Storage Form Factors and Interface Speeds
- **3.5" HDD:** Desktop standard storage. SATA III interface (up to 6 Gbps / ~150 MB/s real-world read/write speed).
- **2.5" SSD:** Laptop standard SSD size. SATA III interface (up to 6 Gbps / ~550 MB/s read/write speed).
- **M.2 NVMe (2280):** 22mm wide, 80mm long. PCIe Gen 4 x4 interface (up to 7,000 MB/s read/write speed). Uses NVMe protocol.
- **M.2 NVMe (2230):** 22mm wide, 30mm long. Used in compact laptops (like Dell Latitude 5300 series) and tablets (Microsoft Surface Pro).

### Storage Protocol Comparison

| Interface/Protocol | Physical Form Factor | Max Theoretical Speed | Typical Real-World Read Speed |
|---|---|---|---|
| **SATA III (SATA)** | 2.5-inch / M.2 SATA | 600 MB/s (6 Gbps) | ~500 - 550 MB/s |
| **PCIe Gen 3 x4 (NVMe)**| M.2 2280 / 2230 | 4 GB/s (32 Gbps) | ~3,000 - 3,500 MB/s |
| **PCIe Gen 4 x4 (NVMe)**| M.2 2280 / 2230 | 8 GB/s (64 Gbps) | ~6,500 - 7,500 MB/s |

---

## 4. Motherboard Form Factors and Sockets
- **ATX:** Standard desktop board (12" x 9.6"). Up to 7 expansion slots. Used in standard tower towers.
- **Micro-ATX:** Compact desktop board (9.6" x 9.6"). Up to 4 expansion slots. Fits in mid-towers.
- **Mini-ITX:** Ultra-compact board (6.7" x 6.7"). 1 expansion slot. Used in small form factor (SFF) corporate PCs (e.g., Dell OptiPlex Micro, HP ProDesk DM).

### CPU Socket Pin Arrangements
- **LGA (Land Grid Array):** Sockets on the motherboard have physical gold pins; the CPU has flat gold pads. Standard for all Intel CPUs (e.g., LGA 1200, LGA 1700) and newer AMD CPUs (e.g., AM5). If a pin is bent on the motherboard socket during installation, it can cause memory channel failure or POST issues.
- **PGA (Pin Grid Array):** Sockets have holes; the CPU has physical pins on the underside. Standard for older AMD CPUs (e.g., AM4). Pins are delicate and can bend on the CPU chip itself.

---

## 5. Motherboard Diagnostic LEDs Chart
Enterprise motherboards feature 4 diagnostic LEDs to identify POST (Power-On Self-Test) failures:
1. **CPU LED (Red/Amber):** Indicates a processor initialization error, missing CPU, or incorrect BIOS version. Check CPU power cables (8-pin EPS connection) and inspect socket pins.
2. **DRAM LED (Yellow/Amber):** Indicates memory detection failure. Reseat memory modules, blow out dust, test individual sticks in slot A2, or check compatibility lists.
3. **VGA LED (White):** Indicates graphics card initialization failure. Reseat discrete GPU, check PCIe power cables, or check monitor connection/input settings.
4. **BOOT LED (Green):** Indicates the BIOS cannot locate a bootable operating system partition. Check BIOS boot order, SATA cables, or NVMe drive installation.

---

## 6. Real-World Support Scenario (STAR Ticket)
- **Situation:** A user reports that their OptiPlex SFF desktop suddenly stopped booting after they moved their desk. When they turn it on, the screen remains black, fans spin at maximum speed, and they hear three beeps, followed by two beeps.
- **Task:** Diagnose the hardware failure, resolve the issue, and ensure the system successfully boots to the Windows OS login screen.
- **Action:**
  1. Identified the beep code (3-2) in the Dell OptiPlex manual, which points to a **memory detection/DRAM failure**.
  2. Unplugged the power cable, held the power button for 15 seconds to drain residual electricity, and opened the chassis.
  3. Located the RAM modules and verified they looked loose. The PC movement had unseated the locking tabs.
  4. Removed both memory modules, blew out the slots with compressed air, and re-inserted them into slots 2 and 4, pressing firmly until both clips clicked.
  5. Connected power, turned on the PC, and monitored the POST process.
- **Result:** The diagnostic beep codes resolved, the motherboard successfully initialized, and the PC booted to the Windows OS login screen. Confirmed RAM capacity showed 16GB dual-channel in the BIOS.

---

## 7. Hardware Command Line References (CMD & PowerShell)

### Query BIOS & Serial Numbers (CMD)
```cmd
:: Get BIOS version details
wmic bios get smbiosbiosversion, version

:: Get PC Serial Number / Service Tag (Crucial for inventory validation)
wmic bios get serialnumber
```

### Query RAM Slot Allocations (CMD)
```cmd
:: Query physical memory modules installed, speed, and capacity
wmic memorychip get devicelocator, capacity, speed, manufacturer
```

### Check CPU Specifications (PowerShell)
```powershell
# Get detailed CPU name and core layout
Get-CimInstance -ClassName Win32_Processor | Select-Object Name, NumberOfCores, NumberOfLogicalProcessors, MaxClockSpeed
```

### Check Disk Drive Health Status (PowerShell)
```powershell
# Query disk health and temperature status
Get-PhysicalDisk | Select-Object DeviceId, FriendlyName, MediaType, OperationalStatus, HealthStatus
```

---

## 8. Frequently Asked Questions (FAQ)

**Q1: What is the difference between DDR3 and DDR3L memory?**
A: DDR3 runs at a standard 1.5V, while DDR3L is a low-voltage variant that runs at 1.35V. DDR3L is backward compatible with DDR3 slots, but standard DDR3 memory cannot run in slots designed exclusively for DDR3L (often found in newer 4th/5th generation Intel laptops).

**Q2: What should I do if a system turns on, fans spin, but there is no display and the CPU LED is solid red?**
A: First, ensure the CPU 8-pin power cable is plugged into the motherboard. If it is, clear the CMOS battery to reset BIOS settings. If the error persists, remove the CPU and inspect the motherboard socket for bent pins.

**Q3: How do I identify if a M.2 SSD is SATA or NVMe?**
A: Inspect the physical keys on the connector. M.2 SATA drives typically have a "B + M" key (two notches), whereas M.2 NVMe drives have an "M" key only (one notch). Additionally, check the label for "PCIe NVMe" or "SATA" branding.

**Q4: Can I run RAM sticks of different speeds together?**
A: Yes, you can run them together, but the motherboard will downclock all modules to match the speed of the slowest stick. This can degrade overall system performance and sometimes cause instability/blue screens.

---

## Related Notes
- [[01-01 Computer Architecture Overview]] - Hardware fundamentals
- [[12-02 CMD & PowerShell Commands Cheat Sheet]] - Administrative CLI scripts
- [[01-07 BIOS & UEFI Configuration]] - Settings setup manual