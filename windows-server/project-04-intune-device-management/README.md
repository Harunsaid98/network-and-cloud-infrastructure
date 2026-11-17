# Intune Device Management Lab

Microsoft Intune, Windows Autopilot and Microsoft Defender for Business in a personal lab.

I moved domain-joined Windows 11 PCs from my Active Directory lab to cloud management with Microsoft Intune, set up a new laptop with Windows Autopilot, and worked through helpdesk and security tickets on those devices.

**Case study:** [Intune_Device_Management_Case_Study.pdf](Intune_Device_Management_Case_Study.pdf) (17 pages, with screenshots)

Lab work: 28 October – 15 November 2026

---

## What the lab covers

Each section of the case study starts with a question. Page 2 lists the question for every ticket with its page number, and section 14 lists the accounts, devices and terms used.

| # | Question | What I did |
|---|---|---|
| 1 | How do you move domain-joined PCs to cloud management and support the users afterwards? | Brought two domain PCs into Intune, set up a new laptop with Autopilot, then handled the support cases that followed (18 tickets, T200–T217) |
| 2 | How do you get a domain PC into Intune, and what if enrollment fails? | Hybrid join, then enrollment by Group Policy. Enrollment failed with 0x8018002A; the sign-in log showed MFA blocking it, and I excluded only the Intune Enrollment app |
| 3 | How do you allow company email only on PCs that meet the baseline? | A compliance policy and a Conditional Access policy. I tested the block by raising the minimum OS version instead of turning off protection |
| 4 | How do you configure, encrypt, patch and install software without visiting the PC? | Settings catalog, silent BitLocker with the key in Entra ID, pilot and broad update rings, Store apps and 7-Zip packaged as a Win32 app |
| 5 | A user says the PC locks too quickly. What is the cause? | Found two profiles setting the same lock setting to different values and removed the extra one. Intune showed the conflict, but the PC's own log didn't show the short lock time |
| 6 | How does a new laptop get set up without IT setting it up by hand? | Windows Autopilot: hardware hash import, dynamic group, user-driven Entra join, Enrollment Status Page. About 10 minutes to the desktop |
| 7 | A laptop asks for a BitLocker recovery key. What does the helpdesk do? | Found the key by its ID with a helpdesk account (the read is in the audit log), fixed the cause and checked that the used key was replaced |
| 8 | A work laptop is stolen. In what order do you act? | Revoked the user's sessions, wiped the laptop from Intune and checked that it came back to Autopilot setup |
| 9 | How do you give staff work email on private phones without managing the phone? | App protection without enrollment: app PIN, no pasting into personal apps, and a selective wipe of only the work data (tested on my own iPad) |
| 10 | Is anything watching the PCs for attacks? | Defender for Business: onboarded the PCs, investigated and closed a test alert, tested phishing protection, and chose what to fix first from 64 recommendations |
| 11 | Hybrid join or cloud-only? | A comparison of the two from what I saw in the lab, checked against Microsoft's guidance |

## Lab setup

| Component | Role |
|---|---|
| LAB-DC01 | AD DS and DNS (domain lab.local), enrollment GPO |
| FS01 | Microsoft Entra Connect Sync (users and computers), hybrid join SCP |
| WIN11-IT-01, WINN11-IT-02 | Hybrid-joined Windows 11 PCs, enrolled in Intune by Group Policy |
| WIN11-AP-01 | The "new laptop": a VM with internet access only, set up with Windows Autopilot (Microsoft Entra joined) |
| Personal iPad | Outlook with app protection, not enrolled |
| Microsoft 365 Business Premium (trial) | Entra ID, Conditional Access, Intune, Defender for Business |

## Problems along the way

- Automatic enrollment failed with 0x8018002A. Two MFA policies were blocking the background enrollment sign-in. The Microsoft Intune Enrollment app was missing from the tenant, so I created it with Microsoft Graph and excluded only that app.
- The new Autopilot laptop wasn't encrypted, because the BitLocker policy was assigned only to the pilot group. I added the Autopilot device group.
- The BitLocker recovery test didn't trigger at first. The TPM protector used PCR 0, 2, 4, 11, which don't include Secure Boot, so I re-created it with Secure Boot on (PCR 7, 11).
- After a correct recovery key, the recovery screen came back, because Secure Boot was still off. Turning it back on fixed it.
- A laptop showed as Compliant without any compliance policy, because the tenant setting for devices with no policy was still on its default (Compliant). In a company I would change it to Not compliant.

## Skills

Microsoft Intune (enrollment, compliance, Settings catalog, BitLocker, update rings, Win32 apps, app protection, remote wipe), Windows Autopilot, Microsoft Entra ID (hybrid join, Entra join, Conditional Access, sign-in and audit logs), BitLocker recovery, Microsoft Defender for Business, Group Policy, PowerShell, Microsoft Graph.

> All screenshots are from my own lab. The tenant domain, admin account names, device, object, session and correlation IDs, BitLocker key IDs, serial numbers, IP addresses and local paths are blacked out. No passwords or recovery keys are shown.
