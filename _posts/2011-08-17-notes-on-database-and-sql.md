---
layout: post
title: "Notes on databases: max allowed packet size"
date: "2011-08-17"
description:
image: 
author: Umayr Hassan
tags:
---

The `max.allowed.packet` size limits the size of query packet from client. It may be specified at both client and server 
side. JDBC clients may need to pass "sessionVariables=maxAllowedPacket=..." during connection setup since the driver 
may not support changing session properties once the session has been established.See also 
[this post](http://dev.mysql.com/doc/refman/5.0/en/packet-too-large.html). On server side, 
`mysql --max_allowed_packet=32M` to set the property and `show variables like 'max_allowed_packet` to query it.

Note that max allowed packet property is distinct from the fetch size property, which limits the number of rows 
fetched from the database.
