---
layout: default
title: Bash
parent: CHEATSHEETS
has_children: false
has_toc: false
---

{% include toc.html %}

## References

[Bash manual (gnu.org)](https://www.gnu.org/savannah-checkouts/gnu/bash/manual/bash.html)

[Bash manual (University of Washington)](https://courses.cs.washington.edu/courses/cse390a/14au/bash.html)

## Bash Key Bindings
### Binding

|Command|Description|
|:---|:---|
|Ctrl+L|Clear screen|
|Ctrl+R|Search command buffer|
|Tab|List available options|
|Tab+Tab|Tab completion|
|Ctrl+C|Kill currently running command|
|Ctrl+Z|Background current process, use fg to bring back to foreground|
|Ctrl+D|Exit current terminal session|
|Ctrl+A|Go to beginning of line|
|Ctrl+E|Go to end of line|
|Alt+F|Move forward by one word (by one white space)|
|Alt+B|Move back by one word (by one white space)|
|Ctrl+K|Kill to end of line (think cut)|
|Ctrl+U|Kill to start of line|
|Ctrl+W|Kill single word backwards|
|Ctrl+Y|Yank text back (think `paste`)|

### Bash Bang Operators

|Command|Description|
|:---|:---|
|`!!`|Repeat previous command, example `sudo !!` prefixes previous command with sudo |
|`!*`|All arguments from previous command|
|`!^`|First argument from previous command|
|`!$`|Last argument from previous command|
|`!-1`|Execute previous command (equivalent to `!!`)|
|`!-n`|Execute the n previous command, example `!-3`|
|`!string`|Execute the last command starting with string, example `!ls`|
|`!:n`|Use argument at position n from previous command, example `!:2`|

### Bash Miscellaneous

|Command|Description|
|:---|:---|
|`${IFS}`|Seperator, usually `space`, can be used to insert spaces into a command where they would normally be removed|
|`$(command)`|Use output of command as argument|
|`echo he{lp,llo,ro}`|Prints `help hello hero`|
|`#`|Comment to end of line|

### Command Reference

|Command|Description|
|:---|:---|
|`history`|List command history|
|`fg`|Bring last backgrounded task to foreground|
|`rdate -r 10.x.x.x`|Sync time with remote machine (think domain controller) using SNTP|
|`sudo date --set "Tuesday, 9th June, 2020 10:59:14 AM"`|Set date/time to that specified|
|`ps -p $$`|Show which shell you are using|
|`echo $SHELL`|Print shell defined in profile|

### Path
To update the path permanently, edit `~/.profile`:
```
// Edit the .profile file add the path.
$ nano ~/.profile

// Update with the new path items.
export GOPATH=$HOME/go
export PATH=$PATH:/usr/local/go/bin:$GOPATH/bin
export PATH=$PATH:/opt/dirsearch

// Reload user profile.
$ source ~/.profile
```

