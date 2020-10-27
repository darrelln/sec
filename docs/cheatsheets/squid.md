---
layout: default
title: Squid Proxy
parent: CHEATSHEETS
has_children: false
has_toc: false
---

{% include toc.html %}

## References

[Squid reference](http://etutorials.org/Server+Administration/Squid.+The+definitive+guide/Chapter+14.+Monitoring+Squid/14.2+The+Cache+Manager/)

## SquidClient
Use `squidclient` to connect to the proxy. You can then access the instance's Cache Manager interface as well as cache objects:

```
// Access the Cache Manager interface (no username required) and list available options.
squidclient {-h|--host} 10.x.x.x -w S3cret mgr:menu

// List the contents of the cache, so what can be reached via the proxy.
squidclient -h 10.x.x.x -w S3cret mgr:objects

// List the domain names and network details for the proxy.
squidclient -h 10.x.x.x -w S3cret mgr:fqdncache
```

## Cache Manager Pages

See the above references for detailed description of these Cache Manager pages:

|Short name|Description|Notes|
|:---|:---|:---|
|leaks|Memory Leak Tracking||
|mem|Memory Utilization||
|cbdata|Callback Data Registry Contents||
|events|Event Queue||
|squidaio_counts|Async IO Function Counters||
|diskd|DISKD Stats||
|config|Current Squid Configuration*||
|comm_incoming|comm_incoming( ) Stats||
|ipcache|IP Cache Stats and Contents||
|**`fqdncache`**|**`FQDN Cache Stats and Contents`**|**`Fully-qualified domain names and IP addresses.`**|
|idns|Internal DNS Statistics||
|dns|Dnsserver Statistics||
|redirector|URL Redirector Stats||
|basicauthenticator|Basic User Authenticator Stats||
|digestauthenticator|Digest User Authenticator Stats||
|ntlmauthenticator|NTLM User Authenticator Stats||
|external_acl|External ACL Stats||
|http_headers|HTTP Header Statistics||
|via_headers|Via Request Headers||
|forw_headers|X-Forwarded-For Request Headers||
|**`menu`**|**`This Cache Manager Menu`**|**`>> START HERE <<`**|
|shutdown|Shut Down the Squid Process*||
|offline_toggle|Toggle offline_mode Setting*||
|**`info`**|**`General Runtime Information`**|**`General information.`**|
|filedescriptors|Process File Descriptor Allocation||
|**`objects`**|**`All Cache Objects`**|**`Shows all servable objects, so what's browsable.`**|
|vm_objects|In-Memory and In-Transit Objects||
|openfd_objects|Objects with Swapout Files Open||
|io|Server-Side Network read( ) Size Histograms||
|counters|Traffic and Resource Counters||
|peer_select|Peer Selection Algorithms||
|digest_stats|Cache Digest and ICP Blob||
|5min|5 Minute Average of Counters||
|60min|60 Minute Average of Counters||
|utilization|Cache Utilization||
|histograms|Full Histogram Counts||
|active_requests|Client-Side Active Requests||
|store_digest|Store Digest||
|storedir|Store Directory Stats||
|store_check_cachable_stats|storeCheckCachable( ) Stats||
|store_io|Store IO Interface Stats||
|pconn|Persistent Connection Utilization Histograms||
|refresh|Refresh Algorithm Statistics||
|delay|Delay Pool Levels||
|forward|Request Forwarding Statistics||
|client_list|Cache Client List||
|netdb|Network Measurement Database||
|asndb|AS Number Database||
|carp|CARP Information||
|server_list|Peer Cache Statistics||
|non_peers|List of Unknown Sites Sending ICP Messages||