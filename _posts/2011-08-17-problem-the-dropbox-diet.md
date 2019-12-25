---
title: "Problem: The Dropbox Diet"
date: "2011-08-17"
---

Dropbox posed the following problem on their [website](http://www.dropbox.com/jobs/challenges):

> Of the boatload of perks Dropbox offers, the ones most threatening to our engineers' waistlines are the daily lunches, the fully-stocked drink fridge, and a full-length bar covered with every snack you could want. All of those calories add up. Luckily, the office is also well-equipped with ping-pong, a DDR machine, and a subsidized gym right across the street that can burn those calories right back off. Although we often don't, Dropboxers should choose the food they eat to counterbalance the activities they perform so that they don't end up with caloric deficit or excess.
> 
> Help us keep our caloric intake in check. You'll be given a list of activities and their caloric impact. Write a program that outputs the names of activities a Dropboxer should choose to partake in so that the sum of their caloric impact is zero. Once an activity is selected, it cannot be chosen again.

##### Sample Input

2
red-bull 140
coke 110

##### Sample Output

no solution

12
free-lunch 802
mixed-nuts 421
orange-juice 143
heavy-ddr-session -302
cheese-snacks 137
cookies 316
mexican-coke 150
dropballers-basketball -611
coding-six-hours -466
riding-scooter -42
rock-band -195
playing-drums -295

coding-six-hours
cookies
mexican-coke

Here is a linear program in Octave that solves it:

c = \[1 1 1 1 1 1 1 1 1 1 1 1\];
a = \[802 421 143 -302 137 316 150 -611 -466 -42 -195 -295\];
b = \[0\]';
lb = \[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0\];
ub = \[1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1\];
ctype = "S";
vartype = "IIIIIIIIIIII";
sense = -1; % maximize

param.msglev = 3;
param.itlim = -1;

\[xmin, fmin, status, extra\] = glpk (c, a, b, lb, ub, ctype, vartype, sense, param)

% see also:
% \[1\] http://bs2.tugraz.at/irt-stud/EOS/glpkmex/glpkmex.m
% \[2\] http://mosaic.cnfolio.com/B324CW2010A105
