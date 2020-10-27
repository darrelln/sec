---
layout: default
title: Linux Command
parent: COOKBOOKS
has_children: false
has_toc: false
---

{% include toc.html %}

## Adding Kali Repos to Ubuntu
```
cd /etc/apt
sudo cp sources.list sources.list.original

sudo tee -a /etc/apt/sources.list<<EOF
deb https://http.kali.org/kali kali-rolling main non-free contrib
deb-src https://http.kali.org/kali kali-rolling main non-free contrib
EOF

sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys ED444FF07D8D0BF6
sudo apt update
```
