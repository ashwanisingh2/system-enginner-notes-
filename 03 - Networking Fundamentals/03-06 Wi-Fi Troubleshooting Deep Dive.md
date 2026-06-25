---
tags: [desktop-support, networking, wifi]
created: 2026-06-25
status: Complete
difficulty: Intermediate
estimated-read-time: 14 mins
---

# 03-06 Wi-Fi Troubleshooting Deep Dive

> [!abstract] Overview
> A diagnostic manual for enterprise wireless networks. This note covers Wi-Fi standards, frequency bands, network configuration checks, and troubleshooting signal dropouts and authentication issues.

---

## 1. What Is It? (Concept Explanation)
Enterprise Wi-Fi systems are more complex than home setups. They use central wireless controllers, multiple roaming access points, and enterprise security protocols (802.1X).
*Seedha simple shabdon mein bole toh: Office Wi-Fi mein multiple access points (APs) hote hain jo ek hi SSID (Name) broadcast karte hain. Jab user office mein chalta hai, toh unka laptop automatically nearest AP par shift (roam) hota hai. Wi-Fi problems drop connections, low speed, ya login error ki wajah se aati hain.*

---

## 2. Wireless Standards and Frequency Bands

| Standard Name | IEEE Standard | Frequency Bands | Max Data Rates | Common support issues |
|---|---|---|---|---|
| **Wi-Fi 5** | 802.11ac | 5 GHz | Up to 3.5 Gbps | Poor penetration through thick concrete walls. |
| **Wi-Fi 6** | 802.11ax | 2.4 GHz / 5 GHz | Up to 9.6 Gbps | Requires modern client network adapter drivers. |
| **Wi-Fi 6E / 7**| 802.11ax/be | 2.4 / 5 / 6 GHz | Up to 46 Gbps | Older client systems cannot detect the 6 GHz SSID. |

### Enterprise Security vs. Home Security
- **WPA2/WPA3 Personal (PSK):** Uses a single shared password for all devices (home standard).
- **WPA2/WPA3 Enterprise (802.1X):** Authenticates each user individually using their Active Directory credentials (username/password) or security certificates.

---

## 3. Real-World Scenarios

### Scenario 1: Laptop Constantly Dropping Wi-Fi Connection
- **Incident Description:** A project manager reports their laptop frequently disconnects from the corporate network (`Corp-WiFi`) when moving between meeting rooms.
- **Troubleshooting Steps:**
  1. Verify the client's wireless card adapter driver version in Device Manager.
  2. Open Command Prompt and monitor signal strength:
     ```cmd
     netsh wlan show interfaces
     ```
  3. Look at the **Signal** percentage and **Channel** parameters.
  4. If the signal drops below 60% in meeting rooms, the wireless controller may not be transitioning the connection to the nearest AP.
  5. Check driver properties: Right-click the wireless adapter -> **Properties** -> **Advanced**. Set **Roaming Aggressiveness** to **Medium-High** or **High**.
- **Resolution:** Updated the Intel Wi-Fi driver and set roaming aggressiveness to high. This forces the wireless card to search for and connect to a stronger AP signal sooner.

---

## 4. Essential Wi-Fi Commands
Run these commands to query wireless configurations and profiles:

```cmd
:: Show active wireless interface connection details (CMD)
netsh wlan show interfaces

:: List all saved wireless network profiles on the PC (CMD)
netsh wlan show profiles

:: Export a saved wireless profile configuration to an XML file
netsh wlan export profile name="Corp-WiFi" folder=C:\temp
```

---
## 2. Technical Deep-Dive: RF Channels & Roaming Thresholds
Enterprise Wi-Fi systems deploy physical access points managed by a central wireless controller. Signal performance is dictated by frequency bands:
- **2.4 GHz Band:** Longer range, better wall penetration, but limited speed. Consists of only 3 non-overlapping channels (1, 6, and 11) in the 20MHz spectrum, making it highly vulnerable to interference.
- **5 GHz Band:** Higher data speeds and more channels (up to 25 non-overlapping channels). However, it has shorter range and poor wall penetration.
- **Wireless Roaming:** Client devices determine when to disconnect from one AP and connect to another based on Received Signal Strength Indicator (RSSI) thresholds. Most enterprise laptops start searching for a new AP when the RSSI drops below `-70 dBm` to `-75 dBm`.
### Ticket 1: Wi-Fi Disconnect Loops in Executive Conference Room
- **Incident ID:** INC103621
- **Priority:** P2
- **Problem Statement:** "Laptops in the 3rd-floor conference room disconnect from the corporate Wi-Fi during presentations."
- **Diagnostics:**
  1. Conducted an RF scan of the room using Wi-Fi Analyzer.
  2. Found that the signal strength (RSSI) in the room was excellent (`-48 dBm`), but two adjacent APs were both broadcasting on Channel 6 in the 2.4GHz band.
  3. This caused co-channel interference (CCI), making the laptops constantly jump between the two APs (roaming loops).
- **Resolution:** Reconfigured the wireless controller to move one of the access points to Channel 11. This separated the frequencies, resolved the co-channel interference, and stabilized connections.
### Query Active Wi-Fi Connection Details (Windows CMD)
```cmd
netsh wlan show interfaces
```
**Expected Output Snippet:**
```text
    State                  : connected
    SSID                   : Corporate-Secure
    BSSID                  : 00:25:9c:12:ef:32
    Network type           : Infrastructure
    Radio type             : 802.11ax
    Channel                : 36 (5 GHz)
    Receive rate (Mbps)    : 866
    Signal                 : 88% (RSSI: -55 dBm)
```
**Q1: What is RSSI and what is a healthy range for enterprise Wi-Fi?**
A: RSSI (Received Signal Strength Indicator) is a relative index measuring the power level of a received radio signal in dBm. A healthy range for corporate laptops is between `-50 dBm` (excellent) and `-67 dBm` (minimum threshold for stable video calls). Signals lower than `-75 dBm` lead to connection drops and slow data rates.

**Q2: What is the benefit of WPA3 security over WPA2 on corporate wireless networks?**
A: WPA3 introduces Simultaneous Authentication of Equals (SAE), replacing the vulnerable pre-shared key exchange of WPA2. This protects wireless connections from offline dictionary brute-force attacks and encrypts individual user traffic even on open SSIDs.

## Related Notes
- [[03-05 Network Hardware]] - Wireless hardware components
- [[03-08 Network Diagnostic Commands]] - Network debugging CLI