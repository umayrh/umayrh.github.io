---
layout: post
title: "A Terrible Summary - The Risks of Reporting on Research"
date: "2015-07-23"
description:
image: /assets/images/econom-learning-gray.png
author: Umayr Hassan
tags:
- Computational economics
- Econometrics
- Mathematics
---

Two reasons why I think that [A Beautiful Algorithm? The Risks of Automating Online Transactions](http://www.news.virginia.edu/content/beautiful-algorithm-risks-automating-online-transactions) 
is way off in its report on an otherwise interesting paper [Econometrics for Learning Agents](http://arxiv.org/pdf/1505.00720.pdf).

![Ratio distribution for one account](/assets/images/econom-learning-gray.png)

1. The paper is concerned with generalized second-price auction, which is common in e-commerce, online advertising in 
particular. The aim is to estimate how inefficient such auctions are by accurately inferring play valuation (how much 
would an agent bid for an item, e.g.) with minimally restrictive assumptions on agent behavior. So, it doesn't quite 
address "risks of automating online transactions."
2. Without quoting verbatim, the report says that, according one of the paper's authors, "the algorithms behind such 
ads could lead to an unforeseen financial crash." At least the paper itself doesn't use the terms "crash" or "financial". 
"Risk-averse bidders" is the only time the paper mentions risk, and that too in 'Further Related Work."

An advertising platform like Bing might be losing money because bidders are not bidding truthfully, i.e. according 
to their valuation of an item. Since the valuation is private, it's impossible to know for sure. On the other hand, 
since the valuation is constrained by a multi-agent environment with repeated games (i.e. auctions) - hence an 
opportunity to learn - it should be possible to infer valuation under some assumptions on agent/player behavior.

A previous paper by the first author, [Denis Nekipelov](http://people.virginia.edu/~dn4w/), discussed player 
valuation under the assumption of a static Nash equilibrium where all player respond independently and with their 
best strategies. That the auctions are already and/or constantly in equilibrium is a strong - if popular - assumption 
in dynamic environments like online advertising. The current paper instead assumes that player learn over time 
using "no-regret strategies" (i.e. responses that _over time_ are close to optimal), and then sets out to estimate 
the number of data samples required to approximate the _rationalizable set_ "consisting of the set of values 
and error parameters (v, ε) such that with value v the sequence of bid by the player would have at most ε regret."
