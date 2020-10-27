---
layout: default
title: Windows Commands
parent: COOKBOOKS
has_children: false
has_toc: false
---

{% include toc.html %}

## Querying eventvwr from terminal
Logs are in `evtx` format, and will open with Eventvwr so export them and open them on another box where you have access to Eventvwr.
```
wevtutil /h
wevtutil enum-logs
wevtutil export-log export-log System C:\Users\user-name\Download\System.evtx
wevtutil export-log export-log System \\10.10.14.12\share\System.evtx
```