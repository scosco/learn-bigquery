# Subqueries: Arrays + UNNEST()

[`UNNEST()`](https://cloud.google.com/bigquery/docs/reference/standard-sql/query-syntax#unnest) turns your arrays into tables! That means you can run SQL on arrays!

Start with this:
```sql
SELECT * FROM UNNEST( [1,1,2,3,5] )
```
`UNNEST()` taks the array as an input and returns its contents as table rows to the `FROM`.

And this is how you give your unnested array an alias:
```sql
SELECT n FROM UNNEST( [1,1,2,3,5] ) AS n
```

Pretty neat, hm?

But what about columns? Introduce STRUCTs!
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


