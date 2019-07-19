# Subqueries: Arrays + `UNNEST()`

`UNNEST()` turns your arrays into tables! That means you can run SQL on arrays!

You can start with this:
```sql
SELECT * FROM UNNEST( [1,1,2,3,5] )
```

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
- Try it with your own data!
- Check out the JSON representation of the result!
- Instead of * query a field!
- Or try `SELECT *, upper(name) AS bigNames, id*3 AS calc FROM` ...


