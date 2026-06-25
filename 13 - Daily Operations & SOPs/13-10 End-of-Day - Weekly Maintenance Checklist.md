---
tags: [desktop-support, sop, maintenance]
created: 2026-06-25
status: Complete
difficulty: Beginner
estimated-read-time: 15 mins
---

# 13-10 End-of-Day - Weekly Maintenance Checklist

> [!abstract] Overview
> Standard checklists for daily and weekly IT maintenance. Covers hardware staging lab cleanups, deployment server tool updates, backup checks, and ticket audit routines.

---

## 1. What Is It? (Concept Explanation)
End-of-Day and weekly maintenance checklists are routine procedures executed by support engineers to keep IT workspaces, staging servers, and asset inventory organized, updated, and ready for deployment cycles.
*Seedha simple shabdon mein: Daily aur Weekly maintenance checklists IT support team ke house-keeping rules hain. Isme staging benches ko saaf rakhna, network switches ke wires check karna, update deployment systems (jaise MDT aur SCCM caches) clear karna, aur resolved tickets ka double-check audit karna shaamil hai, taaki work infrastructure clean aur safe rahe.*

---

## 2. Maintenance Workflows Checklists

### Daily End-of-Day Checklist
- [ ] **Clean Staging Lab Benches:** Shut down all tested laptops. Disconnect diagnostic cables and return spudgers/screwdrivers to the tool drawer.
- [ ] **Verify Resolved Tickets Status:** Check tickets marked "Pending User Verification." If the user has confirmed resolution, close the ticket.
- [ ] **Check Backup Logs:** Open the local server backup dashboard. Verify that last night's client-configuration backups completed successfully.
- [ ] **Reset Test Devices:** Wipe any virtual machines or physical test laptops used for malware analysis during the shift.

### Weekly Maintenance Checklist
- [ ] **Audit Build Room Inventory:** Count physical stock of SSDs, RAM modules, cables, docking stations, and monitors. Update the inventory register.
- [ ] **Run Deployment Server Updates:** Check the SCCM or MDT server. Update the local driver library packages for active laptop models.
- [ ] **Purge Staging Disk Space:** Delete older Windows image `.wim` files and cached software packages from staging server drives.
- [ ] **Audit Compliance Alerts:** Check Intune monitoring dashboards for client machines flagged as "Non-Compliant" due to missing security updates.

---

## 3. Real-World Incident Tickets

### Ticket 1: Staging Server Disk Crash due to Log File Bloat
- **Incident ID:** INC110755
- **Priority:** P2 (Imaging Down)
- **Problem Statement:** "When trying to image a desktop from network boot, we receive error: 'Task sequence failed because disk allocation failed'."
- **Diagnostics:**
  1. Checked the staging server. Found the primary system drive (C:\) was at 100% capacity (0 bytes free).
  2. Discovered that the weekly logs cleanup task script had failed to execute, allowing IIS network boot logs to grow to 120GB, locking the disk.
- **Resolution:** Purged the IIS logs folder, restarted the deployment services, verified 50GB of free space was restored, and rescheduled the weekly maintenance script.

### Ticket 2: ESD Damage to RAM Sticks due to Poor Staging Organization
- **Incident ID:** INC110772
- **Priority:** P3
- **Problem Statement:** "A newly upgraded desktop PC fails to POST, DRAM LED is solid Amber, despite installing new RAM sticks yesterday."
- **Diagnostics:**
  1. Inspected the staging room bench. Found new RAM sticks lying loose on wooden benches alongside metal tools and cords.
  2. The technician did not use ESD packaging or static wrist straps during upgrades, resulting in static discharge damaging the RAM controller.
- **Resolution:** Replaced the damaged RAM stick with a new module from anti-static packaging, used an ESD wrist strap during installation, and verified the system POSTed successfully.

---

## 4. Technical File Maintenance Script (PowerShell)
Use this script weekly to clean local temp files and log caches on staging PCs:
```powershell
# Weekly cleanup script for temporary files
$Paths = @("C:\Windows\Temp\*", "C:\Users\*\AppData\Local\Temp\*")
foreach ($Path in $Paths) {{
    Remove-Item -Path $Path -Force -Recurse -ErrorAction SilentlyContinue
}}
Write-Output "Temporary files purged successfully."
```

---

## 5. Study & Interview Q&A
**Q1: Why is inventory count verification critical in a weekly support checklist?**
A: Inventory verification prevents project delays. If a company onboard new employees but the IT room runs out of stock for laptop chargers, docks, or network cables, the deployments will stall. Keeping stock counts updated helps purchasing managers order items before they run out.

**Q2: What is the role of ESD safety protocols in staging lab maintenance?**
A: Electrostatic Discharge (ESD) can destroy internal motherboard and RAM circuits silently. Proper lab maintenance requires keeping static-generating items (plastic, paper, jackets) off the benches, using conductive anti-static mats, and wearing grounded ESD wrist straps when handling physical chips.

**Q3: How do you handle a server compliance alert showing 20 laptops are non-compliant in Intune?**
A: I audit the non-compliance reports in the Microsoft Intune console to find the cause (e.g., missing security updates or disabled antivirus). I use remote tools to connect to the machines, trigger a manual synchronization client check, and apply the updates.

---

## Related Notes
- [[13-09 Shift Handover Checklist]] - Daily transition logs
- [[10-04 Asset Management]] - Asset tracking databases
- [[08-03 Slow PC Diagnosis & Optimization]] - System cleanup guides