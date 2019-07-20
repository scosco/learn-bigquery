# Subqueries on a table
*A little note before we start: It is expected you follow along in BigQuery so you see the output of all the queries shown here.* 

## Preparation
First we need to create a table on which we can run queries. For that we use a WITH statement to create a temporary table from a `select`.

Our base query looks like this:
```sql
WITH fruitColors AS (
  SELECT * FROM UNNEST([
    STRUCT('orange' as color, [struct('lemon' as fruit, 3 as amount), ('papaya', 4)] as fruits),
    ('green', [('apple', 5), ('avocado', 8)]),
    ('red', [('apple', 5),('cherry', 43)])
  ])
)

SELECT * FROM fruitColors
```
Note, how we only use the function `struct()` for the first struct to provide names.

You can save the result of this query as a table in your project and dataset of choice to get easier access. For future queries I will simply assume the `WITH` statement and *not* write it down anymore!

## Simple Queries

First, let's try and see how to retrieve the whole array:

```sql
SELECT  
  color,
  fruits
FROM fruitColors
```
As simple as that! Now, what if we only want one field from the array - something like this *doesn't* work

```sql
SELECT  
  color,
  fruits.fruit
FROM fruitColors
```
... because the array itself only consists of single elements: structs. If we want a sub-field from a struct, we'd have to select a struct first. As in: 
```sql
SELECT  
  color,
  fruits[SAFE_OFFSET(0)].fruit
FROM fruitColors
```
We select one struct from the array and retrieve their sub-field. But we want all of them! 

To do that we need to run SQL on the array and select the field this way. `UNNEST()` will turn the array into rows. After selecting `fruit` from these rows we need to feed them back into an array using the function `array()`!
```sql
SELECT  
  color,
  array(select fruit from unnest(fruits)) AS fruits
FROM fruitColors
```
You'll see in the results that this gives us an array of strings!

If we want an array of structs (containing the strings) we simply `select as struct` in the sub-query!
```sql
SELECT  
  color,
  array(select as struct fruit from unnest(fruits)) AS fruits
FROM fruitColors
```
Pretty cool, no?
But instead of reducing information, we can also produce new information out of what we already have!

For example we can uppercase the fruit and calculate the share
```sql
SELECT  
  color,
  array(select as struct 
      *,
      upper(fruit) bigFruit,
      round( amount / sum(amount) over (), 2) as share
    from unnest(fruits)
  ) AS fruits
FROM fruitColors
```

Window functions are no problem at all!
