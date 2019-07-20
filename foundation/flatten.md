# Flatten Nested Tables

*A little note before we start: It is expected you follow along in BigQuery so you see the output of all the queries shown here.* 

When working with BigQuery data it might happen quite often that you want to `group by` a field that sits inside of an array. The problem is that you can't group arrays. We somehow have to get these fields out of the array! Let's tackle this problem!

Remember how `UNNEST()` is turning arrays into table rows and structs into table columns? 
Now, imagine what would happen if we 
1. take an array, e.g. `[3, 5, 6]` and its parent row 
2. turn the array into rows with `UNNEST()` and 
3. `cross join` them with the arrays parent row!

Here is an easy example with two fields: *info* is a string and *myArr* is an array of integers.

```sql
WITH a AS (
  SELECT 
    'lorem ipsum' as info,
    [3, 5, 6] as myArr
)

SELECT * 
FROM a 
--CROSS JOIN UNNEST(myArr) as b
```

When running this query you'll find one row with a string an the array.

Now uncomment the last line:

```sql
WITH a AS (
  SELECT 
    'lorem ipsum' as info,
    [3, 5, 6] as myArr
)

SELECT * 
FROM a 
CROSS JOIN UNNEST(myArr) as b
```

`UNNEST()` temporarily created 3 rows out of the array (we call them *b* in this example) and we cross joined them with our row. That means **we repeated the parent row for each entry in the array**. And I mean the whole row: The array gets repeated as well, of course!

Now, this looks a bit chaotic if you're not used to this method. To tidy things up, you can simply pick your fields of interest. In this case this might be *info* and *b*:

```sql
WITH a AS (
  SELECT 
    'lorem ipsum' as info,
    [3, 5, 6] as myArr
)

SELECT info, b 
FROM a 
CROSS JOIN UNNEST(myArr) as b
```
This pick and choose to create a flat table is referred to as "flattening" a table. **But keep in mind that the array still exists!** And it can get very useful to have it available! Think of it like this: For every element of the unnested array you still have the whole array and its information available. So even if you flattened, you can still write a subquery and check the array for certain things. For example in Google Analytics data you might have flattened to hits scope, but with every hit you still can check all the hits with a subquery.

But we're getting ahead of ourselves :) Now that we have a flat table, we can actually group by and aggregate our formerly nested field!

```sql
WITH a AS (
  SELECT 
    'lorem ipsum' as info,
    [3, 5, 6] as myArr
)

SELECT 
  info, 
  sum(b) as sumB 
FROM a 
CROSS JOIN UNNEST(myArr) as b
GROUP BY info
```

Now that we know how this works we can write the whole query a bit shorter (if you feel comfortable doing that). A `cross join` can be abbreviated as a simple comma `,`. 


```sql
WITH a AS (
  SELECT 
    'lorem ipsum' as info,
    [3, 5, 6] as myArr
)

SELECT 
  info, 
  sum(b) as sumB 
FROM a, UNNEST(myArr) as b
GROUP BY info
```

We can also use the *implicit* form of `UNNEST()`: If we reference an array using the dot operator with the table name BigQuery will automatically assume we want to unnest this array.


```sql
WITH a AS (
  SELECT 
    'lorem ipsum' as info,
    [3, 5, 6] as myArr
)

SELECT 
  info, 
  sum(b) as sumB 
FROM a, a.myArr as b
GROUP BY info
```

But keep in mind what we're doing here: 
1. unnesting the array
2. cross join it with its parent row
