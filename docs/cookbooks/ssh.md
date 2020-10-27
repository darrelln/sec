---
layout: default
title: SSH
parent: COOKBOOKS
has_children: false
has_toc: false
---

{% include toc.html %}

## Creating keys
```
ssh-keygen -t rsa -b 4096 -f ssh_key
chmod 600 ssh_key*
```

## When you don't have a password
You may be able to generate keys and login. First generate keys as shown above. Then:
```
// First, generate keys using ssh-keygen and then copy the PUBLIC key to the target.
// If you have access to the users `.ssh` folder, an alternative approach to above is to add the key to `known_hosts`.
ssh-copy-id -i ssh_key.pub user@10.x.x.x

// Use the PRIVATE key to login, and without a password.
ssh -i ssh_key user@10.x.x.x
```

## SSH with password as a parameter
First, install `sshpass` using `sudo apt install sshpass`.

```
sshpass -p <password> ssh <user>@<host>
```

## Port forwarding / port tunnelling
Port forward remote port `5555` to `127.0.0.1:10443`:
```
sudo -L 10443:127.0.0.1:5555 user@10.x.x.x
```