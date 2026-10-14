# Project 2 – File Server, Access and Recovery

This project documents a Windows Server file-server lab built on top of my existing `lab.local` Active Directory environment.

The goal was to work through realistic support tasks rather than only show the finished configuration. I used a dedicated member server, `FS01`, for Finance and HR file shares, group-based access, Group Policy drive mapping, a department transfer, file recovery, and an access-denied case caused by an old Windows logon token.

The full evidence and screenshots are in:

**[P2_File_Server_Access_and_Recovery_FINAL.pdf](./P2_File_Server_Access_and_Recovery_FINAL.pdf)**

## Lab environment

| System | Role | Details |
| --- | --- | --- |
| `LAB-DC01` | Domain controller | Windows Server 2025, AD DS and DNS, private lab IP |
| `FS01` | Member file server | Windows Server 2025, static private IP, separate `D:` data disk |
| `WIN11-IT-01` | Domain client | Used for user-side access and troubleshooting tests |
| `WINN11-IT-02` | Domain client | Used for additional user-side testing |

## Access model

I used an AGDLP-style structure instead of assigning folder permissions directly to individual users.

```text
User
  ↓
Global role group
  ↓
Domain Local resource group
  ↓
NTFS / SMB permission
```

Examples from the lab:

| Role group | Resource group | Access |
| --- | --- | --- |
| `GG_Finance_Users` | `DL_FS01_Finance_Modify` | Modify |
| `GG_Finance_Reviewers` | `DL_FS01_Finance_Read` | Read and execute |
| `GG_HR_Users` | `DL_FS01_HR_Modify` | Modify |

This made later changes easier because access could be changed through group membership instead of editing the folder ACL for each user.

## Tickets completed

### #2001 – Finance share and role-based access

Created the Finance share with separate modify and read-only access. `finance.test01` could create and delete files, `finance.reviewer` could read but not change them, and users outside Finance were refused.

One early test failed because I typed the UNC path incorrectly as `\\FS01\\Finance`. DNS and TCP 445 were working, so the problem was the path rather than the server or permissions.

### #2002 – HR share

Created the HR share using the same group-based model. `hr.test01` could create and delete files, while `finance.test01` was refused when using the direct path `\\FS01\HR`.

This also gave me a useful troubleshooting distinction: `Cannot find path` means the path itself is wrong, while `Access is denied` is an actual permission result.

### #2003 – Automatic department drive mapping

Created a Group Policy Preference that maps:

- `F:` to `\\FS01\Finance`
- `H:` to `\\FS01\HR`

The Finance drive is targeted to either `GG_Finance_Users` or `GG_Finance_Reviewers`, while the HR drive is targeted to `GG_HR_Users`.

I initially used `AND` between the two Finance targeting rules. That would have required a user to belong to both Finance groups, so I corrected it to `OR` before the final test.

### #2004 – Move a user from Finance to HR

Moved the same user account from Finance to HR by changing group membership, the Department attribute, and the OU location.

After a full sign-out and sign-in, the user received the HR drive, lost the Finance drive, could open the HR share, and was refused from the Finance share.

The stale `F:` drive showed why `gpupdate` is not the same as a new logon. Group membership changes can require a fresh Windows security token.

### #2005 – Restore an accidentally deleted Finance file

Installed Windows Server Backup and created a one-time backup of `D:\Shares\Finance`.

I deleted `Budget-2026.txt`, restored only that file to its original location, and then checked both its contents and ACL. The restored file matched the original permissions.

The restore took about 11 minutes end to end, including finding the backup and selecting the file. The actual recovery copy took only seconds.

This backup was stored on the same FS01 VM, so it demonstrates file recovery but is not a production backup design.

### #2006 – Group membership changed but access is still denied

Logged in as `noaccess.test01`, confirmed Finance access was denied, and then added the user to `GG_Finance_Users` while the session was still active.

The user still received `Access is denied` because the current Windows logon token did not contain the new group membership. After a full sign-out and sign-in, `whoami /groups` showed the Finance groups and the share opened successfully.

No FS01 permissions were changed to fix the issue.

## What I learned

The main lesson from this project was how the different layers fit together when something does not work. I had to check group membership, NTFS permissions, SMB permissions, Group Policy targeting, mapped drives, the current user token, and the actual error being returned.

The parts I found most useful were:

- using groups instead of assigning permissions directly to users
- understanding how NTFS and SMB permissions combine
- testing access as the real affected user instead of only as an administrator
- separating path errors from permission errors
- understanding why `gpupdate` does not refresh group membership in the current logon token
- using `Replace` and "Remove this item when it is no longer applied" for drive mappings that should disappear after a role change
- proving a backup by performing a restore and checking the recovered ACL

## Lab limitations

This is a personal VMware lab, not a production design.

- There is one domain controller and one file server, so there is no server redundancy.
- Windows Server is running as an evaluation installation.
- `lab.local` is an internal lab domain.
- The backup used for the recovery test is stored on the same FS01 VM and is not scheduled or off-site.

## Evidence

The PDF contains the screenshots used to verify each ticket, including the permission tests, Group Policy configuration, mapped-drive results, user move, backup and restore, and the stale-token troubleshooting case.

**Full report:** [P2_File_Server_Access_and_Recovery_FINAL.pdf](./P2_File_Server_Access_and_Recovery_FINAL.pdf)
