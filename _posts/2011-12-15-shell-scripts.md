---
layout: post
title: "Shell scripts"
date: "2011-12-15"
description:
image: 
author: Umayr Hassan
tags:
---

1. Sorting du output by human-readable

`du | sort -nr | cut -f2- | xargs du -hs`

2. Listing number of tcp connections to servers

`netstat -a | grep mysql | sort +3 -4 | tr -s ' ' | cut -d' ' -f5 | cut -d: -f1 | uniq -c`

3. Regex match IP address

`egrep -o "[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+:[0-9]+" /tmp/temp.txt`

4. Sed for pattern matching - match any starting with "2011" and ending with "INFO | "

`cat adproc-diag.log | grep ":postprocess:" | sed "s/2011.\*INFO | //" | grep ":postprocess:SamplingHandler" | awk -F: '{print $1,$4}' > postprocess.sampling.log`

5. Find all directories inside a directory

`find /market\_data/\* -type d`

6. Finding DB sizes

`SELECT TABLE\_SCHEMA AS 'database', TABLE\_NAME AS 'tables', CONCAT(ROUND(((DATA\_LENGTH + INDEX\_LENGTH - DATA\_FREE) / 1024 / 1024),2)," MB") AS Size FROM INFORMATION\_SCHEMA.TABLES where TABLE\_SCHEMA like '%bonus\_reporting%';`

7. Calculate various percentiles given a file containing one numeric observation per line [1]:

`cat data.log | sort -n | awk 'BEGIN {count=0} {arr[count]=$1;count++;} END{p[0]=0.5;p[1]=0.75;p[2]=0.9;p[3]=0.95;p[4]=0.99; for(i=0;i<=4;i++){v=int((p[i]\*count)+0.5);print (p[i]\*100) "th percentile: " arr[v] "\\n"}}'`

8. Skip downloading existing files

`rsync -v --ignore-existing -n /source/ username@remote\_machine:/dest/``

References

* [1] http://www.novell.com/communities/node/8706/check-mysql-database-size-using-sql-query
