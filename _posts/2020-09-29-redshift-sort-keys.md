---
layout: post
title: When to Use Redshift Sort Keys?
date: 2020-09-29 00:00:00 +0100
description: # Add post description (optional)
img:  2020-09-29-main-pic.jpg # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [Redshift]
---

Sort keys determine the order in which rows in a table are stored. In general, Amazon Redshift stores columnar data in 1 MB disk blocks. It also stores metadata about every one of these blocks, including the minimum and maximum values within the block. When we run a query that restricts result data to some range, the query processor can use these minimum and maximum values to rapidly skip over a large number of blocks during the table scan. For example, if we have a table with 5 years of data sorted by date and we want to query only for one month, around 98% of the disk blocks can be eliminated from the scan. If this data was not sorted, way more blocks (or possibly all of them) would have to be scanned.

Sort keys have to be determined when we are creating a table (information about them is included in the DDL). Then, when data is initially loaded into the table, the rows are stored on disk in sorted order. Information about sort keys is passed to the query planner and so the query performance is greatly improved.

## Compound Sort Keys

There are two types of sort keys in Amazon Redshift. First one is a compound sort key. It is made up of all the columns listed in the sort key definition in the order they are listed. This ordering is very important here, in that using such kind of key will make sense only if we filter or join data depending on these columns in this order. If we wanted to run a query depending on a secondary column without referencing the primary one, the benefits of using such key decrease. 

Using compound sort keys speeds up joins, GROUP BY and ORDER BY operations, as well as window functions that use PARTITION BY and ORDER BY. They can also help improve compression.

As per AWS documentation the rule of thumb is:
- if recent data is queried most frequently, specify the timestamp column as the leading column for the sort key
- if you do frequent range filtering or equality filtering on one column, specify that column as the sort key
- if you frequently join a (dimension) table, specify the join columns as the sort key


## Interleaved Sort Keys

Interleaved sort key gives equal weight to each column (or subset of columns) in the sort key. This is the main difference from the compound sort key, where the order of columns within the key mattered. We will want to use this type of key if we have multiple queries that use different columns to filter data. 


Additional resources you might find useful:

_[AWS Documentation about vacuuming tables](https://docs.aws.amazon.com/redshift/latest/dg/t_Reclaiming_storage_space202.html)_

_[Tuning table design - step by step tutorial with real-life examples](https://docs.aws.amazon.com/redshift/latest/dg/tutorial-tuning-tables.html)_

_[Detailed step-by-step analysis how to select sort keys based on your needs](https://aws.amazon.com/blogs/big-data/amazon-redshift-engineerings-advanced-table-design-playbook-compound-and-interleaved-sort-keys/)_


_Hope you enjoyed this article! In case of any questions and remarks, please leave a comment._





