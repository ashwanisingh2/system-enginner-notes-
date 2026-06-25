---
tags: [desktop-support, sop, pc-setup]
created: 2026-06-25
status: Complete
difficulty: Intermediate
estimated-read-time: 15 mins
---

# 13-02 New PC Setup & Imaging SOP

> [!abstract] Overview
> Operating system deployment staging, configuring boot parameters (UEFI, TPM), running network installations (PXE), and domain joining procedures.

---

## 1. What Is It? (Concept Explanation)
Staging and imaging is the process of applying a standardized operating system image to new or refreshed desktop and laptop hardware. This ensures that all corporate devices use the same security configurations, software baseline, and driver installations.
*Seedha simple shabdon mein: Naye laptops par direct factory OS use nahi kiya jata. IT support team company ke network switch se laptop connect karti hai, BIOS settings lock karti hai (jaise TPM aur Secure Boot enable karna), aur network ke zariye ek approved Windows image load karti hai. Isse safety policies aur software installations automatically configure ho jate hain.*

---

## 2. Step-by-Step Staging Procedure
Follow these instructions to clean install or image a workstation:

- [ ] **Configure Motherboard BIOS Settings:**
  1. Power on the machine and repeatedly press the system setup key (F2 for Dell, F10 for HP, F1 for Lenovo).
  2. Navigate to **Boot Configuration** and set the boot mode to **UEFI Only** (disable legacy CSM support).
  3. Navigate to **Security** and verify **TPM 2.0** is Enabled and Active.
  4. Ensure **Secure Boot** is Enabled to block unauthorized bootloaders.
  5. Save settings and exit.
- [ ] **Initiate Network Boot (PXE):**
  1. Connect a physical Ethernet cable to the client machine from the staging switch port.
  2. Power on the PC and press the boot menu hotkey (F12 for Dell, F9 for HP).
  3. Select **Onboard NIC (IPV4 PXE Boot)**.
  4. Once network contact is made, press **Enter** to load the WinPE environment from the SCCM server.
- [ ] **Execute Deployment Task Sequence:**
  1. In the SCCM deployment wizard, log in using staging administrator credentials.
  2. Select the corporate image profile (e.g., `Windows 11 Enterprise LTSC Baseline`).
  3. Follow the wizard steps. The task sequence will automatically format the drive (GPT), apply the OS image, inject drivers, and install baseline apps (Office, Chrome, EDR client).
- [ ] **Domain Join & Move to OU:**
  1. Name the device using the company naming convention (e.g., `LAP-FIN-821`).
  2. Join the system to the Active Directory domain.
  3. Move the computer object from the default "Computers" container to the correct departmental OU (e.g., `OU=Laptops,OU=Finance`).
- [ ] **Initialize BitLocker Encryption:**
  1. Log on to the workstation using a staging profile.
  2. Open Command Prompt as Administrator and verify BitLocker starts:
     ```cmd
     manage-bde -status
     ```
  3. Enable full disk encryption and verify the 48-digit recovery key is backed up to Active Directory or Microsoft Intune.

---

## 3. Real-World Incident Tickets

### Ticket 1: PXE Boot Fails with TFTP Timeout Error
- **Incident ID:** INC110344
- **Priority:** P3
- **Problem Statement:** "When trying to image a new desktop, PXE boot fails with error: 'PXE-E32: TFTP open timeout'."
- **Diagnostics:**
  1. Verified the Ethernet cable was connected and physical link LEDs were green.
  2. Tried to ping the SCCM Distribution Point server from another workstation on the same VLAN (successful).
  3. Checked the switch port configuration. Found the port was assigned to the default voice VLAN instead of the staging VLAN.
- **Resolution:** Reconfigured the staging switch port to the correct deployment VLAN (VLAN 50). Restarted the client PC and initiated PXE boot. The TFTP download completed successfully and loaded WinPE.

### Ticket 2: BitLocker Encryption Fails to Backup Key to AD
- **Incident ID:** INC110359
- **Priority:** P3
- **Problem Statement:** "BitLocker fails to turn on during the post-imaging task sequence, returning error code 0x80072030 (active directory object missing)."
- **Diagnostics:**
  1. Opened active directory and searched for the computer account `LAP-HR-45`. Found it resided in the default "Computers" container.
  2. Explained that domain security policies restrict the default container from saving BitLocker recovery key objects.
- **Resolution:** Moved the computer account from the "Computers" container to the dedicated `OU=Workstations,OU=HR` container. Ran `gpupdate /force` on the laptop and initiated BitLocker. The key was successfully written to Active Directory and encryption began.

---

## 4. Technical Command Reference
Use these command lines to manage and monitor BitLocker encryption states:
```cmd
:: Check current encryption status and method
manage-bde -status C:

:: Force backup the active recovery key to Active Directory Domain Services
manage-bde -protectors -adbackup C: -id {31bf3856-ad36-4e35-amd6-190411110110}
```

---

## 5. Common Mistakes & Best Practices
- **Leaving BIOS Unsecured:** Never deploy a staged laptop without setting a secure BIOS administrator password. Leaving the BIOS open allows anyone to disable Secure Boot or wipe BitLocker keys.
- **Using Wi-Fi for Staging:** Never attempt to run OS deployments over Wi-Fi networks. Staging requires high network throughput; always use gigabit Ethernet connections.

---

## 6. Pro Tips (Senior Engineer Secrets)
> [!tip] BIOS Staging Automation
> If you are staging a large batch of Dell laptops, use the **Dell Command | Configure** utility. You can export a BIOS configuration file (`.exe` or `.ini`) and execute it via a script to configure boot options, TPM, and passwords in seconds, eliminating manual BIOS adjustments.

---

## 7. Interview Q&A Bank
**Q1: What BIOS/UEFI settings must be enabled before installing Windows 11 in an enterprise environment?**
A: Windows 11 enforces strict hardware requirements. In the BIOS, UEFI boot mode must be enabled (Legacy CSM disabled), Secure Boot must be turned on to verify system loaders, and TPM 2.0 (Trusted Platform Module) must be enabled and activated to handle cryptographic encryption.

**Q2: What is PXE boot and how does it work during staging?**
A: PXE (Preboot Execution Environment) allows a computer to boot from a network interface card (NIC) before loading a local OS. The NIC sends a DHCP request with special options, receiving the IP of a boot server (like SCCM). The client then downloads a lightweight OS image (WinPE) via TFTP protocol to start the installation wizard.

**Q3: How do you verify if BitLocker drive encryption is active on a newly imaged laptop?**
A: I open Command Prompt as Administrator and run the command `manage-bde -status C:`. I verify that the **Percentage Encrypted** is `100%`, the **Protection Status** is `Protection On`, and the **Key Protectors** list includes a TPM chip configuration.

---

## Related Notes
- [[10-01 SCCM Basics for Desktop Support]] - Deployment log files
- [[13-03 PC Replacement - Refresh SOP]] - Laptop migration checklists
- [[07-05 Encryption & Data Protection]] - BitLocker management guides