---
tags: [desktop-support, sop, printing]
created: 2026-06-25
status: Complete
difficulty: Beginner
estimated-read-time: 15 mins
---

# 13-06 Printer Setup SOP (Local & Network)

> [!abstract] Overview
> Standard Operating Procedure (SOP) for mapping local USB printers and configuring enterprise network printers. Covers printer driver installation, static TCP/IP port configuration, and print queue diagnostics.

---

## 1. What Is It? (Concept Explanation)
Printer configuration is the installation process of connecting client operating systems to physical printing devices. It requires setting communication interfaces (USB or network ports) and installing matching printer driver files to translate document layouts.
*Seedha simple shabdon mein: Printer setup do tarike se hota hai: Local (jab computer printer se direct USB cable se connected ho) aur Network (jab printer LAN cable se direct network swap switch par connected ho). Network printers configure karte waqt hum printer ko ek static IP assign karte hain taaki office ke saare PCs usse locate aur execute kar sakein.*

---

## 2. Printer Installation SOP Steps
Follow these procedures to set up client printers:

### Step 2.1: Local USB Printer Setup
- [ ] Connect the printer's physical USB cable to the PC.
- [ ] Power on the printer. Windows should auto-detect the device and search for class drivers.
- [ ] If driver installation fails, download the exact driver package (e.g., `.inf` file) from the manufacturer's official support portal.
- [ ] Go to Settings > Devices > Printers & Scanners. Verify the printer status shows "Ready."

### Step 2.2: Enterprise Network Printer Setup (TCP/IP)
- [ ] Print a network configuration page from the printer physical control panel to obtain its **Static IP Address** (e.g., `10.100.20.50`).
- [ ] On the user's PC, open **Printers & Scanners** settings and click **Add a printer or scanner**.
- [ ] Click **The printer that I want isn't listed**.
- [ ] Select **Add a printer using an IP address or hostname** and click Next.
- [ ] Set Device Type to **TCP/IP Device**.
- [ ] Input the printer IP address (`10.100.20.50`). Set Port Name to match (e.g., `IP_10.100.20.50`).
- [ ] Uncheck "Query the printer and automatically select the driver to use" to manually select the correct driver.
- [ ] Click **Have Disk** and browse to the extracted driver folder containing the `.inf` driver descriptor file.
- [ ] Complete the wizard. Name the printer according to its location (e.g., `PRN-HR-Floor3`).

### Step 2.3: Verification
- [ ] Open Printer Properties and click **Print Test Page**.
- [ ] Verify the print output is clean (no garbage characters or layout shifts).

---

## 3. Real-World Incident Tickets

### Ticket 1: Network Printer Prints Random Garbled Symbols
- **Incident ID:** INC110540
- **Priority:** P3
- **Problem Statement:** "Every time I send a document to the office copy machine, it prints hundreds of pages filled with random code, smileys, and blank sheets."
- **Diagnostics:**
  1. Recognized that the printer was receiving data it could not interpret, which is a classic symptom of an incorrect printer driver (PCL vs. PostScript mismatch).
  2. Verified the active driver properties. Found Windows had automatically mapped a generic "Microsoft IPP Class Driver" instead of the dedicated HP LaserJet driver.
- **Resolution:** Cleared the print queue on the user's PC, deleted the generic printer configuration, installed the correct HP Universal Print Driver (UPD) PCL6 package, re-mapped the printer using the PCL6 driver, and successfully printed a test page.

### Ticket 2: Multi-Function Printer Offline due to DHCP Change
- **Incident ID:** INC110555
- **Priority:** P2 (Multiple Users Affected)
- **Problem Statement:** "No one on the 2nd floor can print to the HR department printer. It shows offline on all computers."
- **Diagnostics:**
  1. Pinged the printer's configured IP address `10.10.20.150` (Request Timed Out).
  2. Checked the printer panel. Found the printer was configured for DHCP and had leased a new IP address `10.10.20.180` because the power cable was unplugged earlier, releasing its old lease.
  3. Workstations were still sending print jobs to the old IP port `10.10.20.150`.
- **Resolution:** Re-configured the printer network panel with the correct static IP address `10.10.20.150`, configured the subnet mask and default gateway, and added a DHCP reservation on the corporate server to block this IP from the dynamic pool.

---

## 4. Technical Diagnostics Command (PowerShell)
Use this command to query configured printer ports and verify their IP targets:
```powershell
# Get all TCP/IP printer ports configured on the computer
Get-PrinterPort | Where-Object {$_.PortNumber -eq 9100} | Select-Object Name, PrinterHostAddress
```

---

## 5. Study & Interview Q&A
**Q1: What is the difference between PCL and PostScript printer drivers?**
A: PCL (Printer Command Language) is a device-dependent driver standard developed by HP. It uses the local client computer resources to render the print job, making it faster for standard documents. PostScript is a device-independent language developed by Adobe. It sends raw commands to the printer, which renders the graphics using its internal processor, making it slower but ideal for high-end graphic design and complex vector images.

**Q2: How do you map a printer share hosted on a print server?**
A: I map it by opening the Run dialog (`Win + R`), typing the print server path (e.g., `\\printserver01`), and pressing Enter. This lists all shared printers. I double-click the target printer icon. Windows will download the driver from the server and map the printer to the user's profile.

**Q3: A user reports their local USB printer is not responding. How do you troubleshoot?**
A: I start by verifying the physical connections: check if the USB cable is connected, the printer is powered on, and showing no errors on its panel. I open Device Manager to see if the USB printing port is recognized. If it shows warnings, I uninstall the USB host controllers and restart the PC, or swap the USB cable to a different port.

---

## Related Notes
- [[06-06 Printer & Printing Support]] - Printer troubleshooting guides
- [[12-08 Printer Error Codes Reference]] - Error codes key
- [[02-04 Windows Services & Processes]] - Print Spooler management