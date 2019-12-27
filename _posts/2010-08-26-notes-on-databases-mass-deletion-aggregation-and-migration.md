---
layout: post
title: "Note on databases: mass deletion, aggregation and migration"
date: "2010-08-26"
description:
image: 
author: Umayr Hassan
tags:
---

_Scenario_: Our company stores ad statistics (e.g. ads viewed) and properties (e.g. homepage) in a database so that marketing folks can query the database according to their needs. The database is MS SQL since it was thought that it would provide better performance for certain queries. This year, the database size grew rapidly such that secondary memory had to be supplemented with USB-based 2TB Flash memory. Eventually, even that filled up and the database was unable to load new data. Thus, our goals are to:

- Delete all data before January 01, 2010, since it's too old to be useful
- Upload the backlog of data into the data base
- Modify the database update script to aggregate all incoming data over certain keys
- Migrate the database to a new one with an updated schema

_Observation 1_: Significant differences between SQL scripts written for MS-SQL and MySQL. For example, [LOAD DATA INFILE](http://dev.mysql.com/doc/refman/5.0/en/load-data.html) queries in MySQL are replaced by [BULK INSERT](http://msdn.microsoft.com/en-us/library/ms188365.aspx) queries in MS-SQL. Moreover, bulk queries in MS-SQL may requires explicit permissions from the DBA to be carried out.

_Observation 2_: The company actually maintains two databases: the primary database stores the various statistics and properties, while the [Cube](http://www.microsoft.com/sqlserver/2008/en/us/Analysis-Services.aspx) is a [MOLAP](http://en.wikipedia.org/wiki/MOLAP) that processes (using XML queries) the data (partitioned by the month) in the primary database and then stores the results, to make certain queries (now made using an ODC-based [PivotTable](http://msdn.microsoft.com/en-us/library/ms178798.aspx)) much faster and easy to invoke. Before issuing bulk delete, we needed ensure that modifying data did not crash the Cube.

Bulk delete was nearly disastrous. It seems that the MS SQL Server was writing to C drive while deleting data from the Flash memory. The available space on C drive fell from <100GB to 24Gb in an hour. This was primarily because the database log file kept on increasing. We had better luck deleting rows in batches. How large should a batch be? For example, a batch of 500000 rows took ~2.5min while five batches of 50 million rows took more than five hours. After running the deletion query, we [shrank the log files](http://technet.microsoft.com/en-us/library/ms190757.aspx) (located on C drive), as well as the [database](http://technet.microsoft.com/en-us/library/ms189035.aspx) (on the Flash-based U drive), to reclaim free space.

Another problem is determining the number of batches, which would requires us to know the total number of rows being deleted beforehand. Given that this number is very large (in billions), a count query might take too long. We roughly estimated the number of batches by counting the rows being affected for ten days and then using the fact that we had around six months of data to be deleted. Here's a script to automate the process:

\[sourcecode language="sql"\] Set rowcount to 50000000 to limit number of deletes per batch SET ROWCOUNT 50000000

DECLARE @innerCount INT SET @innerCount = 0

DECLARE @outerCount INT SET @outerCount = 0

WHILE (@outerCount < 3) BEGIN WHILE (@innerCount < 5) BEGIN BEGIN TRANSACTION -- Use tablockx and holdlock to obtain and hold -- an immediate exclusive table lock. This unusually -- speeds the update because only one lock is needed. DELETE \[reporting\].\[dbo\].\[stats\] WITH (tablockx, holdlock) WHERE \[stats\_date\] < 14610 -- Commit the transaction COMMIT SET @innerCount = (@innerCount + 1) END -- Shrink log files to free up space for future transactions -- Note that reporting\_log is the name of the log file DBCC SHRINKFILE (reporting\_log, 1); -- Reclaim free space in the database. This might take a while. -- Note that reporting is the name of the database being shrunk DBCC SHRINKDATABASE (reporting);

SET @outerCount = (@outerCount + 1) END

\-- Remove rowcount limitation SET ROWCOUNT 0

\-- Delete the rest of rows, less than ROWCOUNT BEGIN TRANSACTION DELETE \[reporting\].\[dbo\].\[stats\] WITH (tablockx, holdlock) WHERE \[stats\_date\] < 14610 COMMIT -- Validate no rows are left to be deleted SELECT COUNT(\*) FROM \[reporting\].\[dbo\].\[stats\] WHERE \[stats\_date\] < 14610 -- Shrink again DBCC SHRINKFILE (reporting\_log, 1); DBCC SHRINKDATABASE (reporting); \[/sourcecode\]

On our system, this query deleted 750 million rows, took 11.5hr to complete, freed up 227GB of memory on U: drive and returned the free space in C: to its normal state. We needed to runs this query a couple more times to delete all rows, taking up 22hr more and finally freeing up ~450GB on U: drive.

It is now trivial to write a [Perl script](http://baishui.info/orelly/linux/dbi/ch08_02.htm) that can remotely connect to a database server (using DBD::Proxy, DBI and DBD::ODBC modules), and execute this query.

_Observation 3_: Once deletion is complete, we may start aggregation data over certain columns to further reduce database size. In our case, the table in question is composed of two kinds of columns: keys and values. We may aggregate values (e.g. sum them) for given keys. Thus, assuming that (key\_i+1...key\_N) are the keys we are aggregating on, this yields an aggregation query with the following format:

\[sourcecode language="sql"\] INSERT INTO aggregated\_table (key1...key\_i, value1...value\_M) ( SELECT key1...key\_i, SUM(value1)...SUM(value\_M) FROM unaggregated\_table GROUP BY key1...key\_i ) \[/sourcecode\]

Finally, as for migration, which is need to change the number of columns in the database, we need to ensure that the aggregated\_table above, when created, include the new columns.

_Observation 4_: What if one accidentally truncates a table? It seems that that only way to undo truncation is by ensuring that it is carried out within a transaction. More information on code and limitations available [here](http://blog.sqlauthority.com/2010/03/04/sql-server-rollback-truncate-command-in-transaction/).
