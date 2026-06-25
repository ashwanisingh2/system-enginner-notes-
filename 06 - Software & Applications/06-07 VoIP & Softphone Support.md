---
tags: [desktop-support, software, voip]
created: 2026-06-25
status: Complete
difficulty: Intermediate
estimated-read-time: 14 mins
---

# 06-07 VoIP & Softphone Support

> [!abstract] Overview
> A guide to Voice over IP (VoIP) and softphone support. This note covers VoIP protocols, debugging network jitter and latency, and configuring firewall rules for media streaming.

---

## 1. What Is It? (Concept Explanation)
VoIP (Voice over IP) is a technology that allows users to make voice and video calls over internet connections instead of traditional phone lines.
*Seedha simple shabdon mein bole toh: Office desks par lagne wale IP phones aur laptops par chalne wale call softwares (like Teams, Skype, or Cisco Jabber) VoIP technology use karte hain. Agar network connection slow ho ya switches mein priority configuration (QoS) na ho, toh voice break hone lagti hai ya call cut jaati hai.*

---

## 2. Essential VoIP Terms and Metrics
To diagnose poor audio quality during calls, monitor these network metrics:

- **Jitter:** The variation in the time delay between data packets arriving. High jitter (>30ms) causes choppy, broken audio.
- **Latency (Ping Time):** The time it takes for data to travel from your computer to the server. Latency higher than 150ms causes noticeable delays in conversation.
- **Packet Loss:** The percentage of data packets lost during transmission. Even 1% packet loss can cause audio dropouts and call disconnects.
- **QoS (Quality of Service):** A network configuration on switches and routers that prioritizes voice traffic over data traffic.

---

## 3. Real-World Scenarios

### Scenario 1: Remote Call Center Agent Choppy Audio
- **Incident Description:** A customer support agent working from home reports that callers frequently complain about their voice sound choppy and robotic, forcing them to repeat instructions.
- **Troubleshooting Steps:**
  1. Have the user run a ping and speed test to their home connection. Download speed is 80 Mbps, which is sufficient, but ping latency shows spikes up to 450ms.
  2. Ask if the user is connected via Wi-Fi or Ethernet. They are using Wi-Fi on a different floor from their home router.
  3. Explain that wireless packet loss and latency spikes are common causes of choppy VoIP audio.
- **Resolution:**
  - Instructed the user to connect their laptop directly to their home router using an Ethernet cable.
  - Latency stabilized to <25ms with 0% packet loss, resolving the choppy audio issues.

---
## 2. Technical Deep-Dive: VoIP Protocols & Network Jitter
Voice over IP (VoIP) softphones (e.g., Cisco Jabber, Teams calling) convert analog voice signals into digital packets:
- **SIP (Session Initiation Protocol):** Operates on Port `5060` (UDP/TCP). Used to establish, manage, and terminate VoIP calls.
- **RTP (Real-time Transport Protocol):** Transmits the actual voice data payloads. Uses dynamic UDP ports.
- **Network Performance Metrics:**
  - **Latency:** Must remain under 150ms to prevent voice lag.
  - **Jitter:** The variation in packet delivery times. Must remain under 30ms.
  - **Packet Loss:** Must remain under 1% to prevent choppy audio.
### Ticket 1: Choppy Audio on Remote Softphone Calls
- **Incident ID:** INC106740
- **Priority:** P3
- **Problem Statement:** "When I take customer calls via Jabber from home, the audio is choppy and the call drops."
- **Diagnostics:**
  1. Had the user run a ping test to the corporate VPN gateway. Found latency was normal (30ms), but jitter was high (65ms).
  2. Checked their home connection. They were connected to Wi-Fi from another floor, resulting in weak signal strength.
- **Resolution:** Instructed the user to move closer to their home router or connect using an Ethernet cable. Latency and jitter stabilized, resolving the audio issues.
### Query Network QoS Settings on Workstation (PowerShell)
```powershell
# Get active Quality of Service (QoS) routing policies
Get-NetQosPolicy | Select-Object Name, IPProtocol, DSCPValue
```
**Q1: What is jitter in VoIP communications and what causes it?**
A: Jitter is the variation in the arrival time of data packets over a network. It is caused by network congestion, route path changes, or weak Wi-Fi signals, resulting in choppy audio or dropped call packets.

## Debugging VoIP Echo and Hardware Feedback
Voice echo on softphone calls is caused by microphone loops or sample rate mismatches.

### Step-by-Step VoIP Audio Tuning
- **Sample Rate Synchronization:** Open the Windows Sound Control Panel (`mmsys.cpl`), select both the USB headset's playback and recording channels, go to Advanced properties, and ensure both are set to the same sample rate (e.g., CD Quality, 16-bit, 44100Hz). Mismatches force the CPU to resample, introducing lag and echo.
- **Acoustic Echo Cancellation (AEC):** Enable AEC inside the softphone application settings (like Cisco Jabber or Teams). AEC uses algorithms to filter out speaker sound picked up by the microphone.
- **SIP Response Codes:** Softphones register with VoIP servers using the Session Initiation Protocol. Key codes include:
  - **401 Unauthorized:** Incorrect credentials or expired security certificate token.
  - **408 Request Timeout:** VoIP server unreachable due to firewall blocks or VPN disconnects.

## Related Notes
- [[03-03 Core Network Protocols]] - RTP and SIP protocols