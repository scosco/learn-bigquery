# Mastering Structs
## Structs and JSON

Structs are containers for key-value pairs. 

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
These statements do not define the type of the fields - if you want to learn about that please read cloud.google.com/bigquery/docs/reference/standard-sql/data-types#struct-type 

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
