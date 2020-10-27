---
layout: default
title: DNS
parent: ENUMERATION
has_children: false
has_toc: false
---

{% include toc.html %}

## Nameserver and domain name leaks
```
nslookup server 10.x.x.x            // Nameserver information and hopefully domain details
nslookup server domain.name         // Details about the domain, good way of checking if it exists
```

## Zone transfers
If 53/TCP is open, try a zone transfer.
```
dig axfr @10.x.x.x domain.name

dnsrecon -d <domain.name> -n <(NS IP) 10.x.x.x> -a

fierce -dns egotistical-bank.local
```

## Sub-domain scanning
```
dnsmap egotistical-bank.local -w /usr/share/seclists/Discovery/DNS/fierce-hostlist.txt d -5

fierce -dns egotistical-bank.local -wordlist /usr/share/seclists/Discovery/DNS/fierce-hostlist.txt
```