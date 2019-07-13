# Learn BigQuery!
Assuming Standard SQL is known, this material aims to provide a path to master SQL in semi-structured nested data.

## Foundation
Foundational concepts are:
* `unnest()` - turning arrays into row format (and structs into columns)
* `Structs` - mastering them helps using unnest
* `Arrays` - mastering them helps using unnest
* `Subqueries + Unnest` - Run SQL on arrays!
* `Flattening` - Join arrays (unnested as rows) with their parent table

## Data Invironment: Google Analytics Data
[Export Schema](https://support.google.com/analytics/answer/3437719)  
[Example Data](https://console.cloud.google.com/bigquery?folder&p=bigquery-public-data&d=google_analytics_sample&t=ga_sessions_20170801&page=table)

Concepts focus on the data structure, not the possible settings of the tool:
* Session scoped fields
* Hit scoped fields 
* Product scoped fields (Enhanced Ecommerce)
* Internal Promotion fields
* tbd ...

## Data Environment: Firebase Data
[Export Schema](https://support.google.com/firebase/answer/7029846?hl=en&ref_topic=7029512)    
[Example Data](https://console.cloud.google.com/bigquery?folder&p=firebase-public-project&d=analytics_153293282&t=events_20181003&page=table)

Concepts focus on the data structure, not the possible settings of the tool:
* Events and event_params
* tbd ...

## Example

```sql
SELECT
  column
FROM
  table
WHERE column = 'test'
```
