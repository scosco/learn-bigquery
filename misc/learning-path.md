# Learning Path

## Foundation
 - master structs!
   - write your own structs (using the function and anonymous structs)
 - master arrays!
   - write your own arrays
   - create arrays using `generate_array()` and `generate_date_array()` functions
   - get single elements from arrays using `offset()`, `ordinal()`, `safe_offset()` and `safe_ordinal()`
 - master structs in arrays!
   - list simple structs in an array, e.g. `SELECT [(1, 'a'),(5, 'c')]` but fancier
   - list structs that contain structs
   - list structs that contain arrays
 - master `unnest()` and SQL on arrays!
   - run simple `select` and `select as struct` SQL statements on arrays, also feeding back into arrays using `array()`
   - run aggregation statements: simple ones over the whole array and more complex ones building groups with `group by`
   - run window functions on arrays
   - retrieve special elements from arrays: last, first, second, third according to a custom order (not array indices), biggest, smallest element
 - master flattening a.k.a. joins on unnested arrays
   - flatten with cross join
   - flatten with left join
   - flatten within a subquery
   
## Google Analytics Data

 - ### master session scope
   - understand meaning of session scoped fields - build a case per struct, e.g. retrieve information from `device` and explain what it means, as in: interpret the numbers
   - understand main metrics: sessions (all versions), visitors, all things from `totals`
   - run sub-queries on `customDimensions`
 - master hit scope
   - understand main fields and build cases: `hitNumber`, `type`, `time`+`visitstarttime` and `visitid`, `datasource`, `page`, `appInfo`
   - understand main metrics: hits, pageviews, screenviews, events (non- and interactive), build cases and explain the results
   - run sub-queries on `hits` array
   - re-build numbers from struct `totals` using sub-queries
   - flatten to hit scope
   - understand when to do what: flatten vs. sub-query (applicable to all arrays) - build a case
   - run sub-queries on `hits.customDimensions` (2 ways: 1. from hits scope and 2. from session scope)
 - master product scope
   - understand meta-infos from `hits` scope: `ecommerceAction` and `transaction` - build a case each
   - understand main metrics: products, qty products, price, revenue, build cases and explain them
   - run sub-queries on `product` from scope `hits` (flattened)
   - run sub-queries on `hits.product` from session scope (2 methods: 1. nested sub-queries and 2. flattening within sub-query)
   - flatten to `product` scope
   - run sub-queries on `hits.product.customDimensions` (3 ways: from session, hits and product scope; mixed methods: nested sub-queries and/or flattening)
 - master promotion scope
   - understand meta-field `promotionActionInfo` - build a case
   - run sub-queries on `promotion` from `hits`
   - run sub-queries on `promotion` from session scope
