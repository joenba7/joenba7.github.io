---
title: Oracle Tips and Tricks
date: 2008-11-04
authors:
    - jorge
categories:
    - Linux
---
Finding out if Oracle is running on the machine (Oracle always has a db-writer process running):

```
# ps aux | grep dbw

```

Finding the name of the database-instance:

```
# ps aux | grep -v grep | grep dbw | cut -d _ -f 3

```

Logging in as sysdba:

```
$ sqlplus "/ as sysdba"

```

Finding out where Oracle places the archive-logs:

```
> show parameter log_archive_dest;

```

Show general log information:

```
> show parameter log_archive;

```

Setting the archive-log path:

```
> system set log_archive_dest='/path/to/logs/arch';

```

Finding out where Oracle places the alert-log:

```
> show parameter background_dump_dest;

```

Healthchecking Oracle (if you are able to take down the database):

```
> shutdown abort;
> startup;
> shutdown;
> startup;
> select sysdate from dual;

```

Forcing Oracle to write to the archive-logs:

```
> alter system switch logfile;

```

Starting the database if it isn’t running:

```
> alter database open;

```

