---
layout: post
title: Solving Gaps and Islands Problem with AWS Redshift
date: 2020-03-20 00:00:00 +0100
description: # Add post description (optional)
img: 2020-03-20-main_pic.jpg # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [SQL, Amazon Web Services, AWS Redshift]
---

Gaps and Islands problem appears when there is a **sequence of rows** in a table that should appear at some **regular intervals** (daily measures of temperature, weekly summary of sales, etc.) but **some entries are missing**. Although such issue appears most often in data ordered by a date or a timestamp, in general it can be any sequence ordered by any other type of column which could be defined as a sequence of consecutive values (integers, alphabet letters and so on). Islands refer to chunks of sorted consecutive records and gaps are simply missing values between the islands. This might sound a little bit enigmatic but it reality is very intuitive. If we have a dataset of some measures taken in March 2020 ordered by day and there are missing measures for 10th and 24th of March (for whatever reason), the gaps and islands are as follows:

Island: 1<sup>st</sup> - 9<sup>th</sup> March

Gap: 10<sup>th</sup> March

Island: 11<sup>th</sup> - 23<sup>th</sup> March

Gap: 24<sup>th</sup> March

Island: 25<sup>th</sup> - 31<sup>st</sup> March

Real-life examples of such situations might include a temporary unavailability of systems acting as source of data, any kinds of errors in the stage of dataset creation or simply raw data structure being not 'rich' enough to be directly used in an analysis or a dashboard.


# Business Problem to Solve

Let's imagine we have some kind of an application. Everyone can use the application as long as they create a user account. Some functionalities of our app are available for free and some only for paid premium account. There is also a 1 week free trial. Depending on the usage of an app we also divide users into categories like 'beginner', 'contributor' and 'expert'. In our database we gather all possible logs of users behaviours and attributes. Now let's say there is a trick in these logs. Everytime a new user account is created, the log records a unique ID of this account and all user attributes. But then with any change of any of the attributes, we only get the ID and this particular attribute. Let's take a look at the sample raw data below to understand the problem better.


```sql
CREATE TABLE user_sample
(
    log_date        date,
    user_account_id integer,
    action_type     varchar(32),
    account_type    varchar(16),
    user_level      varchar(16)
);

INSERT INTO user_sample VALUES ('2020-02-15', 111, 'user account created', 'free (basic)', 'beginner');
INSERT INTO user_sample VALUES ('2020-02-18', 111, 'user account updated', 'trial', NULL);
INSERT INTO user_sample VALUES ('2020-02-25', 111, 'user account updated', NULL, 'contributor');
INSERT INTO user_sample VALUES ('2020-03-01', 111, 'user account updated', 'premium', NULL);
INSERT INTO user_sample VALUES ('2020-03-02', 111, 'user account updated', NULL, 'expert');
INSERT INTO user_sample VALUES ('2020-03-06', 111, 'user account deleted', NULL, NULL);
INSERT INTO user_sample VALUES ('2020-02-19', 222, 'user account created', 'free (basic)', 'beginner');
INSERT INTO user_sample VALUES ('2020-03-01', 333, 'user account created', 'free (basic)', 'beginner');
INSERT INTO user_sample VALUES ('2020-03-04', 333, 'user account deleted', NULL, NULL);

SELECT *
FROM user_sample
ORDER BY user_account_id, log_date;
```

Output:

| log\_date | user\_account\_id | action\_type | account\_type | user\_level |
| :--- | :--- | :--- | :--- | :--- |
| 2020-02-15 | 111 | user account created | free \(basic\) | beginner |
| 2020-02-18 | 111 | user account updated | trial | NULL |
| 2020-02-25 | 111 | user account updated | NULL | contributor |
| 2020-03-01 | 111 | user account updated | premium | NULL |
| 2020-03-02 | 111 | user account updated | NULL | expert |
| 2020-03-06 | 111 | user account deleted | NULL | NULL |
| 2020-02-19 | 222 | user account created | free \(basic\) | beginner |
| 2020-03-01 | 333 | user account created | free \(basic\) | beginner |
| 2020-03-04 | 333 | user account deleted | NULL | NULL |


As you can see when there is a new account created all columns are populated with values (and intuitively, all users start their accounts with basic plan and they are on the beginner level). Then in case of a change in one of user attributes (for example our user 111 changed their _account_type_ to trial on 2020-02-18) we do not receive their other attribute (here _user_level_) and the assumption is it stayed as it already was. When a user account is deleted, we receive no attributes at all. On 2020-03-06 user 111 deleted their account and we can assume by analyzing their historical records that at this day they had a premium account and were at the expert level. 

Now such information might be easy to deduce while looking at my simple example with 9 rows, but imagine hundreds of thousands of user records. No analysis could ever be done based on such a messy dataset. What we want to achieve here is having all possible information for every record in the dataset. To put it even simpler - let's get rid of all the nulls. Part 1 shows how to solve such a problem.

Additionally, if the further analysis of this dataset required that we (or another program like a dashboard) easily query the state of every existing user account at a particular date, we also need to somehow fill the gaps in the dates themselves. This is an example of building a slowly changing dimension with a daily grain (well, there are numerous other types of SCDs and other ways of tackling irregularly changing data, I recommend [Wikipedia article on SCD](https://en.wikipedia.org/wiki/Slowly_changing_dimension) as a reference). This will be covered in the second part.

# Part 1 - Propagate User Attributes Using Advanced Window Functions

So, in the second row, in column _user_level_ instead of null we would like to have the most recent _user_level_ for this user_account_id which is _beginner_. In the third row the _account_type_ should be _trial_. And so on.

The query that does the magic looks as follows:

```sql
SELECT
    log_date
  , user_account_id
  , action_type
  , last_value(account_type IGNORE NULLS)
    OVER (PARTITION BY user_account_id ORDER BY log_date ROWS UNBOUNDED PRECEDING) AS account_type
  , last_value(user_level IGNORE NULLS)
    OVER (PARTITION BY user_account_id ORDER BY log_date ROWS UNBOUNDED PRECEDING) AS user_level
FROM user_sample
ORDER BY user_account_id, log_date;
```

And it gives the following output:

| log\_date | user\_account\_id | action\_type | account\_type | user\_level |
| :--- | :--- | :--- | :--- | :--- |
| 2020-02-15 | 111 | user account created | free \(basic\) | beginner |
| 2020-02-18 | 111 | user account updated | trial | beginner |
| 2020-02-25 | 111 | user account updated | trial | contributor |
| 2020-03-01 | 111 | user account updated | premium | contributor |
| 2020-03-02 | 111 | user account updated | premium | expert |
| 2020-03-06 | 111 | user account deleted | premium | expert |
| 2020-02-19 | 222 | user account created | free \(basic\) | beginner |
| 2020-03-01 | 333 | user account created | free \(basic\) | beginner |
| 2020-03-04 | 333 | user account deleted | free \(basic\) | beginner |

## Now what happened here?

We partition data by _user_account_id_ because this is the window within which we want to track user attributes. Naturally, they are ordered by _log_date_. Then, we define a frame clause. For every record within a window (that is within _user_account_id_ ) we want to look back on previous _account_type_ values (hence the ROWS UNBOUNDED PRECEDING part) and replace the value in the current record the last non-null value. Analogically, we run the same function for _user_level_ column.

Two awesome references that can help understanding frame clauses better are [AWS Window Function Syntax Documentation](https://docs.aws.amazon.com/redshift/latest/dg/r_Window_function_synopsis.html) and this [Window Functions in Redshift](https://sonra.io/2017/07/11/introduction-window-functions-redshift/) article.


# Part 2 - Implement Date Ranges for Every User State

Okay, so now there are no nulls in the table and for whichever record we query we see all possible attributes of a user in that particular moment in time. However, there are still information missing from this table. What if I wanted to check what was the status of all users of my app as of 2020-03-03? There is no row with such a _log_date_.

To easily query the user report by a specific date, we will introduce a time range in which every user state applied. _log_date_ in this case acts as a date from which a user existed with particular attributes. _valid_to_ is equal to a day before any changes occurred. If after some point in time user never changed their state, their _valid_to_ date will be a surrogate high date '9999-12-31'. It indicates the user is still active with attributes as in this particular record. For user deletion entries, _valid_from_ and _valid_to_ dates are equal.

Note that we used a common table expression here aliased as _users_no_gaps_. This is the same subquery we saw in part 1 of tutorial.

I saved the result of the whole query into a new table named _users_final_report_.

```sql
CREATE TABLE users_final_report AS (
    WITH users_no_gaps AS (
        SELECT
            log_date
          , user_account_id
          , action_type
          , last_value(account_type IGNORE NULLS)
            OVER (PARTITION BY user_account_id ORDER BY log_date ROWS UNBOUNDED PRECEDING) AS account_type
          , last_value(user_level IGNORE NULLS)
            OVER (PARTITION BY user_account_id ORDER BY log_date ROWS UNBOUNDED PRECEDING) AS user_level
        FROM user_sample)
    SELECT
        log_date                AS valid_from
      , CASE
            WHEN action_type != 'user account deleted' THEN
                nvl(lead(log_date, 1) OVER (PARTITION BY user_account_id ORDER BY log_date) - 1, '9999-12-31') 
            -- surrogate date indicates no further changes in user attributes
            ELSE valid_from END AS valid_to 
            -- if user is deleted the date of their deletion becomes their 'valid_to' date
      , user_account_id
      , action_type
      , account_type
      , user_level
    FROM users_no_gaps
    ORDER BY user_account_id, valid_from)
;
```

Now that we built the final structure, we can go back to the question that was asked at the beginning of part 2. We want to see the state of all users as of 2020-03-03. The query is simple:

```sql
SELECT *
FROM users_final_report
WHERE '2020-03-03' BETWEEN valid_from AND valid_to;
```

And the output clean and easy to interpret:

| valid\_from | valid\_to | user\_account\_id | action\_type | account\_type | user\_level |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 2020-02-19 | 9999-12-31 | 222 | user account created | free \(basic\) | beginner |
| 2020-03-01 | 2020-03-03 | 333 | user account created | free \(basic\) | beginner |
| 2020-03-02 | 2020-03-05 | 111 | user account updated | premium | expert |

_Hope you enjoyed this tutorial! In case of any questions and remarks, please leave a comment._





