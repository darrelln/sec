---
layout: default
title: Seatbelt
parent: COOKBOOKS
has_children: false
has_toc: false
---

{% include toc.html %}

## AV
WinDefender will flag `Seatbelt.exe` and delete it, even when building with VisualStudio.

## Build
Check the .Net version supported on the target and build from source against that verstion.

## Examples
Running the `.exe` writes output when run in Powershell, so if no output check the .Net version.
```powershell
PS > .\Seatbelt.exe -group=all -outputfile=".\out.txt"
```