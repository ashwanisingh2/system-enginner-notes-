---
tags: [desktop-support, itsm, documentation]
created: 2026-06-25
status: Complete
difficulty: Beginner
estimated-read-time: 10 mins
---

# 05-08 Documentation & Knowledge Base

> [!abstract] Overview
> A guide to maintaining and utilizing IT documentation and Knowledge Base (KB) articles. This note outlines KB structures, the Knowledge-Centered Service (KCS) methodology, and best practices for creating technician-facing and user-facing SOPs.

---

## 1. What Is It? (Concept Explanation)
IT Documentation is the structured storage of configuration settings, resolution paths, and standard operating procedures (SOPs). A Knowledge Base is a centralized repository of these documents, divided into technician-facing guides and user-facing self-service portals.
*Seedha simple shabdon mein bole toh: Documentation IT team ki diary hai. Jab aap koi naya problem solve karte hain, toh use step-by-step likh lete hain taaki agli baar wahi problem aane par time waste na ho. Ek accha KB article naye engineers ko guide karta hai aur users ko self-service ke liye empower karta hai.*

---

## 2. Technical Deep-Dive: Knowledge-Centered Service (KCS)
**Knowledge-Centered Service (KCS)** is a methodology that integrates creating and updating documentation directly into the active support ticket lifecycle. Instead of writing KB articles as a separate task, technicians update documentation as they solve cases.

### The KCS Loop: Solve & Evolve
1. **Capture:** Capture the customer's actual words describing the issue during ticket logging.
2. **Structure:** Format the information using simple, consistent templates (Symptom, Cause, Resolution).
3. **Reuse:** Before starting troubleshooting, search the KB. Reuse existing articles to solve issues faster.
4. **Improve:** If a technician uses an article and notices a mistake or outdated step, they must fix it immediately or flag it for review.

### Knowledge Base vs. Known Error Database (KEDB)
- **Known Error Database (KEDB):** Maintained by Problem Management. It contains temporary workarounds for active root-cause investigations.
- **Knowledge Base (KB):** Maintained by Support Teams. It contains permanent resolutions, instructional guides, and user onboarding checklists.

---

## 3. Real-World Support Scenario (STAR Ticket)
- **Situation:** Following a corporate VPN migration, the Help Desk received over 150 tickets in 24 hours from remote users experiencing a specific connection failure (Error `0x8007274D`).
- **Task:** Create a high-quality Knowledge Base article with step-by-step resolution instructions to enable technicians to close tickets faster and allow users to self-resolve the issue.
- **Action:**
  1. Investigated the VPN error and found that it was caused by a stale virtual network adapter driver that needed to be reinstalled.
  2. Documented the steps clearly using a standard KB template:
     - **Title:** How to Resolve VPN Connection Error 0x8007274D.
     - **Symptom:** User sees "Connection failed" message when clicking connect on Cisco AnyConnect.
     - **Environment:** Windows 10/11 laptops using Wi-Fi.
     - **Resolution Steps:** Step-by-step guide to uninstalling the virtual driver via Device Manager and restarting the client.
  3. Included clear screenshots with highlighted buttons.
  4. Published the article as **KB10291** in the corporate IT Portal.
  5. Created a short link and sent it to the support team, advising them to email it to affected users.
- **Result:** Ticket volumes for the VPN issue dropped by 65% within 48 hours as users self-resolved using the KB link, and technicians reduced average handling times from 15 minutes to 2 minutes.

---

## 4. Standard Structure of a KB Article

| Section | Target Content | Example |
|---|---|---|
| **Article ID / Title** | Clear, searchable description of the task or issue. | `KB20409 - Setting up Corporate Email on iPhone` |
| **Audience** | Specifies who the guide is written for. | `End User (Self-Service) / Helpdesk Staff` |
| **Prerequisites** | Tools or credentials needed before starting. | `Active cellular network connection, M365 password` |
| **Step-by-Step Instructions** | Sequenced, numbered steps with images. | `1. Open Settings, 2. Tap Mail...` |
| **Validation Step** | How to verify the setup worked. | `Send a test email to verify mailbox sync.` |

---

## 5. Frequently Asked Questions (FAQ)

**Q1: What makes a Knowledge Base article effective for end users?**
A: Clear, non-technical language, numbered steps, bold highlights on what to click, and large, clear screenshots. Avoid technical jargon like "DNS registration" and focus on user actions like "Type your email address".

**Q2: Who is responsible for reviewing and approving KB articles?**
A: Typically, senior support engineers (Tier 2/3) or Help Desk managers act as Knowledge Managers. They review draft articles for technical accuracy, formatting compliance, and clarity before publishing them to the public catalog.

**Q3: How do we prevent the Knowledge Base from containing outdated information?**
A: Enforce KCS workflows (technicians flag or edit articles during tickets), run monthly audits of the most frequently used articles, and set automatic expiration timers (e.g., articles expire and require review after 12 months).

**Q4: Should internal IT procedures be shared with end users?**
A: No. Internal IT documentation (like server configuration settings or administrator passwords) should be kept in a secure, technician-only section of the KB. User-facing folders should contain only self-service instructions.

---

## Related Notes
- [[05-03 Problem Management]] - Known error data
- [[13-09 Shift Handover Checklist]] - Daily handovers logs
- [[05-07 Communication Skills for Support]] - Handling user tickets