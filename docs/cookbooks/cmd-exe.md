---
layout: default
title: Cmd.exe
parent: COOKBOOKS
has_children: false
has_toc: false
---

{% include toc.html %}

## Create an empty file:
```
type nul > file.ext
```

## Write text to a file overwriting existing contents:
```
echo "hello" > file.ext
```

## Append text to a file:
```
echo "hello" >> file.ext
```

## Hide/unhide file
```
attrib +h <file.ext>
attrib -h <file.ext>
```

## Redirect errors to null
```
cmd.exe /c command 2>nul
```

## Ping a machine
```
ping /n 1 x.x.x.x
```

## Recursively search current folder and sub-folders for any .vbs files:
```
dir *.vbs /s /a                                     // Current and child directories
dir C:\ /s /a /b | findstr /i ".vbs"                // Root and all child directories
```

## Recursive search for objects modified by date
Search for objects modified since or equal to the specified date or days (`{+MM/DD/YYYY | +n]`) or modified before or equal to the specified date or days (`{-MM/DD/YYYY | -n}`) and output in a parsable format. See `forfiles /?` for examples.
```
forfiles /p C:\ /s /d +MM/DD/YYYY /c "cmd.exe /c echo [path=@path][last-modified=@fdate @ftime][is-dir=@isdir]" 2>nul
```

## Findstr
```
findstr /i "search term"                            // Case-insensitive seach
findstr /v "word"                                   // Return matches that do not contain "word"
```

## Execute a .cmd or .bat file:
```
cmd.exe /c file.bat
```

## Users
```
whoami
whoami /groups                                      // Groups the user belongs to
whoami /priv                                        // Privileges granted to the user
whoami /all                                         // Everything, includes groups and privileges

net user                                            // Names of users on the box
net user <username>                                 // Details about specific user
net user <username> <password> /add                 // Create a new user
net localgroup Administrators <username> /add       // Add the user to the Administrators group
net localgroup Administrators <username> /delete    // Delete the user from the group
```

## Service enumeration and management
```
net start

sc query
sc query > \\10.10.14.12\share\services
sc start <service name>
sc stop <service name>

sc create "Service Name" binpath=C:\Path\to\service.exe displayname="My Service" start=auto

wmic service get name,pathname,startname,displayname,startmode
wmic service get name,pathname,startname,displayname,startmode | findstr /i /v "c:\windows\\"
```

## Directory permissions
```
icacls "C:\path\to\folder\"                             // Show granted permissions by group
icacls "C:\path\to\folder\" /grant "BUILTIN\Users":W    // Group write permissions to group on folder
```

## Scheduled tasks
```
schtasks                                            // List all scheduled tasks
schtasks /query                                     // List all scheduled tasks
schtasks /query /v /fo list                         // List all scheduled tasks, verbose, format output as list
schtasks /{run | create | delete | query | change | showsid}
schtasks /showsid /?                                // Option specific help
```

## Netstat
```
netstat -anoq                                       // Print open/listening ports, if using unprivileged account
netstat -anoqb                                      // Print open/listening ports, if using privileged account
netstat -r                                          // Print routing table
```

## Tasklist
```
tasklist /v                                         // Print running processes in table format
tasklist /v /fo list                                // Print running processes in list format
tasklist /svc /fo list                              // Print service informatiom in list format
```

## Downloading files
A `wget` alternative to download a file (`-f`):
```
certutil -urlcache -f http://10.x.x.x:port/file.ext outfile.exe
```