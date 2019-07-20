# Using WITH
## Introduction
One of the first concepts we need to master is working with [WITH](https://cloud.google.com/bigquery/docs/reference/standard-sql/query-syntax#with-clause). This will help you understanding the tasks and creating your own exercises to practice the concepts.

The WITH clause contains one or more named subqueries which execute every time a subsequent SELECT statement references them. Any clause or subquery can reference subqueries you define in the WITH clause. This includes any SELECT statements on either side of a set operator, such as UNION.

The [WITH](https://cloud.google.com/bigquery/docs/reference/standard-sql/query-syntax#with-clause) clause is useful primarily for readability, because BigQuery does not materialize the result of the queries inside the WITH clause. If a query appears in more than one WITH clause, it executes in each clause.

Example:
```sql
WITH subQ1 AS (SELECT SchoolID FROM Roster),
     subQ2 AS (SELECT OpponentID FROM PlayerStats)
     
SELECT * FROM subQ1
UNION ALL
SELECT * FROM subQ2;
```

So, basically we create our own temporary tables (on run-time only) using queries. 

# Using WITH to Test and Learn
*A little note before we start: It is expected you follow along in BigQuery so you see the output of all the queries shown here.* 

We will use `WITH` mostly to create little exercise tables. And this is something you should do every time you want to test something in or learn about SQL. It saves resources and keeps things at a managable level.

For examle, imagine you want to create a little table with two rows to understand how the function `concat()` works in a table environment. You'd start by writing a temporary table with some sample data:

```sql
WITH a AS (
  SELECT 
    'lorem ipsum' as str1, 'dolor sit amet' as str2
  UNION ALL
  SELECT
    'one two ', 'three four'
)

SELECT 
  * 
FROM a 
```

Now, just add a new field in your `SELECT` statement that concatenates the two other fields!

```sql
WITH a AS (
  SELECT 
    'lorem ipsum' as str1, 'dolor sit amet' as str2
  UNION ALL
  SELECT
    'one two ', 'three four'
)

SELECT 
  *,
  concat(str1, str2) as test
FROM a 
```

This is especially useful if you're testing more complex functions like regex

```sql
WITH a AS (
  SELECT 
    'lorem ipsum' as str1, 'dolor sit amet' as str2
  UNION ALL
  SELECT
    'one two ', 'three four'
)

SELECT 
  *,
  concat(str1, str2) as test,
  regexp_extract(str1, r' (.*)') as test2
FROM a 
```

## When to use WITH

*This chapter might be a bit complex for beginners. If it is, don't bother and just come back at a later time!*

In the example above we see `subQ1` and `subQ2` - first we define them as subqueries. But later we treat them like normal tables. This is quite powerful because we only define them once and then don't have to think about them anymore. We can save cognitive resources!

A typical use cases for `WITH`:
 - prepare raw data before using it
 - use a table multiple times
 - constants
 
Where constants are a simple query without `FROM`. This is useful to quickly adjust analysis time frames (and it is faster than using a temporary function). Note, that we make use of the [table wildcard functionality](https://cloud.google.com/bigquery/docs/querying-wildcard-tables) here which provides us the pseudo-column `_table_suffix`.

```sql
WITH const AS (
  SELECT
    '181002' as startDate,
    '181003' as endDate
)

SELECT
  event_date as date,
  COUNT(DISTINCT user_pseudo_id) users
FROM
  `firebase-public-project.analytics_153293282.events_20*`
WHERE
  -- long version: get dates in two separate subqueries from const
  -- _table_suffix BETWEEN (SELECT startDate FROM const) AND (SELECT endDate FROM const) 

  -- short version: use _table_suffix within the subquery
  (SELECT _table_suffix BETWEEN startDate AND endDate FROM const) 
GROUP BY
  1
```

But this approach works with normal columns as well:

```sql
WITH const AS (
  SELECT
    date '2016-10-02' as startDate,
    date '2016-10-03' as endDate
)

SELECT
  EXTRACT(DATE from created_date) as date,
  COUNT(complaint_type) complaints
FROM
  `bigquery-public-data.austin_311.311_service_requests`
WHERE 
  -- created_date comes from the parent FROM while startDate and endDate come from the subqueries FROM
  (SELECT EXTRACT(DATE from created_date) BETWEEN startDate AND endDate FROM const) 
GROUP BY
  1
```
