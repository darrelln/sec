---
layout: default
title: Memcached
parent: COOKBOOKS
has_children: false
has_toc: false
---

{% include toc.html %}

## References

[Get all keys from cache](https://stackoverflow.com/questions/19560150/get-all-keys-set-in-memcached)

[Read key values ](https://ma.ttias.be/read-keys-values-memcached-instance/)

[Memcache cheatsheet](https://lzone.de/cheat-sheet/memcached)

[List all keys](https://bobcares.com/blog/memcached-list-all-keys/)

## Query Keys and Values
```
telnet 127.0.0.1 11211

> stats items
STAT items:1:hits_to_warm 0
....

> stats cachedump 1 100
ITEM user [5 b; 0 s]
ITEM passwd [9 b; 0 s]
....

> get user
VALUE user 0 5
luffy

> get passwd
VALUE passwd 0 9
0n3_p1ec3

> quit
```