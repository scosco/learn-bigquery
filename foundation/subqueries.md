# Subqueries: Arrays + UNNEST()
*A little note before we start: It is expected you follow along in BigQuery so you see the output of all the queries shown here.* 

Nested data can look a bit scary at first - how are we supposed to get information from these arrays? A lot of poeple learn about the flattening "trick" quite early and use it as a one-size-fits-all solution. But there might be a neater way to approach those arrays!

Enter [`UNNEST()`](https://cloud.google.com/bigquery/docs/reference/standard-sql/query-syntax#unnest) - it turns your arrays into tables! That means you can run SQL on arrays!

Start with this little query - `UNNEST()` takes our hard-coded array, turns it into rows and feeds them to the `FROM`. After that, `SELECT` shows us the wanted output: all array elements, but in the form of distinct rows. 
```sql
SELECT * FROM UNNEST( [1,1,2,3,5] )
```

And this is how you give your unnested array an alias:

```sql
SELECT n FROM UNNEST( [1,1,2,3,5] ) AS n
```
Pretty neat, hm? You can even think of your unnested array as a temporary table.

But what about columns? A real table has a couple of columns, right? Introduce STRUCTs!
```sql
SELECT * FROM UNNEST( [ 
    STRUCT(12 as id, 'Hannah' as name),
    (13, 'Simone'),
    (14, 'Ada')
  ] )
```

BigQuery is semi-structured - that means the schema needs to be consistent. This is also true for sub-fields within structs!
In the second line we're using the function `STRUCT(12 as id, 'Hannah' as name)` because it allows us to name the fields. The following structs `(13, 'Simone')` and `(14, 'Ada')` are anonymous and BigQuery infers their name from the first struct.

Using Structs means to have a fixed list of key-value pairs - the length of the list is not variable! This is perfect for being consistent with schemas. Every struct provides the *columns* of their respective row. 

To sum up: `UNNEST()` turns array elements into rows and struct elements (within arrays) into columns.

- Try this query with your own data! Just modify the variables or add some fields.
- Check out the JSON representation of the result!
- Instead of * query a field!
- Or try `SELECT *, upper(name) AS bigNames, id*3 AS calc FROM` ...


