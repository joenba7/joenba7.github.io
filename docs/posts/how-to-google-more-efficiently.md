---
title: How to Google more efficiently
date: 2012-05-01
authors:
    - jorge
categories:
    - 'Tutorials'
tags:
    - Linux
    - Windows
    - convert
    - efficiently
    - google
    - more
    - tips
    - tricks
    - weather
    - '672801457'
---
Most people these days use google.com as their preferred search engine, and most times a regular search will bring you the results you are looking for. But what if you want a more complex search? Or want to filter out some results? Well, here’s a few pointers on how to Google a bit more efficiently, and some other neat tricks.

A regular search
----------------

Say that I want to search for a review for the cellphone HTC Desire Z. I could always use the string “HTC Desire Z review”. Or, we could do a more efficient search.

Take the following search string as an example:

> site:engadget.com ~review “htc desire z” -amazon 2010..2012

Let’s break it down:

- **site:engadget:com** – Only searches the pages of the site engadget.com
- **~review** – Also searches related words, such as “check, examine, opinion”.
- **htc desire z** – Searches for the exact phrase, and not each word separately.
- **-amazon** – Excludes this term from the search.
- **2010..2012** – Shows all results from within the specified timerange.

The last element above not only works with a time-range, but also with any other type of range. For instance:

- **$100..$200**

..gives you results based on a range from $100 to $200.

Searching for filetypes
-----------------------

In certain cases you want to search for specific types of file. For instance,

> filetype:pdf linux intitle:best \*distribution

Again, lets break this down:

- **filetype:pdf** Searches only results of the file type you specify. .doc, .jpg, .torrent, the options are endless. Close to, anyway.
- **linux** – Just a regular search string.
- **intitle:best** – Only shows results with that word in the title (in this case, ‘best’)
- **\*distribution** – Replaces itself with common terms in your search (in this case ‘Arch Linux distribution’, ‘Red Hat distribution’, will both be searched, among many others)

Definitions
-----------

To find the definition of a word, use:

> define: exquisite

Unit converter
--------------

Converting from one unit to another made easy:

Distance

> 54 km to miles

Weight

> 2 kg to lbs

Currency

> 150 NOK to USD

Calculator
----------

Also works great as a calculator:

> 20\*(2+4)/5-1

Time
----

Check out what the time is in another part of the world:

> time Hong Kong

Weather
-------

Also handy for checking the weather:

> weather Oslo

Just a few of the things one can do with the Google search engine!
