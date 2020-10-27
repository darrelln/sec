---
layout: default
title: Port Tunnelling
parent: COOKBOOKS
has_children: false
has_toc: false
---

{% include toc.html %}

## SSH
If you have `SSH` accession, you can use:
```
// Tunnel local port 8888 to remote port 4506 via SSH.
ssh -L 8888:localhost:4506 user@{ip | domain}
```

## Socat
If you can get a copy of `socat` onto the machine you can use:
```
// Expose port 8888 and tunnel any data received on that port to internal port 4506.
socat TCP-LISTEN:8888,fork,reuseaddr TCP:127.0.0.1:4506
```
