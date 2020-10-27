---
layout: default
title: MySql
parent: CHEATSHEETS
has_children: false
has_toc: false
---

{% include toc.html %}

## Start MySql Service
```bash
// Check current status of the service.
service mysql status
systemctl status mysql

// Start the service.
service mysql start
systemctl start mysql
```

## MySql Client
### Execute shell commands from query
```
// Execute SQL query from the command line.
mysql -u username -ps3cret -D database -e 'select * from users;'

// Execute system commands via SQL query.
mysql -u username -ps3cret -D database -e 'system pwd'
```