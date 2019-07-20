# Enhanced Ecommerce and Product Scope

## Product Array
This array contains informations on products themselves, not what exactly happened to these products. The context can be gathered from the field `hits.ecommerceAction.action_type` which tells whether these products were seen in a product detail view, added to cart, purchased, etc.

There's **one exception** to this: If `hits.product.isImpression` is **true** then the `action_type` is **overruled**! Imagine the following situation: A customer purchased 4 products and now sees the thank-you page. But on this page she also sees a product list with recommendations on what to buy next! How can you possibly track both informations in one hit? You simply add a flag that says: "Hey, this product was seen in a list - it doesn't belong to whatever the action_type is saying, ok?" And this flag is `hits.isImpression`. You'll find an example below.

Field Name | Data Type | Description
--- | --- | ---
hits.product.isImpression | BOOLEAN | TRUE if at least one user viewed this product (i.e., at least one impression) when it appeared in the product list.
hits.product.isClick | BOOLEAN | Whether users clicked this product when it appeared in the product list.
hits.product.customDimensions | RECORD | This section is populated for all hits containing product scope Custom Dimensions.
hits.product.customDimensions.index | INTEGER | The product scope Custom Dimensions index.
hits.product.customDimensions.value | STRING | The product scope Custom Dimensions value.
hits.product.customMetrics | RECORD | This section is populated for all hits containing product scope Custom Metrics.
hits.product.customMetrics.index | INTEGER | The product scope Custom Metrics index.
hits.product.customMetrics.value | INTEGER | The product scope Custom Metrics value.
hits.product.productListName | STRING | Name of the list in which the product is shown, or in which a click occurred. For example, "Home Page Promotion", "Also Viewed", "Recommended For You", "Search Results List", etc.
hits.product.productListPosition | INTEGER | Position of the product in the list in which it is shown.
hits.product | RECORD | This row and nested fields will be populated for each hit that contains Enhanced Ecommerce PRODUCT data.
hits.product.localProductPrice | INTEGER | The price of the product in local currency, expressed as the value passed to Analytics multiplied by 10^6 (e.g., 2.40 would be given as 2400000).
hits.product.localProductRefundAmount | INTEGER | The amount processed as part of a refund for a product in local currency, expressed as the value passed to Analytics multiplied by 10^6 (e.g., 2.40 would be given as 2400000).
hits.product.localProductRevenue | INTEGER | The revenue of the product in local currency, expressed as the value passed to Analytics multiplied by 10^6 (e.g., 2.40 would be given as 2400000).
hits.product.productBrand | STRING | The brand associated with the product.
hits.product.productPrice | INTEGER | The price of the product, expressed as the value passed to Analytics multiplied by 10^6 (e.g., 2.40 would be given as 2400000).
hits.product.productQuantity | INTEGER | The quantity of the product purchased.
hits.product.productRefundAmount | INTEGER | The amount processed as part of a refund for a product, expressed as the value passed to Analytics multiplied by 10^6 (e.g., 2.40 would be given as 2400000).
hits.product.productRevenue | INTEGER | The revenue of the product, expressed as the value passed to Analytics multiplied by 10^6 (e.g., 2.40 would be given as 2400000).
hits.product.productSKU | STRING | Product SKU.
hits.product.productVariant | STRING | Product Variant.
hits.product.v2ProductCategory | STRING | Product Category.
hits.product.v2ProductName | STRING | Product Name.

## Meta Info on Hit Scope
While `product` gives you information about product specifics, there's another field that provides you with the actual context of these products. Like, what is happening with them at the moment: are they being bought or just added to the cart?

Field Name | Data Type | Description
--- | --- | ---
hits.eCommerceAction | RECORD | This section contains all of the ecommerce hits that occurred during the session. This is a repeated field and has an entry for each hit that was collected.
hits.eCommerceAction.action_type | STRING | The action type. Click through of product lists = 1, Product detail views = 2, Add product(s) to cart = 3, Remove product(s) from cart = 4, Check out = 5, Completed purchase = 6, Refund of purchase = 7, Checkout options = 8, Unknown = 0.
hits.eCommerceAction.option | STRING | This field is populated when a checkout option is specified. For example, a shipping option such as option = 'Fedex'.
hits.eCommerceAction.step | INTEGER | This field is populated when a checkout step is specified with the hit.

 Usually the action type applies to all the products in a hit, with the following exception: when hits.product.isImpression = TRUE, the corresponding product is a product impression that is seen while the product action is taking place (i.e., a ""product in list view"").
 Example query to calculate number of products in list views:
 ```sql
 SELECT 
  p.v2productName,
  COUNT(*) AS listImpressions
FROM 
  `bigquery-public-data.google_analytics_sample.ga_sessions_20170801` t, t.hits h, h.product p 
WHERE
  p.isImpression = true
GROUP BY 
  1
 ```
 Example query to calculate number of products in detailed view:
 ```sql
 SELECT 
  p.v2productName,
  COUNT(*) AS productDetailViews
FROM 
  `bigquery-public-data.google_analytics_sample.ga_sessions_20170801` t, t.hits h, h.product p 
WHERE
  p.isImpression is null
  AND
  h.ecommerceAction.action_type='2'
GROUP BY 
  1
 ```

## Transaction Information

Field Name | Data Type | Description
--- | --- | ---
hits.transaction | RECORD | This section is populated for each hit with type = "TRANSACTION".
hits.transaction.transactionId | STRING | The transaction ID of the ecommerce transaction.
hits.transaction.transactionRevenue | INTEGER | Total transaction revenue, expressed as the value passed to Analytics multiplied by 10^6. (e.g., 2.40 would be given as 2400000).
hits.transaction.transactionTax | INTEGER | Total transaction tax, expressed as the value passed to Analytics multiplied by 10^6. (e.g., 2.40 would be given as 2400000).
hits.transaction.transactionShipping | INTEGER | Total transaction shipping cost, expressed as the value passed to Analytics multiplied by 10^6. (e.g., 2.40 would be given as 2400000).
hits.transaction.affiliation | STRING | The affiliate information passed to the ecommerce tracking code.
hits.transaction.currencyCode | STRING | The local currency code for the transaction.
hits.transaction.localTransactionRevenue | INTEGER | Total transaction revenue in local currency, expressed as the value passed to Analytics multiplied by 10^6 (e.g., 2.40 would be given as 2400000).
hits.transaction.localTransactionTax | INTEGER | Total transaction tax in local currency, expressed as the value passed to Analytics multiplied by 10^6 (e.g., 2.40 would be given as 2400000).
hits.transaction.localTransactionShipping | INTEGER | Total transaction shipping cost in local currency, expressed as the value passed to Analytics multiplied by 10^6 (e.g., 2.40 would be given as 2400000).
hits.transaction.transactionCoupon | STRING | The coupon code associated with the transaction.

