---
layout: post
title: "Perl: split tricks"
date: "2011-01-14"
description:
image: 
author: Umayr Hassan
tags:
---

Why does the following Perl script give an incorrect field count?

```perl
#!/bin/perl

use strict; 
use warnings; 
use Data::Dumper;

my $str = "a\\t\\t\\tb\\t\\t\\t\\t6\\t\\t";
my @fields = split( /\\t/, $str); my $n = 0; 
print Dumper @fields; while ($fields[$n]) { print "$n: $fields[$n]\\n"; $n++; } 
print "Field count: " . scalar(@fields) . "\\n"; print "done!\\n";
```
Simply because split() function discards trailing null characters when splitting argument string. There are two 
ways to avoid this:

1. Force the split() function to map the results to a predefined number of fields, e.g.

`(split( /\\t/, $str))[0..8];`

2. Append the string to be parsed with a non-delimiter character, e.g. newline, and then remove it:

`str = "$str" . "\\n"; my @array = split( /\\t/, $str); splice(@array, $#array, 1)`
