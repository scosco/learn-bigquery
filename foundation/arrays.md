# Arrays

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

