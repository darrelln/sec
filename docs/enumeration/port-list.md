---
layout: default
title: Port List
parent: ENUMERATION
has_children: false
has_toc: false
---

{% include toc.html %}

## References

[List of TCP and UDP port numbers](https://en.wikipedia.org/wiki/List_of_TCP_and_UDP_port_numbers)

## Port List / Common Ports

|Port #|Protocol|Description|Notes|
|:--:|:--|:--|:--|
|21|TCP|FTP||
|22|TCP|SSH||
|25|TCP|SMTP||
|53|UDP/TCP|DNS|[Enumeration](dns.md)|
|88|TCP|Kerberos|[Enumeration](kerberos.md)|
|110|TCP|Telnet||
|135|TCP|MS-RPC Endpoint Mapper||
|137|UDP|NetBIOS Name Service|[Enumeration](netbios.md)|
|138|UDP|NetBIOS Datagram Service||
|139|TCP|SMB over NetBIOS||
|161|UDP|SNMP||
|389|TCP|LDAP|[Enumeration](ldap.md)|
|443|TCP/UDP|HTTPS, UDP = QUIC / HTTP/3|
|445|TCP|SMB|[Enumeration](smb.md)|
|464|TCP|KPASSWD5 (Kerberos)||
|631|TCP/UDP|IPP (Internet Printing Protocol)|
|636|TCP|LDAPS (lDAP over SSL)||
||||
||||
||||
||||
|3128|TCP|Squid Proxy|x
|3268|TCP|LDAP Global Catalog||
|3269|TCP|LDAP Global Catalog (SSL)||
||||
||||
|3306|TCP|MySQL|
||||
||||
||||
|5985|TCP|WinRM (HTTP) - Windows Remote Management|Powershell Remoting, [Enumeration](winrm.md)|
|5986|TCP|WinRM (HTTPS) - Windows Remote Management|Powershell Remoting, [Enumeration](winrm.md)|
||||
||||
||||
|8080|TCP|Apache Tomcat|
||||
|9050|TCP|Socks 5 Proxy|
|9100|TCP|Raw printing|
|9389||Active Directory Web Service||
||||
||||
||||
|47001||WinRM Local Requests||
||||
|11211|TCP|Memcached||
||||
|29820|TCP|Sirep Service (Windows IoT Core)|
||||
