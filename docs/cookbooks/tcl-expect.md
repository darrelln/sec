---
layout: default
title: TCL Expect
parent: COOKBOOKS
has_children: false
has_toc: false
---

{% include toc.html %}

## References

`man expect`

[Expect man page](https://wiki.tcl-lang.org/page/Expect)

[Expect examples](https://wiki.tcl-lang.org/page/Expect+Examples)

[Expect if command](https://www.tcl.tk/man/tcl8.5/TclCmd/if.htm)

## Example expect script
`Expect` scripts are written in `TCL`, not `bash`, so the syntax is completely different.
```bash
#!/usr/bin/env expect
# ./changesmbpwd.exp username password

set USER [lindex $argv 0]
set PASS [lindex $argv 1]

if { $USER != "" && $PASS != "" } {
        spawn /usr/bin/smbpasswd -r 10.10.10.193 -U fabricorp.local/$USER

        interact -o -nobuffer -re "Old SMB password:" return
        send "Fabricorp01\n"

        interact -o -nobuffer -re "New SMB password:" return
        send "$PASS\n"

        interact -o -nobuffer -re "Retype new SMB password:" return
        send "$PASS\n"

        interact
} else {
  puts "Usage: ./changesmbpwd.exp user password"
}
```