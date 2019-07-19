# Learning Path

## Foundation
   1. master structs!
   2. master arrays!
   3. master structs in arrays!
   4. master `unnest()` and SQL on arrays! 
   5. master flattening a.k.a. joins on unnested arrays
## Google Analytics Data
   1. master session scope
      - understand meaning of session scoped fields
      - run sub-queries on `customDimensions`
   2. master hit scope
      - run sub-queries on `hits` array
      - re-build numbers from struct `totals` using sub-queries
      - flatten to hit scope
      - understand when to do what: flatten vs. sub-query (applicable to all arrays)
      - run sub-queries on `hits.customDimensions` (2 ways: 1. from hits scope and 2. from session scope)
   3. master product scope
      - understand meta-infos from `hits` scope: `ecommerceAction` and `transaction`
      - run sub-queries on `product` from scope `hits` (flattened)
      - run sub-queries on `hits.product` from session scope (2 methods: 1. nested sub-queries and 2. flattening within sub-query)
      - flatten to `product` scope
      - run sub-queries on `hits.product.customDimensions` (3 ways: from session, hits and product scope; mixed methods: nested sub-queries and/or flattening)
   4. master promotion scope
     - understand meta-field `promotionActionInfo`
     - run sub-queries on `promotion` from `hits`
     - run sub-queries on `promotion` from session scope
