---
title: "Awk: comparing numbers in two files"
date: "2011-01-10"
---

Given two files, each with the first column serving as a key and the second column as value; how can we obtain the percentage difference between corresponding keys in the file?

For example, if a.txt has data:

key1 10 key2 20 key3 30

While b.txt has data:

key1 5 key2 10 key3 20

We might be interested in percentage difference between the values, i.e.:

key1 50 key2 50 key3 33.3

The following awk script can achieve this, while also skipping any keys not common to both files:

awk '
BEGIN {
   while (getline < "a.txt") {arr\[$1\] = $2}
} {
   if (length(arr\[$1\])==0)
      { print FILENAME":" $0 }
   else arr2\[$1\]=$2
}
END {
for (key in arr)
   if (arr2\[key\]>0)
      { print (arr2\[key\]-arr\[key\])\*100/arr2\[key\] }
}' b.txt

To achieve the same for more than two files, we can modify the while loop in BEGIN to read the contents of all but the last file into a distinct array and then, in END, comparing the last file's current key to see if it exists in all arrays before processing the array's contents.

To find the number of columns in the last line of all files in a directory:

#!/bin/bash

for file in \*
  do
    awk -F"\\t" 'END {print FILENAME, NF}' $file
  done
#
