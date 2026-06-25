---
tags: [desktop-support, applications, browser]
created: 2026-06-25
status: Complete
difficulty: Beginner
estimated-read-time: 11 mins
---

# 06-04 Browser Troubleshooting

> [!abstract] Overview
> A troubleshooting guide for enterprise web browsers (Chrome, Edge). This note covers clearing cache vs cookies, resolving SSL/TLS certificate warnings, debugging extensions, and testing proxy routing.

---

## 1. What Is It? (Concept Explanation)
Web browsers act as the client interface for enterprise SaaS and intranet web applications. Browser troubleshooting involves identifying if a website failure is caused by local cached assets, browser extensions, network proxy routing, or server-side issues.
*Seedha simple shabdon mein bole toh: Web browser user aur target website ke beech ka gate hai. Jab kisi web-page par formatting bigad jaye ya redirect loops aane lagein, toh iska matlab hai browser cache ya cookies corrupt ho chuki hain. Chrome aur Edge ko clean reset karna aur DNS clear karna hi resolves dilata hai.*

---

## 2. Technical Deep-Dive: Cache, Cookies, and DNS Resolving
Understanding how browsers load pages is key to resolving issues:

### Cache vs. Cookies
- **Browser Cache:** Stores static assets (images, stylesheets, HTML documents). If a web developer updates a corporate tool, a user's browser might still load old cached code, resulting in broken layouts.
- **Cookies:** Store session identifiers and preferences. If a cookie is corrupted, the web server rejects the token, triggering `400 Bad Request` or infinite login redirect loops.

### DNS & HSTS Security Settings
- **HSTS (HTTP Strict Transport Security):** Enforces secure HTTPS connections. If a site's SSL certificate expires, browsers block access with a warning and do not allow the user to click "Proceed". Technicians can clear HSTS security rules for specific domains inside Chrome/Edge by navigating to `chrome://net-internals/#hsts`.
- **DNS Cache:** Browsers keep their own DNS cache independent of the Windows OS. To clear it, navigate to `chrome://net-internals/#dns` and click "Clear host cache".

---

## 3. Real-World Support Scenario (STAR Ticket)
- **Situation:** Users in the HR department report they cannot log on to the new portal. When using Google Chrome, the page displays error: `ERR_TOO_MANY_REDIRECTS`. The page loops and crashes after a few seconds.
- **Task:** Diagnose the redirect loop, clear specific profile logs, and restore access to the HR portal.
- **Action:**
  1. Opened an **Incognito window** in Chrome and attempted to load the HR portal. The portal loaded and allowed login, proving the web server and corporate network were working.
  2. Identified that the issue was caused by a corrupted session token/cookie in the user's primary Chrome profile.
  3. Instead of clearing the entire browser history (which would delete the user's saved passwords and history), opened the HR portal, pressed `F12` to open Developer Tools.
  4. Went to the **Application** tab, expanded **Cookies** on the left menu, right-clicked the portal's domain name, and clicked **Clear**.
  5. Refreshed the page and prompted the user to log in again.
- **Result:** The portal loaded normally in the standard browser profile, and the login completed without loops.

---

## 4. Browser Errors & Diagnostics

| Error Code | Potential Root Cause | Resolution Action |
|---|---|---|
| **ERR_CONNECTION_TIMED_OUT** | Firewall blockage or incorrect proxy settings. | Verify proxy settings in `inetcpl.cpl`. |
| **ERR_CERT_DATE_INVALID** | Client system time is out of sync or expired certificate. | Synchronize Windows system clock. |
| **ERR_TOO_MANY_REDIRECTS** | Corrupt session cookies or routing loops. | Clear browser cookies for that specific site. |
| **ERR_NAME_NOT_RESOLVED** | DNS server is unreachable or incorrect domain. | Run `ipconfig /flushdns` in command prompt. |

---

## 5. Frequently Asked Questions (FAQ)

**Q1: How do I identify if a browser extension is causing a page crash?**
A: Run the browser in safe mode or open the page in an Incognito/InPrivate window (which disables extensions by default). If the page loads successfully, open Extension Settings (`chrome://extensions`) and disable extensions one by one to find the culprit.

**Q2: What is the difference between hard reload and normal reload?**
A: A normal reload (`F5`) uses cached files if they are not expired. A hard reload (`Ctrl + F5` or `Ctrl + Shift + R`) bypasses the local browser cache and requests all assets directly from the web server.

**Q3: How do I configure proxy settings for Google Chrome?**
A: Google Chrome uses the system proxy settings managed by Windows. Press `Win + R`, type `inetcpl.cpl` (Internet Properties), go to the **Connections** tab, click **LAN Settings**, and configure the proxy server address or PAC script path.

**Q4: Why does a webpage display an SSL Certificate Warning?**
A: This occurs when the website's certificate has expired, is self-signed (untrusted), is missing intermediate certificates, or if the client computer's system time is set incorrectly, causing it to evaluate the certificate as expired.

---

## Related Notes
- [[03-09 Proxy & Firewall Basics]] - Network proxy configuration
- [[12-07 HTTP Status Codes]] - Web error codes reference
- [[09-05 Remote Management Commands]] - Network test commands