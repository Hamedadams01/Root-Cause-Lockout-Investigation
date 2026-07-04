# Root-Cause-Lockout-Investigation

# Overview
This project goes beyond simply unlocking a locked account — it walks through using Windows Event Viewer to identify the root cause of a lockout before resolving it, giving a fuller picture of why an account locked out rather than just fixing the symptom.
# Objectives

- Confirm the reported lockout symptom on the client side
- Use Event Viewer to investigate the root cause of the lockout
- Identify the specific account and source of the failed login attempts
- Unlock the account using PowerShell
- Verify the account status and confirm the user can log in successfully

# Step-by-Step

- I signed in as the affected user (Emily Garcia) on the Windows 11 client and confirmed the exact error: "The referenced account is currently locked out and may not be logged on to."
- I opened Server Manager on the Houtech server and went to Tools > Event Viewer to begin investigating the cause rather than just unlocking the account outright.
- I expanded Windows Logs and selected Security, which showed a large volume of events (23,101) to sift through.
- I opened Filter Current Log and entered Event ID 4740 (the specific event ID for account lockouts) to narrow down the results.
- I reviewed the filtered results, which returned 7 matching lockout events under the "User Account Management" task category.
- I opened the first Event 4740 entry and confirmed the description: "A user account was locked out," with the Logon ID and computer details listed under Subject.
- I scrolled to the Details tab of the event and identified the account that was locked out: HOUTECH300\egarcia, with the Caller Computer Name listed as WINDOWS11 — confirming the lockout originated from Emily's own workstation, likely due to repeated failed login attempts or a cached credential issue.
- I opened PowerShell as Administrator on the server and ran Unlock-ADAccount -Identity egarcia to unlock the account directly from the command line.
- I ran Get-ADUser egarcia -Properties LockedOut | Format-List to verify the account status, confirming LockedOut: False and reviewing her full AD attributes (Distinguished Name, SID, UserPrincipalName, etc.).
- I went back to the Windows 11 client and signed in as Emily Garcia using her existing password to confirm the fix worked from the end-user side.
- I confirmed she was fully signed in, with the Start menu showing her account (egarcia@Houtech.local) and a normal desktop session — closing out the investigation.

# Conclusion

- The lockout was diagnosed using Event Viewer rather than resolved blindly, identifying Event ID 4740 as the specific lockout trigger
- The source computer (WINDOWS11) was identified as the origin of the failed login attempts, pointing to a likely cause on the user's own machine
- The account was unlocked via PowerShell (Unlock-ADAccount) and verified programmatically before closing the ticket
- This root-cause approach demonstrates a more thorough troubleshooting methodology than a simple unlock, useful for identifying recurring lockout patterns
