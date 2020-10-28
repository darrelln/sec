---
layout: default
title: Pwn Tools
parent: TOOLS
has_children: false
has_toc: true
---

{% include toc.html %}

## About
Pwn Tools is a CTF framework written in Python. It's available on [GitHub here](https://github.com/Gallopsled/pwntools), has a [tutorial here](https://github.com/Gallopsled/pwntools-tutorial/blob/master/tubes.md) and [documentation here](https://pwntools.readthedocs.io/en/latest/tubes.html).

## Installation
Install using `python3 pip`:
```python
// Install or update pip.
python3 -m pip install --upgrade pip

// Install or update pwntools.
python3 -m pip install --upgrade pwntools

// To test installation.
python3 -c 'from pwn import *'
```