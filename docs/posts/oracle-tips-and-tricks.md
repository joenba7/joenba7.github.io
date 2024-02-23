---
title: Oracle Tips and Tricks
date: 2008-11-04
comments: true
authors:
    - jorge
categories:
    - Linux
tags:
    - oracle
    - linux
    - cheatsheet
---

# Oracle Tips and Tricks

Finding out if Oracle is running on the machine (Oracle always has a db-writer process running):

```bash
ps aux | grep dbw

```

Finding the name of the database-instance:


```bash
ps aux | grep -v grep | grep dbw | cut -d _ -f 3

```

Logging in as sysdba:

```sql
sqlplus "/ as sysdba"

```

Finding out where Oracle places the archive-logs:

```sql
> show parameter log_archive_dest;

```

Show general log information:

```sql
> show parameter log_archive;

```

Setting the archive-log path:

```sql
> system set log_archive_dest='/path/to/logs/arch';

```

Finding out where Oracle places the alert-log:

```sql
> show parameter background_dump_dest;

```

Healthchecking Oracle (if you are able to take down the database):

```sql
> shutdown abort;
> startup;
> shutdown;
> startup;
> select sysdate from dual;

```

Forcing Oracle to write to the archive-logs:

```sql
> alter system switch logfile;

```

Starting the database if it isn’t running:

```sql
> alter database open;

```

