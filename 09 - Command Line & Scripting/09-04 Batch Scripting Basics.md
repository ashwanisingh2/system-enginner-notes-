---
tags: [desktop-support, cli, batch]
created: 2026-06-25
status: Complete
difficulty: Beginner
estimated-read-time: 11 mins
---

# 09-04 Batch Scripting Basics

> [!abstract] Overview
> A guide to creating and executing legacy Windows Batch scripts (.bat / .cmd). This note covers variables, flow control, exit codes, and administrative logons automations.

---

## 1. What Is It? (Concept Explanation)
Batch scripting is the process of storing a sequence of Windows Command Prompt (CMD) commands in a text file with a `.bat` or `.cmd` extension. When the file is executed, the interpreter runs the commands sequentially.
*Seedha simple shabdon mein bole toh: Batch script ek auto-run CMD file hai. Jab humein corporate networks par multi-step configurations (jaise network drives connect karna, ya old printers delete karna) har login par run karni hoti hain, tab hum batch file use karte hain.*

---

## 2. Technical Deep-Dive: Variables, Loops, and Error Checking
Batch scripts use a specific syntax structure maintained by the `cmd.exe` interpreter:

### 1. Variables
- Define variables using `set`:
  `set SERVER_PATH=\\fileserver\public`
- Access variables by wrapping the name in `%` signs:
  `net use P: %SERVER_PATH%`

### 2. Flow Control (If / Goto)
Batch scripts support basic conditional statements and branching logic:
```cmd
@echo off
ping -n 1 10.0.0.1 >nul
if %errorlevel% equ 0 (
    echo Connection Successful
) else (
    echo Connection Failed
    goto ERROR_HANDLER
)
exit /b 0

:ERROR_HANDLER
echo Running Diagnostics...
```

### 3. Loop Execution (FOR)
- Process lists of files or items:
  `for %%i in (*.tmp) do del %%i`

---

## 3. Real-World Support Scenario (STAR Ticket)
- **Situation:** Remote employees returning to the office report that their shared network printers and network folders fail to map. The Help Desk is flooded with tickets to manually configure these settings.
- **Task:** Create a logon batch script that automatically maps office resources based on the user's connection status.
- **Action:**
  1. Wrote a batch script named `logon_script.cmd` with clean logic.
  2. Used `@echo off` at the beginning of the script to hide command text and keep the user interface clean.
  3. Added code to disconnect existing network mappings to prevent conflicts:
     ```cmd
     net use * /delete /y
     ```
  4. Added ping checks to verify the corporate network gateway is reachable:
     ```cmd
     ping -n 1 10.10.1.1 >nul
     if %errorlevel% neq 0 exit /b 1
     ```
  5. Configured the file share mappings:
     ```cmd
     net use S: \\fileserver\shares\finance
     net use P: \\fileserver\shares\public
     ```
  6. Configured default printer mapping:
     ```cmd
     rundll32 printui.dll,PrintUIEntry /in /n "\\fileserver\Accounting-Printer"
     ```
  7. Saved the script and coordinated with the AD admin to link the batch file to the "Finance Users" GPO logon profile.
- **Result:** Mappings applied automatically at logon, resolving user connection issues and reducing Help Desk tickets by 90%.

---

## 4. Batch Scripting Operations Table

| Syntax / Command | Function | Support Example |
|---|---|---|
| **@echo off** | Hides the command prompt command text during execution. | Placed on line 1 of all clean scripts. |
| **set /p** | Prompts the user for input and saves it to a variable. | `set /p CompName=Enter Computer Name:` |
| **pause** | Suspends execution and prints "Press any key to continue". | Used to keep the console open for users to read logs. |
| **exit /b <exitcode>** | Exits the batch file, returning a custom exit code. | `exit /b 0` indicates script ran successfully. |

---

## 5. Frequently Asked Questions (FAQ)

**Q1: What is the difference between .bat and .cmd files?**
A: In modern Windows, both formats behave identically. Historically, `.bat` was the default extension for MS-DOS, while `.cmd` was introduced for Windows NT to run inside the native Command Prompt interpreter.

**Q2: How do I run a batch script silently in the background?**
A: You can launch it using a VBScript wrapper or a PowerShell command. For example, run: `powershell -WindowStyle Hidden -Command "Start-Process C:\script.cmd -WindowStyle Hidden"`.

**Q3: Why does my batch file close instantly when I double-click it?**
A: When a batch file finishes executing, the CMD window closes automatically. If you want the window to remain open so you can read error logs, add the `pause` command to the very end of your script.

**Q4: How do I pass parameters into a batch file?**
A: Parameters passed to a batch script are read sequentially using percent signs and numbers (from `%1` to `%9`). For example, in `install.cmd app.msi`, parameter `%1` evaluates to `app.msi`.

---

## Logging Batch Script Execution Output
To audit logon scripts, redirect the console execution output to a text log:
- **Redirect Command:**
  `call C:\scripts\logon.cmd > C:\temp\logon_run.log 2>&1`
  This command captures all batch script output and error logs, writing them to a local log file for analysis.

## Related Notes
- [[09-01 CMD Masterclass for Desktop Support]] - Syntax rules
- [[02-05 Group Policy (GPO)]] - Deploying logon scripts
- [[09-03 Useful PowerShell Scripts for Support]] - Modern scripting options