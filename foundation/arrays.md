# Arrays
*A little note before we start: It is expected you follow along in BigQuery so you see the output of all the queries shown here.* 

Knowing how to deal with nested arrays is key when using BigQuery! But what is an array?
It's an ordered list of things - as per convention you list these things between `[` and `]`.
Things you can list in an array:
- Integers: `[1, 4, 832]`
- Strings: `['a', 'b', 'c', 'xyz', 'Hallo!' ]`
- Booleans: `[true, true, false, true ]`
- Records (STRUCTs) and within them:
  - all of the above: `[ { int : 1, bool: false }, { int : 25, bool: false }, { int : 620, bool: true } ]`
  - arrays `[ { arr : [5, 2, 9] }, { arr : [7, 2] }, { arr : [15, 0, 94] } ]`
  
  Let's create all examples from above in one query:

```sql
SELECT
  [1, 4, 832] as integers,
  ['a', 'b', 'c', 'xyz', 'Hallo!'] as strings,
  [true, true, false, true ] as booleans,
  [
    STRUCT(1 as int, false as bools), 
    STRUCT(25, false), 
    STRUCT(620, true) 
  ] as structs1,
  [
    STRUCT( [5, 2, 9] as arr ), 
    STRUCT( [7, 2] ), 
    STRUCT( [15, 0, 94] ) 
  ] as structs2
```
Try it for yourself! Have a look at the JSON representation as well!

# Accessing Elements in Arrays

The easiest way to access an element is by using either [`OFFSET()`](https://cloud.google.com/bigquery/docs/reference/standard-sql/functions-and-operators#offset-and-ordinal) which is zero-based or [`ORDINAL()`](https://cloud.google.com/bigquery/docs/reference/standard-sql/functions-and-operators#offset-and-ordinal) which is one-based.
Run the following code:
```sql
WITH t AS (
  SELECT 1 AS id, [1,2,3] AS arr
  UNION ALL
  SELECT 2 AS id, [4,5,6] AS arr
  UNION ALL
  SELECT 3 AS id, [42] AS arr
  )
   
SELECT
  id,
  arr[OFFSET(0)] firstElem,
  arr[ORDINAL(1)] firstElemOrd
FROM t
```

Row | id | firstElem | firstElemOrd
--- | -- | --------- | -------
1 | 1 | 1  | 1
2 | 2 | 4  | 4
3 | 3 | 42 | 42

## Safe Offset - Avoid "Out of Bounds" Error
If you tried to access an object that is not there, you'll get this error: `Error: Array index 2 is out of bounds (overflow)`
To avoid that you can either change your method to access that element and/or use the prefix SAFE_ with your OFFSET function:
- [`SAFE_OFFSET()`](https://cloud.google.com/bigquery/docs/reference/standard-sql/functions-and-operators#safe_offset-and-safe_ordinal)
- [`SAFE_ORDINAL()`](https://cloud.google.com/bigquery/docs/reference/standard-sql/functions-and-operators#safe_offset-and-safe_ordinal)
```sql
SELECT
  id,
  arr[SAFE_OFFSET(1)] off,
  arr[SAFE_ORDINAL(2)] ord
FROM t
```
which is results in NULL values for not existing elements.

Row | id | off | ord
--- | -- | --------- | -------
1 | 1 | 2  | 2
2 | 2 | 5  | 5
3 | 3 | null | null

## Getting the Last Element
You might think of getting the length from that array and use it as an index for ORDINAL - and yes, this does work:
```sql
SELECT
  id,
  arr[ ORDINAL( ARRAY_LENGTH(arr) ) ] lastElem
FROM t
```
But you can also reverse the array and take the first element:
```sql
SELECT
  id,
  ARRAY_REVERSE(arr)[OFFSET(0)] lastElem
FROM t
```
Try it!
But be careful with reversing arrays, it slows down your query execution.

# Concatenate Arrays

Concatenating arrays is achieved by using the function [`ARRAY_CONCAT_AGG()`](https://cloud.google.com/bigquery/docs/reference/standard-sql/functions-and-operators#array_concat_agg) or [`ARRAY_CONCAT()`](https://cloud.google.com/bigquery/docs/reference/standard-sql/functions-and-operators#array_concat), depending on whether you want to aggregate rows (as in GROUP BY) or operate without aggregation of rows:
```sql
WITH t AS (
  SELECT 1 AS id, [1,2,3] AS arr
  UNION ALL
  SELECT 2 AS id, [4,5,6] AS arr
  UNION ALL
  SELECT 3 AS id, [42] AS arr
  )
   
SELECT
  ARRAY_CONCAT_AGG( arr)  as concArr
FROM t
```
The aggregation function has some nice features that might come in handy:
```sql
ARRAY_CONCAT_AGG(expression  [ORDER BY key [{ASC|DESC}] [, ... ]]  [LIMIT n])
```
You can order the way arrays get concatenated and also limit the amount of arrays.
```sql
SELECT
  ARRAY_CONCAT_AGG( arr)  as concArr,
  ARRAY_CONCAT_AGG( arr ORDER BY ARRAY_LENGTH(arr) ASC)  as concArr2,
  ARRAY_CONCAT_AGG( arr ORDER BY ARRAY_LENGTH(arr) ASC LIMIT 2)  as concArr2_2
FROM t
```
