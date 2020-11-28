---
layout: default
title: TMUX
parent: CHEATSHEETS
has_children: false
has_toc: false
---

{% include toc.html %}

## References

[Cheatsheet](https://tmuxcheatsheet.com)

[Cheatsheet](https://outcoldman.com/cheatsheets/tmux)

## Command Reference

|Sessions||
|:---|:---|
|$ tmux                                     |Start new session|
|$ tmux new                                 |Start new session|
|$ tmux new -s `<name>`                     |Start new session using name|
|$ tmux rename-session -t `<id>` `<name>`   |Rename existing session|
|$ tmux ls                                  |List tmux sessions|
|$ tmux attach -t `<id or name>`            |Attach to tmux session|
|$ Ctrl+b, $                                |Name session|
|$ : kill-session                           |Exit session|
|$ : rename-session -t `<current name>` `<new name>`|Rename the current session|

|Windows||
|:---|:---|
|$ Ctrl+b, c                                |Create new window|
|$ Ctrl+b, ,                                |Name window|
|$ Ctrl+b, &                                |Close current window|
|$ Ctrl+b, p                                |Move to previous window|
|$ Ctrl+b, n                                |Move to next window|
|$ Ctrl+b, `<0,1,2 etc>`                    |Switch to specific window|
|$ Ctrl+b, d                                |Detach window|
|$ Ctrl+b, %                                |Split panes vertically|
|$ Ctrl+b, “                                |Split panes horizontally|
|$ Ctrl+b, :, join-pane -s 1 -t 2          |Join window 1 with window 2|

|Panes||
|:---|:---|
|$ Ctrl+b, :                                |Enter command mode|
|$ Ctrl+b, `<arrow key>`                    |Move to next pane|
|$ Ctrl+b+ `<arrow key>`                    |Resize pane|
|$ Ctrl+b, ;                                |Toggle last active window|
|$ Ctrl+b, {                                |Move pane left|
|$ Ctrl+b, }                                |Move pane right|
|$ Ctrl+b, q                                |Show pane numbers|
|$ Ctrl+b, q <0-9>                          |Switch to pane|
|$ Ctrl+b, o                                |Move to next pane|
|$ Ctrl+b, spacebar                         |Toggle pane layouts - change between horizontal and vertical|
|$ Ctrl+b, x                                |Close pane|
|$ Ctrl+b, !                                |Convert pane to window|
|$ Ctrl+b, t                                |Show the time, esc to revert|
|$ Ctrl+b, :, x, y                          |Kill a dead pane|
|$ Ctrl+b, :, swap-pane, -U                 |Swap horizontal pane updwards|
|$ Ctrl+b, :, swap-pane, -D                 |Swap horizontal pane downwards|
|$ Ctrl+b, o                                |Swap horizontal/vertical panes (keep ctrl held down, press b and then o)|

|Search, copy, paste||
|:---|:---|
|$ Ctrl+b, [|Enter edit mode (column, line shows in top right)|
|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Ctrl+s|Search forward|
|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Ctrl+r|Search backwards|
|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;q or Esc or Enter, enter|Exit edit mode|
|$ Ctrl+b, [|Enter edit mode (column, line shows in top right)|
|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Ctrl+space|??|
|$ Ctrl+b, f, `<term>`, enter|Search for term in window titles and window output. A results pane is shown, use arrow keys to switch to selected window|

*TMUX uses Emacs key bindings by default.*

## Configuration
Add a `.tmux.conf` file to your home folder. There is an example config file which should be located here (if it's not, `locate tmux.conf`):
```
/usr/share/doc/tmux/example_tmux.conf
```

Terminal colouring:
```csharp
## Change the default $TERM to tmux-256color
set -g default-terminal "screen-256color"
```
