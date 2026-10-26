# Hybrid Identity Lab: Active Directory + Microsoft Entra ID + Microsoft 365

A personal lab where I connected an on-premises Active Directory domain to Microsoft Entra ID and Microsoft 365, then worked through the everyday tasks of an IT support / junior sysadmin role.

**Read the case study:** [Hybrid_Identity_Lab_Case_Study.pdf](Hybrid_Identity_Lab_Case_Study.pdf) (11 pages, with screenshots)

Lab work: 16-22 October 2026

---

## What the lab covers

Each section of the case study starts with a question and then shows how I solved it.

| # | Question | What I did |
|---|---|---|
| 1 | How do you connect on-prem AD to Microsoft 365 with one identity per user? | Microsoft Entra Connect Sync on FS01, syncing only one OU, with password hash sync and password writeback |
| 2 | Is the AD data ready to sync, and who owns a synced user's details? | Found and fixed three empty UPNs before the first sync; proved AD is the source of authority |
| 3 | How do you license users without doing it one by one? | Group-based licensing from an AD group; mailboxes created automatically |
| 4 | How do you require MFA without locking yourself out? | Conditional Access tested in report-only first; break-glass account excluded from all policies |
| 5 | Can users reset their own password, and does it work on the domain PC? | SSPR with writeback; the cloud reset worked for domain logon |
| 6 | A user says "I can't sign in". What is the real cause? | Read the sign-in log first: error 50057 (disabled account) and 50126 (wrong password) |
| 7 | A new starter begins on Monday. What has to happen? | AD account, groups, sync, licence, mailbox, MFA and mapped F: drive |
| 8 | An employee leaves. In what order? | Shared mailbox while licensed, manager access, revoke sessions, disable in AD, free the licence |
| 9 | How do you limit the helpdesk? | Helpdesk Administrator role: can reset normal users, blocked from admins and Conditional Access |

## Lab setup

| Component | Role |
|---|---|
| DC01 | AD DS + DNS (domain lab.local) |
| FS01 | File server and Microsoft Entra Connect Sync |
| WIN11-IT-01 | Domain-joined Windows 11 client |
| Microsoft 365 Business Premium | Entra ID, Conditional Access, SSPR, Exchange Online |

## Problems I solved along the way

- Entra Connect wizard sign-in failed (WAM error 3399614467). Fixed with the `/InteractiveAuth` switch.
- Disabling security defaults created four Microsoft-managed Conditional Access policies with no break-glass exclusion. I added the exclusion and kept them on.
- A user saw "account locked", but the sign-in log showed the account was disabled (50057).
- An admin password reset from Entra was rejected by on-prem AD policy. A longer, policy-compliant temporary password was accepted.

## Skills

Active Directory, Microsoft Entra Connect Sync, Microsoft Entra ID, Conditional Access, MFA, SSPR and password writeback, Microsoft 365 group-based licensing, Exchange Online, sign-in log troubleshooting, onboarding and offboarding, least-privilege role assignment.

> All screenshots are from my own lab. Tenant, object, session and correlation IDs and IP addresses are blacked out, and no passwords are shown.
