---
tags: [desktop-support, management, asset]
created: 2026-06-25
status: Complete
difficulty: Beginner
estimated-read-time: 11 mins
---

# 10-04 Asset Management

> [!abstract] Overview
> A operational guide to IT Asset Management (ITAM) for desktop support. This note details the hardware asset lifecycle, configuration databases (CMDB), software compliance audits, and asset tracking.

---

## 1. What Is It? (Concept Explanation)
IT Asset Management (ITAM) is the practice of tracking and managing the complete lifecycle of corporate IT assets (hardware, software licenses, networks, and cloud resources) from procurement to disposal.
*Seedha simple shabdon mein bole toh: Asset management company ke saare laptops, monitors, software aur servers ka record rakhne ki process hai. Jab koi computer buy kiya jata hai, toh use asset tag diya jata hai aur use central database (CMDB) mein register kiya jata hai, taaki inventory accurate rahe aur software audits safe hon.*

---

## 2. Technical Deep-Dive: Hardware Lifecycles & CMDB Operations
IT Asset management coordinates with helpdesk ticketing systems:

### 1. Hardware Lifecycle Phases
- **Procurement:** Ordering and purchasing the hardware asset from approved vendors.
- **Deployment:** Staging the device (imaging), assigning it to an owner, and registering details in the database.
- **Maintenance & Support:** Active phase. Software upgrades, battery replacements, and ticket maintenance history are logged.
- **Decommission / Retirement:** Wiping data drives securely, updating status logs, and sending hardware to certified electronics recycling vendors.

### 2. Configuration Management Database (CMDB)
A **CMDB** is a database that stores details about **Configuration Items (CIs)** (e.g., servers, client PCs, software packages, network switches) and describes the relationships between them (e.g., this database server runs on this host hardware).
- **ServiceNow Integration:** Modern ticketing systems automatically populate the CMDB using discovery agents that scan network subnets and report hardware specifications.

---

## 3. Real-World Support Scenario (STAR Ticket)
- **Situation:** A company-wide hardware audit reveals that 25 laptop computers are missing from the inventory tracking database, and their user assignments are outdated, violating audit policies.
- **Task:** Audit the missing assets, update their configuration items in the CMDB, and log ownership details.
- **Action:**
  1. Extracted a query list of the missing computer serial numbers from the AD active computer container.
  2. Wrote a quick PowerShell command to query the active users logged on to those laptops:
     ```powershell
     $TargetPCs = @("WS-4091", "WS-4092", "WS-4093")
     foreach ($PC in $TargetPCs) {
         Get-CimInstance -ClassName Win32_ComputerSystem -ComputerName $PC | 
             Select-Object Name, UserName
     }
     ```
  3. Located the active users, contacted them to confirm the physical location of the hardware, and noted their asset tags.
  4. Logged onto the corporate ITSM portal (ServiceNow) and navigated to the CMDB Asset page.
  5. Updated each device's configuration item entry: changed status to "In Use", mapped the active employee ID to the user field, and registered the office location.
- **Result:** Successfully updated the inventory database for all missing laptops, ensuring compliance with corporate IT auditing rules.

---

## 4. Asset Lifecycle Status Metrics

| Phase State | Description | Support Technician Responsibilities |
|---|---|---|
| **Ordered / Procured** | Purchased from hardware vendor. | Verify shipment delivery details. |
| **In Stock (Staged)** | Located in IT build room. | Run OS imaging / apply physical asset tag. |
| **In Use (Active)** | Assigned to employee. | Link user profile to asset in ticketing console. |
| **Retired (Decommissioned)** | Hardware lifecycle completed. | Securely wipe data drives / update status to "Retired". |

---

## 5. Frequently Asked Questions (FAQ)

**Q1: What is an Asset Tag and why is it used?**
A: An asset tag is a physical barcode or QR label attached to a computer chassis, showing a unique identification number. Technicians scan it to query or update the device's maintenance records in the database.

**Q2: What is the difference between a Hardware Asset and a Configuration Item (CI)?**
A: A hardware asset refers to the physical, financial value of the computer (purchase cost, depreciation). A Configuration Item (CI) is a technical representation in the CMDB, focusing on its configuration, relationships, and service delivery status.

**Q3: How do we securely wipe data before disposing of a computer?**
A: Technicians use certified data sanitization tools (e.g. DBAN, Blancco, or physical disk shredding) that write random 1s and 0s over the entire storage space multiple times, ensuring files cannot be recovered.

**Q4: What is the purpose of a Software License Audit?**
A: It is an audit to verify that the number of installed software packages on client PCs matches the number of licenses purchased, preventing financial penalties and legal liability.

---

## Software Asset Management (SAM) and License Compliance
SAM is a sub-practice of ITAM focused on optimizing software spending:
- **Compliance Monitoring:** Audit installations of expensive utilities (like Microsoft Project, Visio, or Adobe Acrobat).
- **Harvesting Licenses:** Automatically uninstall licensed software if it has not been launched by the user within 90 days, releasing the license back to the pool.

## Related Notes
- [[05-05 Ticketing Systems]] - CMDB integration rules
- [[13-08 User Offboarding SOP]] - Device collection procedures
- [[01-11 Hardware Cheat Sheet]] - Serial number queries commands