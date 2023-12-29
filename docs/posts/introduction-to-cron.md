---
title: Introduction to cron
date: 2015-03-06
authors:
    - jorge
categories:
    - Linux
tags:
    - cron
    - crontab
    - linux
---
**cron** is a time-based job scheduler. This piece of software utility is used when you need to have a program run repeatedly at set time.

The daemon that runs in the background is named **crond**, while the file that contains the jobs themselves is named **crontab**.

You have three main directories:

- **/etc/crontab** is the main system **crontab** file.
- **/var/spool/cron/** a directory for storing **crontabs** defined by users.
- **/etc/cron.d/** a directory for storing system **crontabs**.

Creating
--------

Creating a **crontab**-file for your user is easy. Usage depends on what you need to do.

### Edit

```
crontab -e
```

This command will allow you to edit the current user’s **crontab**. If a **crontab** doesn’t already exist, it will create a new one once you have saved your changes.

### Edit another user’s crontab

```
crontab -u jorge -e
```

You can also edit another user’s **crontab**, but only if you have the correct permissions in place to do so.

### List

```
crontab -l
```

or

```
crontab -u jorge -l
```

..to list another user’s **crontab**.

These are the most common usages of the command **crontab**. For a couple of more options, see **man crontab**.

Syntax
------

The syntax of a **crontab**-file looks like this:

```
* * * * * /path/to/program
```

Each asterisk represents a field. Starting from left to right, the fields are as follows:

- Minute (0 – 59)
- Hour (0 – 23)
- Day of month (1 – 31)
- Month (1 – 12)
- Day of week (0 – 7, where 0 and 7 is Sunday)

On the fields **Month** and **Day of the week**, you can also use names instead of numeric values.

Operators
---------

There are various operators that you can use with **cron**. Here’s the list:

### Asterisk (\*)

An asterisk (\*) is used to indicate that every instance (i.e. every hour, every weekday, etc.) of the particular time period will be used. So if you use an asterisk in the field **Hour**, it means that it will run every hour.

### Comma (,)

With this operator you can specify a list of values. If you use **1,5** in the field **Day of week**, this would mean Monday **and** Friday.

### Dash (-)

While comma specifies a list of values, dash specifies a range. **1-5** in the field **Day of week** would mean Monday **to** Friday.

### Forward slash (/)

Forward slash specifies a step value. If you’d like to execute a command every other hour, you could use **0-23/**. You can also use steps after an asterisk, so if you want the command to run every two hours instead, use **\*/2**.

Examples
--------

### First

```
45 14 1 * * /home/jorge/backup.sh
```

In this example the script **backup.sh** runs at 14:45, the first day of the month, every month.

### Second

```
30 04 1,15 * 5 /home/jorge/list_files.sh
```

This line runs the command **list\_files.sh** at 04:30 on the 1st and 15th of each month, plus every Friday.

### Third

```
00 08 * * 1-5 /home/jorge/wake_up.sh
```

The line above runs the command **wake\_up.sh** at 08:00, Monday **to** Friday.

### Fourth

```
*/5 * * * * * /home/jorge/annoy_wife.sh
```

..and this line would run the program **annoy\_wife.sh** every five minutes, every day.

As always, if you want to know more about **cron**, check out the command **man cron**.

Have fun!
