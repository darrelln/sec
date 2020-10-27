---
layout: default
title: Powershell
parent: CHEATSHEETS
has_children: false
has_toc: false
---

{% include toc.html %}

## References

[Dumping lsass](https://medium.com/@markmotig/some-ways-to-dump-lsass-exe-c4a75fdc49bf)

[Powershell general](https://docs.microsoft.com/en-us/powershell/module/?view=win10-ps)

[Add admin](https://docs.microsoft.com/en-us/powershell/module/addsadministration/?view=win10-ps)

## Commands

|Command|Description|
|:---|:---|
|`$PSVersionTable.PSVersion`|Print PowerShell version|
|`$env:computername`|Print hostname|
|`Get-ExecutionPolicy`|Print execution policy in force|
|`Get-HotFix`|Print details of installed patches|
|`Get-HotFix | Where-Object HotfixID -eq "KB3186568"`|Query specific hostfix ID|
|`Get-ChildItem`|Directory listing, equivalent to `dir`|
|`Get-ChildItem -Force`|Show hidden files in listing, equivalent to `dir /a`|
|`Get-Command -Name systeminfo`|Print details about the provided command|
|`Get-TimeZone`|Print time zone information|
|`Get-Content` "C:\File.txt"|Print file contents|
|`Get-Date -Format 'MM-dd-yyyy_hh-mm-ss'`|Print date with supplied format|
|`Get-NetUser`|Equivalent of `net user`|
|`Get-ACL`|Equivalent of `icacls`|
|`Get-ADObject`|Query AD|
|`Start-Sleep -Seconds 10`|Sleep for 10 seconds|
|`.\file.exe`|Execute `file.exe`|
|`[System.Environment]::Is64BitOperatingSystem`|Prints `true` if x64 Windows|
|`[System.IntPtr]::Size`|8 = 64 bit, 4 = 32 bits|
|`[System.Environment]::OSVersion`|Prints platform, service pack and version details|

## Run as a different user
```powershell
PS C:\Users\FSmith\Documents> $user = "svc_loanmanager"
PS C:\Users\FSmith\Documents> $pass = "Moneymakestheworldgoround!"
PS C:\Users\FSmith\Documents> $creds = New-Object System.Management.Automation.PSCredential -ArgumentList @($user,(ConvertTo-SecureString -String $pass -AsPlainText -Force))
PS C:\Users\FSmith\Documents> Start-Process Seatbelt.exe -Credential ($creds)
```

## Check if Credential Guard is Running
```powershell
$DevGuard = Get-CimInstance –ClassName Win32_DeviceGuard –Namespace root\Microsoft\Windows\DeviceGuard; if ($DevGuard.SecurityServicesConfigured -contains 1) {"Credential Guard configured"}; if ($DevGuard.SecurityServicesRunning -contains 1) {"Credential Guard running"}
```