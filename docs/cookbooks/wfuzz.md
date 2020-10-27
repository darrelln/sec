---
layout: default
title: WFuzz
parent: COOKBOOKS
has_children: false
has_toc: false
---

{% include toc.html %}

## Fuzzing host names
```
wfuzz --hh 8193 -H "Host: FUZZ.htb" -u http://10.10.10.188/ --hc 400 -w wordlists/host-names
```