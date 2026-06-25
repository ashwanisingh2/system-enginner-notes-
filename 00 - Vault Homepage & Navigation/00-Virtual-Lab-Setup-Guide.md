---
tags: [desktop-support, lab-setup, virtualization, hands-on]
created: 2026-06-25
status: Complete
difficulty: Intermediate
estimated-read-time: 12 mins
---

# 00-Virtual-Lab-Setup-Guide

> [!abstract] Overview
> Complete step-by-step guide to setting up a free, isolated Home Lab using VirtualBox, Windows Server Evaluation, and Windows 10/11 client VMs to safely practice Active Directory, GPOs, and networking commands.

---

## What Is It? (Concept Explanation)
A Virtual Lab is an isolated sandbox environment running on your physical PC using a Hypervisor.
*Seedha simple shabdon mein: Bina dusre computer kharide, ek hi PC ke andar software (VirtualBox) ka use karke fake routers, servers (Domain Controller), aur clients (Windows 10/11) setup karna taaki aap company jaisa environment design kar sakein aur real practice kar sakein bina main system ko damage kiye.*

---

## How It Works (Deep Dive)
Virtualization software (Hypervisor Type 2) runs on top of your host OS and shares CPU, RAM, and storage resources to run guest operating systems.

```
+-----------------------------------------------------+
|                     Guest VMs                       |
|  +------------------------+  +-------------------+  |
|  | Windows Server (DC)    |  | Windows 10 Client |  |
|  +------------------------+  +-------------------+  |
+-----------------------------------------------------+
|           Hypervisor (VirtualBox / Hyper-V)         |
+-----------------------------------------------------+
|                  Host OS (Windows)                  |
+-----------------------------------------------------+
|                  Physical Hardware                  |
+-----------------------------------------------------+
```

### Resource Requirements:
- **CPU:** 4 Cores minimum (Intel Core i5 / AMD Ryzen 5 or higher).
- **RAM:** 8GB minimum (16GB highly recommended; allocating 4GB to Server VM and 2GB to Client VM).
- **Storage:** 40GB+ of free SSD space.

---

## Step-by-Step Lab Setup Guide

### Step 1: Enable Hardware Virtualization (BIOS)
Before installing any hypervisor, virtualization must be enabled in your physical PC's BIOS:
1. Restart your PC and press the BIOS key (F2, Del, or F10).
2. Locate the CPU configuration settings.
3. Enable **Intel Virtualization Technology (VT-x)** or **AMD SVM Mode**.
4. Save settings and reboot into Windows.

### Step 2: Download and Install VirtualBox
1. Go to [VirtualBox Official Website](https://www.virtualbox.org).
2. Download the Windows hosts installer.
3. Install VirtualBox with default options.

### Step 3: Download Free Windows Evaluation ISOs
1. **Windows Server:** Go to the [Microsoft Evaluation Center](https://www.microsoft.com/evalcenter/). Download **Windows Server Evaluation** (select ISO download).
2. **Windows Client:** Download the Windows 10 or 11 Media Creation Tool from Microsoft and select "Create installation media (ISO file)".

### Step 4: Create the Windows Server VM (Domain Controller)
1. Open VirtualBox and click **New**.
2. Name it `WinServer-DC`, select the Windows Server ISO, and allocate **4GB RAM** and **2 vCPU Cores**.
3. Create a **50GB virtual hard disk**.
4. Set Network Adapter 1 to **Internal Network** (name it `intnet`). This isolates your lab from your physical home network.
5. Start the VM and install Windows Server (choose Desktop Experience edition).

### Step 5: Create the Windows Client VM
1. Click **New** in VirtualBox.
2. Name it `WinClient-01`, select the Windows 10/11 ISO, and allocate **2GB RAM** and **1 vCPU Core**.
3. Create a **40GB virtual hard disk**.
4. Set Network Adapter 1 to **Internal Network** (name it `intnet`). Both VMs must be on the same internal network to communicate.
5. Start the VM and perform a clean Windows installation.

---

## Important Commands / Shortcuts
Run these commands inside your VMs to test connectivity between the client and server:
```cmd
:: Ping the server IP to test Layer 3 connection
ping 192.168.10.10

:: Verify client has registered correct DNS server IP
ipconfig /all
```

---

## Common Mistakes to Avoid
> [!warning] Watch Out
> - **Setting network adapter to NAT instead of Internal:** If adapters are on NAT, they will pull IPs from your home router and won't be isolated. Always use **Internal Network** for lab setups.
> - **Allocating too much RAM:** Allocating more RAM than your physical host has will freeze your physical PC. Leave at least 4GB of RAM for your host OS.

---

## SOP (Standard Operating Procedure) for Active Directory Practice
- [ ] Install Active Directory Domain Services (AD DS) role on the Windows Server VM.
- [ ] Promote the server to a Domain Controller (create a new forest, e.g., `lab.local`).
- [ ] Configure the server's static IP to `192.168.10.10` and Subnet Mask to `255.255.255.0`.
- [ ] Set up the DHCP scope on the server to distribute IPs in the range `192.168.10.20-192.168.10.100`.
- [ ] Boot the client VM. Verify it receives an IP from the server DHCP scope.
- [ ] Join the client VM to the domain `lab.local` (requires domain admin credentials).

---

## Custom Virtual Network Configurations (NAT vs Bridged)
When setting up local virtual test labs on Hyper-V:
- **NAT Virtual Switch:** Provides VMs with internet access through the host's physical network adapter while blocking inbound traffic from the external network, protecting your lab from corporate scanning tools.
- **Internal/Isolated Switch:** Restricts communication to the virtual environment only, preventing test Active Directory services (like DHCP) from leaking onto the production office network and causing network conflicts.

## Related Notes
- [[01-05 Motherboard & BIOS]] — Virtualization CPU options
- [[03-02 IP Addressing & Subnetting]] — Static IP setup
- [[04-04 Computer Account Management]] — Domain joining steps