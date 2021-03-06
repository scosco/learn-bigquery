# Mastering Structs
*A little note before we start: It is expected you follow along in BigQuery so you see the output of all the queries shown here.* 

## Structs and JSON

Structs are lists of key-value pairs with a fixed length. They are a rather simple concept: you come up with some names for the fields and assign some values. We'll see later that we can use them to introduce columns in sub-tables. But first things first ...

One way to define a struct is using struct() function:
```sql
SELECT
  STRUCT(42 AS a, 'hello world' as b)
```
The JSON representation looks as follows:
```json
{
  "a" : "42",
  "b" : "hello world"
}
```
Defining anonymous structs is also possible and may come in handy sometimes:
```sql
SELECT (45, 6, 'abc')
```
These statements do not define the type of the fields - if you want to learn about that please read the [GCP docs](https://cloud.google.com/bigquery/docs/reference/standard-sql/data-types#struct-type). 

### Example
In this example we create a one-row temporary table using WITH and select an element from the struct:
```sql
WITH table AS (
  SELECT 
    STRUCT(4 AS a, 5 AS b) AS field
)

SELECT 
  field.a 
FROM 
  table
```

Try it out!

## Multiple Levels

You can also nest structs within structs:
```sql
WITH table AS (
  SELECT 
    STRUCT(
      4 AS a, 
      5 AS b,
      STRUCT('red' as color, 5 as amount) as level2
    ) AS level1
)

SELECT 
  level1.level2.*
  --,level1.*
  --,*
FROM 
  table
```
Try it out! Uncomment to see the difference when querying for different levels.

## Structs in Tables

In BigQuery table schemas structs are called `RECORD`. You can see in the following rows which fields belong to the struct.

![record in a table](foundation/img/bq-record.png)

Be careful! `RECORD` does *not* mean array! 
