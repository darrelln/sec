---
layout: default
title: Webapps
parent: ENUMERATION
has_children: false
has_toc: false
---

{% include toc.html %}

## Discovery
Try multiple wordlists, don't rely on any single one.

### Extensions
```
csv,xls,xslx,bin,bat,doc,docx,pdf,cmd,xml,txt,py,cgi,pl,php,html,htm,shtm,shtml,asp,aspx,jsp,jspx,java,zip,7z,tar,tgz,rar,conf,cfg,c,cpp,h,ini,sql
```

### Gobuster
```powershell
// Directory and file discovery.
gobuster dir -u http://10.10.10.191/ -w /usr/share/wordlists/dirb/common.txt -x txt,py,cgi,pl,php,html,htm,shtm,shtml,asp,aspx,jsp,java

// Write results to file, status code whitelist, status code blacklist
gobuster dir -u http://10.10.10.191/ -o results.out -s 200,301,302 -b 403 -w /usr/share/wordlists/dirb/common.txt -x txt,py,cgi,pl,php,html,htm,shtm,shtml,asp,aspx,jsp,java
```

### Dirsearch
```powershell
```

### Dirb
```powershell
// Simple.
dirb http://10.10.10.191/ /usr/share/wordlists/dirb/common.txt -X txt,py,cgi,pl,php,html,htm,shtm,shtml,asp,aspx,jsp,java
```
