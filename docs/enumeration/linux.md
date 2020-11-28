---
layout: default
title: Linux
parent: ENUMERATION
has_children: false
has_toc: false
---

{% include toc.html %}

## References

[Sudo and sudoers](https://www.hostinger.com/tutorials/sudo-and-the-sudoers-file/)

## OS details:
```
cat /etc/os-release
```

## Kernel and architecture
```
uname -a
```

Read up on `linux capabilities`

## Search for usernames inside files
```bash
// Recursive search within directories.
grep -r -l "to-find" *.php 2>/dev/null
``` 

## Capabilities
```bash
// Recursive search for files with capabilites set.
getcap -r / 2>/dev/null
```

## Sudo
### Sudo bug
Sudo implementations earlier than `1.8.28` may allow escalation to root even if the sudoers file explicitly prevents it (by using `!root`). It uses the non-existant user ID of -1 which wraps (`uint`) to be interpretted as root. As long as the user can run Sudo in some form, it may be exploitable as follows (search `sudo -u#-1` for more details):
```
sudo -u#-1 /bin/bash
```

### List sudo commands the user can run
Use `sudo -l` to check which commans the user can run.

```
// Will usually need to provide a password for sudo.
sudo -l
```
