# Project 1 – Windows Domain Administration, Identity & Auditing

**Personal lab project** · Windows Server 2025 · Active Directory · Group Policy · PowerShell

I worked through seven support-style tickets in my own `lab.local` Active Directory lab. For each one I checked the current state first, made one change, and then tested that it actually worked. A few things didn't go the way I expected. Those are included too, because that's where I learned the most.

📄 **[Open the full report with screenshots (PDF)](Project1_Domain_Administration_Identity_Auditing.pdf)**

---

## The lab

| Machine / account | Role |
|---|---|
| **DC01** | Domain controller and DNS – Windows Server 2025 (Evaluation) |
| **WIN11-IT-01** | Domain workstation – Windows 11 Pro 25H2 |
| **LAB\ajaradi** | My everyday account (standard user) |
| **LAB\adm-ajaradi** | Separate admin account, used only when elevation is needed |
| **LAB\jonas** | Helpdesk test account |

All test accounts live in a separate `PortfolioLab` OU, so tests never touch the real structure. The VMs run in VMware and I manage them remotely over Tailscale.

## The seven cases

| # | Ticket | Result |
|---|---|---|
| 1 | Is the domain healthy? | Healthy. I also found DC01 advertising its VPN (Tailscale) address in DNS, and the client was actually using it |
| 2 | No admin work with normal accounts | Confirmed my daily account isn't a local admin. Admin rights only in a separately elevated window |
| 3 | Log successful and failed logons | Audit GPO applied, and Events 4624 and 4625 recorded on the client |
| 4 | Helpdesk may reset Finance passwords only | Jonas can reset Finance ✔, is denied on HR ✘, and has no admin rights |
| 5 | Group Policy Management "not found" | Installed the RSAT Group Policy Management tool and confirmed the console opens |
| 6 | "The trust relationship … failed" | Reproduced it and repaired it **without removing the PC from the domain** |
| 7 | "Access denied" deleting an account as admin | The cause was accidental-deletion protection, not permissions |

## What I learned

- **Configured isn't the same as applied.** A GPO can look right in the console and still not reach the PC. I used `gpresult` and `auditpol` on the client to confirm it.
- **Test both sides of a permission.** For Jonas I proved the reset works on Finance *and* is denied on HR. My first test accidentally ran as Administrator, so it proved nothing. Now I check `whoami` before every permission test.
- **Some problems only appear after a restart.** After resetting the computer account, the PC still said the trust was fine. The failure only showed up after a reboot. Then I repaired it with `Test-ComputerSecureChannel -Repair`.
- **"Access denied" for an admin doesn't always mean missing rights.** Check the object's settings first.

## Limitations

- One domain controller, so no replication or redundancy testing
- Windows Server is an evaluation installation
- `lab.local` is only for the lab. A real company would use a domain it owns
- DC01's Tailscale address is still registered in DNS. It's documented, and I'll clean it up before adding the next server

## Next

**Project 2:** a file server with Finance and HR shares, group-based permissions, moving an employee between departments, and restoring a deleted file, all built on the same domain.
