# Root-Cause-Lockout-Investigation


## Overview

In this lab, I investigated the root cause of a repeated account lockout for user Emily Garcia on a Windows Server 2019 domain controller (Houtech). Rather than just unlocking the account, this involved digging into the Security event log in Event Viewer to find the specific lockout event, identifying the source computer that triggered it, unlocking the account via PowerShell, confirming the fix programmatically, and verifying the user could sign in successfully afterward.

## Objectives


- Reproduce and confirm the reported lockout error on the client
- Open Event Viewer on the domain controller and navigate to the Security log
- Filter the Security log for Event ID 4740 (account lockout events)
- Identify the specific lockout event and review its details
- Determine which account was locked out and which computer triggered the lockout
- Unlock the account using PowerShell rather than the GUI
- Confirm programmatically that the account's LockedOut status was cleared
- Verify the user could sign in successfully after the fix


# Descriptions

1 — Confirming the reported error

On the Windows 11 client, the sign-in screen for Emily Garcia displayed "The referenced account is currently locked out and may not be logged on to," confirming the reported lockout before beginning any investigation on the server side.

![image alt]()

2 — Opening Event Viewer

On the Houtech server, opened the Tools menu in Server Manager and selected Event Viewer, to begin investigating the Security log for the root cause of the lockout rather than just clearing it.

![image alt]()

3 — Event Viewer overview screen

Event Viewer opened to the Overview and Summary screen for the local computer, showing the Custom Views, Windows Logs, Applications and Services Logs, and Subscriptions nodes available to drill into.

![image alt]()

4 — Reviewing the Security log

Selected the Security log under Windows Logs, showing over 23,000 events including numerous Logon/Logoff events (Event ID 4634), far too many to scan manually without filtering.

![image alt]()

5 — Filtering for lockout events

Opened Filter Current Log and entered Event ID 4740 (the event ID specifically logged for account lockouts) to narrow down the massive Security log to only the relevant lockout events.

![image alt]()

6 — Filtered results showing lockout events

The filtered Security log now showed only 7 events, all Event ID 4740 under the User Account Management task category, isolating the exact account lockout occurrences.

![image alt]()

7 — Reviewing the lockout event details

Opened the Event Properties for the most recent Event 4740 (Microsoft Windows security auditing), showing "A user account was locked out" along with Subject details including Security ID: SYSTEM and Account Domain: HOUTECH300.

![image alt]()

8 — Identifying the locked-out account and source

Scrolled further into the same event details and found "Account That Was Locked Out: HOUTECH300\egarcia" along with "Caller Computer Name: WINDOWS11," identifying both the affected account and the specific machine that triggered the lockout.

![image alt]()

9 — Unlocking the account via PowerShell

Opened an elevated PowerShell prompt and ran Unlock-ADAccount -Identity egarcia, unlocking Emily Garcia's account directly from the command line instead of using the Active Directory Users and Computers GUI.

![image alt]()

10 — Confirming the unlock programmatically

Ran Get-ADUser egarcia -Properties LockedOut | Format-List, which returned LockedOut : False along with the account's full distinguished name and other attributes, confirming the lockout flag had been successfully cleared.

![image alt]()

11 — Testing sign-in on the client

Back on the Windows 11 client, signed in as Emily Garcia with her password to confirm from the end-user side that the account was no longer locked out.

![image alt]()

12 — Confirming successful sign-in

Confirmed Emily Garcia was fully signed in, with the Start menu showing her account (egarcia@Houtech.local) and a normal desktop session, verifying the account was fully accessible again after the fix.

![image alt]()

