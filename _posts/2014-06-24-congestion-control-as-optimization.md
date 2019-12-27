---
layout: post
title: "Congestion control as optimization, 1"
date: "2014-06-24"
description:
image: /assets/images/ss-ca-phases.png
author: Umayr Hassan
tags:
- Optimization
- Algorithms
- Distributed systems
---

## Why transmission control?

The Internet of 1986 underwent a congestion collapse as available bandwidth dropped by factor of 1000 (1). 
Transmission Control Protocol (TCP) was then enhanced with various algorithms, two in particular, to ensure that the 
network reached and stayed close to equilibrium. The algorithms were called "Slow-start" and "Congestion avoidance." 
TCP, with these enhancements, has kept the Internet afloat, and spurred much research on the design and analysis of 
network protocols. Although the initial justifications for these algorithms were control-theoretic - concerned with 
stability, and, to some extent, efficiency and fairness of allocation - it was only much later that more sophisticated 
mathematical models for congestion control were developed. In this note, we will focus on the connections between 
optimization and congestion control. The aim is to try and apply concepts and techniques from congestion control to 
computational ad serving, especially distributed ad optimization.

After a brief overview of TCP, we'll discuss four topics in congestion control:

- Increase/decrease algorithms, and linear control systems (2)
- Online search, and competitive analysis (3)
- Network optimization, and primal-dual algorithms (4)
- Network games, and non-cooperative equilibria (5)

* (1) V. Jacobson, M. Karels. Congestion Control and Avoidance. Proceedings of SIGCOMM, 1988. 
* (2) D. Chiu, R. Jain. Analysis of the increase and decrease algorithms for congestion avoidance in computer networks. Computer Networks and ISDN Systems, 1989. 
* (3) R. Karp, E. Koutsoupias, C. Papadimitriou, S. Shenker.  Optimization Problems in Congestion Control. Proceedings of FOCS, 2000. 
* (4) F. P. Kelly, A. Maulloo, D. Tan. Rate control in communication networks: shadow prices, proportional fairness and stability. Journal of the Operations Research Society, 1998. 
* (5) C. Papadimitriou. Algorithms, Games and the Internet. Proceedings of STOC, 2001.

## TCP congestion control: overview

Two variables are essential for congestion control: the congestion window (CWND) and round-trip time (RTT) (6). 
CWND controls the number of packets transmitted at a given point in time. RTT indicates the time it takes to 
receive acknowledgement for a packet. Thus the ratio of CWND to RTT indicates the sender's instantaneous sending rate. 
Another parameter, receiver window (RWND), indicates the number of packets that the receiver is willing to receive. Hence 
the sender needs to send the minimum of RWND and CWND. We'll assume that the receiver bandwidth is not restrictive and 
hence RWND >> CWND.

RTT is assumed to be a property of the network, and can be decomposed into a fixed component (propagation delay) and a 
variable component (queuing delay). It is used primarily to estimate the timeout (RTO) span after which an unacknowledged 
packet is considered lost, which in turn is interpreted as a binary congestion signal. Although, in some cases (TCP Vegas, 
FAST TCP e.g.), it is also used directly as  multi-bit congestion signal since queuing delays are usually due to congestion.

CWND itself is determined or limited by various other parameters such as initial window (IW), slow-start threshold 
(SSTHRESH). Initial window sets the initial size of the CWND, while SSTHRESH is the maximum value of CWND beyond which 
TCP enters "congestion avoidance" phase.

There are two congestion control phases in TCP: slow-start and congestion avoidance. The aim of slow-start is to ramp up 
the sending rate so that the algorithm reaches close to equilibrium value of available bandwidth as soon as possible. 
Congestion avoidance, on the other hand, allows staying close to equilibrium without causing congestion.

Slow-start works by doubling the CWND  for every packet acknowledged (hence once every RTT), which implies exponential 
increase in CWND size (2, 4, 8, 16...). This is akin to binary search and allows the sender overshoot the equilibrium value 
by a factor of at most 2 in log(N)\*RTT time, where N is the number of packets sent. The first slow-start ends when - 
because of excessive sending rate, and hence congestion at the gateway/router - a packet is lost. At this point, the sender 
sets its new SSTHRESH value to be max(FlightSize, 2), where FlightSize is the number of unacknowledged packets (which is 
generally CWND until unless CWND > RWND). The sender then restarts slow-start till another loss is encountered or 
CWND = SSTHRESH. If latter, the algorithm moves into congestion avoidance.

In congestion avoidance, the CWND is increased incrementally. For example, if N packets were sent and acknowledged, 
then the sender may send (N+1) packets next. In case of severe packet loss (indicated by RTO timeout) slow-start is 
restarted. Otherwise, CWND keeps growing till packet loss (indicate by duplicate ACKs) is experienced, at which point 
the CWND is reset to half of SSTHRESH.

(6) M. Allman, V. Paxon, E. Blanton. TCP Congestion Control. RFC 5681, September 2009.

## Increase/decrease algorithms, and linear control systems

[_to be concluded_]
