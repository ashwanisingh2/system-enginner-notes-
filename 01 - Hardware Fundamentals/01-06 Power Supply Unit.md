---
tags: [desktop-support, hardware, psu, power]
created: 2026-06-25
status: Complete
difficulty: Intermediate
estimated-read-time: 9 mins
---

# 01-06 Power Supply Unit

> [!abstract] Overview
> Deep guide to Power Supply Unit (PSU) form factors, wattage requirements calculations, testing connector pins, and identifying symptoms of power failure.

---

## What Is It? (Concept Explanation)
The PSU (Power Supply Unit) converts high-voltage alternating current (AC) electricity from the wall outlet into low-voltage direct current (DC) electricity (3.3V, 5V, 12V) that the motherboard and internal components need.
*Seedha simple shabdon mein: PSU computer ka dil hai. Jaise dil oxygenated blood pure shareer ko bhejta hai, waise hi PSU safe, regulated power motherboard aur baki parts ko distribute karta hai. Agar PSU kamzor hoga, toh system heavy games ya apps chalate waise turant crash kar jayega.*

---

## How It Works (Deep Dive)
- **Voltage Rails:**
    - **+12V Rail:** Powers high-load parts like the CPU and GPU.
    - **+5V Rail:** Powers storage devices (HDD/SSD) and USB ports.
    - **+3.3V Rail:** Powers RAM and PCIe motherboard slots.
- **Form Factors:** ATX (Standard Desktop), SFX (Small Form Factor).
- **80 Plus Certification:** Measures energy efficiency. Ratings range from 80 Plus Standard to Bronze, Silver, Gold, Platinum, and Titanium.
- **Connectors:**
    - **24-pin ATX:** Main motherboard power cable.
    - **4+4-pin EPS:** Dedicated CPU power cable.
    - **6+2-pin PCIe:** Dedicated Graphics Card power cable.
    - **SATA Power:** Connects hard drives and SSDs.

---

## Real-World Scenarios
**Scenario 1:** A user complains that when they begin rendering a 3D animation, the PC immediately shuts down, but otherwise works fine for web browsing.
- Problem: Sudden shutdowns under heavy GPU/CPU workload.
- Root Cause: The PSU is unable to output enough wattage on the +12V rail under load.
- Solution: Calculate total component wattage requirements and upgrade to a higher capacity PSU (e.g., from 450W to 650W).

**Scenario 2:** An engineer presses the PC power button; nothing happens. No lights turn on, and no fans spin.
- Problem: No Power.
- Root Cause: Faulty power button connection, damaged power cable, or a dead PSU.
- Solution: Perform the "paperclip test" on the 24-pin ATX connector to see if the PSU fan turns on. If it doesn't spin, the PSU is dead.

---

## Step-by-Step Troubleshooting Guide
1. **Verify Wall Power:** Ensure the wall outlet is working and the PSU switch on the back of the computer is flipped to the **I** (On) position.
2. **Perform the Paperclip Test:**
   - Disconnect the 24-pin cable from the motherboard.
   - Insert a paperclip ends into **pin 16 (Green - PS_ON)** and any **black pin (Ground)**.
   - Plug the PSU into the wall and turn it on. If the PSU fan spins, the PSU is likely working; the issue could be the motherboard or power switch.
3. **Use a PSU Tester:** Plug connectors into a dedicated LCD PSU tester to verify output voltages on 12V, 5V, and 3.3V lines.
4. **Isolate Overheating:** If the PSU is hot and turns off after an hour, verify its exhaust fan is running. If not, replace the PSU.

---

## Essential CMD Commands for Power Diagnostics
*(Power parameters are managed via local hardware BIOS and OS drivers. Use powercfg to audit settings)*
```cmd
:: Generate a detailed energy efficiency and diagnostics report
powercfg /energy

:: Create a detailed battery and power utilization log (for laptops)
powercfg /batteryreport
```

---

## Common Mistakes Desktop Support Engineers Make
> [!warning] Avoid These Mistakes
> - **Opening a PSU case:** Never attempt to open or repair a PSU. The large capacitors inside store lethal voltage charges even when the PSU has been unplugged for months.
> - **Using modular cables from another PSU:** Modular PSU cables are not standardized. Using cables from a different model can swap pin layouts and fry the motherboard.
> - **Ignoring efficiency ratings:** Using uncertified generic PSUs in corporate systems can lead to fire hazards and motherboard damage.

---

## SOP (Standard Operating Procedure)
- [ ] Shut down the system, unplug the power cord, and toggle the PSU switch to **O**.
- [ ] Disconnect the 24-pin motherboard, CPU EPS, PCIe, and SATA power cables.
- [ ] Unscrew the four mounting screws on the back of the case.
- [ ] Slide the old PSU out and place the new PSU in.
- [ ] Secure the new PSU with mounting screws.
- [ ] Reconnect all internal power cables. Verify clips lock in place.
- [ ] Connect the power cord, toggle the switch to **I**, and boot the system.

---

## Pro Tips (From Senior Engineers)
> [!tip] Field Secrets
> - **Capacitor Squeal:** If you hear a high-pitched whistling noise (coil whine) coming from the PC, the PSU capacitors are degrading and the unit should be scheduled for replacement.

---

## Quick Revision Summary
| # | Concept | Remember This |
|---|---------|---------------|
| 1 | Input Voltage | Ensure red slider is set to correct wall voltage (115V or 230V) |
| 2 | Green Pin 16 | Short with black ground pin to boot PSU without motherboard |
| 3 | +12V Rail | Delivers critical power to CPU and dedicated GPU |
| 4 | Capacitors | Dangerous, never open a PSU case to fix it |
| 5 | Efficiency | 80 Plus Gold/Platinum reduces heat and power losses |

---

## Interview Questions & Model Answers
**Q1: What are the indicators of a failing Power Supply Unit?**
A: Common indicators include random system restarts under load, Blue Screen of Death errors related to hardware instability, failure to boot (POST fails), fans spinning briefly and stopping, or a burning smell/clicking noise coming from the back of the PC.

**Q2: How do you manually test if a power supply is dead without a motherboard?**
A: I perform the "paperclip test." I unplug the PSU from the motherboard, find the 24-pin ATX connector, short the PS_ON pin (pin 16, green wire) to any ground pin (black wire) using a paperclip, and turn on the PSU. If the fan spins, the PSU is receiving power. If it does not spin, the unit is dead.

**Q3: What is the 80 Plus certification?**
A: It is an energy efficiency rating system for power supplies. It certifies that the PSU will operate at a minimum of 80% efficiency under various workloads, converting less electricity into wasted heat. Ratings include White, Bronze, Silver, Gold, Platinum, and Titanium.

---

## Related Notes
- [[01-01 Computer Architecture Overview]] — Powering internal components
- [[01-10 Hardware Troubleshooting Masterclass]] — Diagnosing boot and power failures