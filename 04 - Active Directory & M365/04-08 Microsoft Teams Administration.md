---
tags: [desktop-support, m365, teams]
created: 2026-06-25
status: Complete
difficulty: Beginner
estimated-read-time: 14 mins
---

# 04-08 Microsoft Teams Administration

> [!abstract] Overview
> A troubleshooting guide for Microsoft Teams. This note covers Teams caching issues, audio/video device configuration, meeting room configurations, and checking connection health logs.

---

## 1. What Is It? (Concept Explanation)
Microsoft Teams is the standard communication tool in modern offices. Support engineers troubleshoot Teams application performance, hardware integration (webcams/headsets), and login issues.
*Seedha simple shabdon mein bole toh: Teams call dropouts, bad audio/video quality, aur login loops desktop support engineers ki common complaints hain. Isme background local cache folders delete karna, audio settings check karna, aur media settings reset karna shaamil hai.*

---

## 2. Real-World Scenarios

### Scenario 1: Teams Camera and Audio Devices Not Detected
- **Incident Description:** An executive reports that during a call, Teams displays the warning: "No microphone or camera found," although their Jabra headset is connected.
- **Troubleshooting Steps:**
  1. Open Teams Settings -> **Devices**. Check the audio and video device dropdown selections.
  2. Confirm the headset is set as the **Default Device** and **Default Communication Device** in Windows Sound settings (`control mmsys.cpl`).
  3. Verify application permissions: Open Windows Settings -> **Privacy & Security** -> **Camera/Microphone**. Confirm the toggle for **Allow desktop apps to access your camera/microphone** is enabled.
- **Resolution:**
  - Enabled Windows app privacy permissions for the microphone and camera.
  - Replaced the USB connector hub to ensure the headset remained powered during calls.

---

## 3. SOP: Clearing the Microsoft Teams Cache
If Teams experiences login loops, missing chats, or performance freezes, clear its local cache:
1. Quit Microsoft Teams completely (verify in Task Manager that no `Teams.exe` processes are running).
2. Open the Run dialog (`Win + R`).
3. For classic Teams, paste: `%appdata%\Microsoft\Teams` and delete all folders.
4. For new Teams, paste: `%localappdata%\Packages\MSTeams_8wekyb3d8bbwe` and delete the subfolders.
5. Restart Teams and have the user log on again.

---
## 2. Technical Deep-Dive: Media Ports & Teams Client Cache
Microsoft Teams routes voice, video, and screen sharing data over real-time media channels:
- **Ports & Protocols:** Teams media traffic uses Real-time Transport Protocol (RTP) over UDP. Network firewalls must allow outbound traffic on UDP ports `3478` through `3481` to prevent call drops and choppy audio.
- **Teams Cache Store:** The new Teams client stores cache files in:
  `%localappdata%\Packages\MSTeams_8wekyb3d8bbwe\LocalState\`
- **Calling Policies:** Configured in the Teams Admin Center, determining user privileges for creating external meetings, dial-out numbers, and screen share controls.
### Ticket 1: Microphone and Camera Blocked in Teams Call
- **Incident ID:** INC104812
- **Priority:** P3
- **Problem Statement:** "When I join a Teams call, I can see other people but they cannot hear me, and my camera is grayed out."
- **Diagnostics:**
  1. Verified the microphone and camera were functional in native Windows camera apps.
  2. Checked Windows Settings > Privacy & Security > Microphone / Camera.
  3. Found "Allow desktop apps to access your microphone" was toggled to Off.
- **Resolution:** Enabled the privacy access toggle, restarted the Microsoft Teams application, and verified the microphone and camera active icons showed in a test call.
### Clear Teams Cache Command Line (CMD)
```cmd
:: Close Teams process tree and delete local cache folder
taskkill /f /im ms-teams.exe
powershell -Command "Remove-Item -Path $env:LOCALAPPDATA\Packages\MSTeams_8wekyb3d8bbwe\LocalState\* -Recurse -Force -ErrorAction SilentlyContinue"
```
**Q1: What network ports must be open to ensure high-quality Microsoft Teams video calls?**
A: Outbound UDP ports `3478, 3479, 3480, and 3481` must be open on the corporate network firewall to route real-time media streams (audio, video, screen share) directly, preventing latency caused by TCP retransmissions.

**Q2: How do you resolve a Teams app that hangs on the startup loading screen?**
A: I terminate the Teams process in Task Manager, clear the Teams cache files located under the AppData directory, reset the Teams app package in Windows App Settings, and restart the application.

## Advanced Teams Media Bypass & Quality of Service (QoS)
Teams call routing is optimized over corporate network infrastructure using Quality of Service (QoS) configurations.

### Configuring DSCP Packet Markings
To ensure real-time audio and video packets are prioritized over database downloads on congested network switches:
1. **Voice Packets:** marked with **DSCP 46 (Expedited Forwarding - EF)**, routing calls through the highest-priority switch queues.
2. **Video Packets:** marked with **DSCP 34 (Assured Forwarding - AF41)**.
3. **Application policies:** Group Policies are created in AD to assign these DSCP tags to the `Teams.exe` executable file dynamically.

### Teams Network Assessment Diagnostics
Support teams use Microsoft's network assessment diagnostic tool to verify network readiness:
- **Jitter limit:** Must remain under 30ms.
- **Latency target:** Must remain under 150ms round-trip.
- **Packet loss:** Must remain under 1% during calls.
- If these metrics fail, configure Media Bypass in the Teams Admin Portal to route call traffic directly between local users rather than hairpinning packets through cloud gateways.

## Related Notes
- [[06-03 Microsoft Teams Troubleshooting]] - Teams cache and hardware
- [[04-06 Microsoft 365 Admin Center]] - Cloud licenses