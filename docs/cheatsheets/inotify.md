---
layout: default
title: Inotify
parent: CHEATSHEETS
has_children: false
has_toc: false
---

{% include toc.html %}

## References

https://www.linuxjournal.com/content/linux-filesystem-events-inotify

## Installation
```
sudo apt install inotify-tools
```

## Monitor file
```
// -m: monitor
inotifywait -m file.txt

// Output
Setting up watches.
Watches established.
```

Open or read the file to trigger events:
```
cat file.txt

// Output
flag.txt OPEN
flag.txt ACCESS
flag.txt CLOSE_NOWRITE,CLOSE
```