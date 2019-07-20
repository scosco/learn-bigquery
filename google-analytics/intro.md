# Introduction

In the course of this training we'll be using the openly available Google Analytics data on the project [bigquery-public-data](https://console.cloud.google.com/bigquery?project=&folder&organizationId&p=bigquery-public-data&d=google_analytics_sample&page=dataset). Note, that BigQuery allows to [pin projects](https://cloud.google.com/bigquery/docs/bigquery-web-ui#pinning_adding_a_project) for easy access.

You'll want to consult the documentation on the [table schema](https://support.google.com/analytics/answer/3437719) while practicing. It's useful to bookmark this page to have it easily available.

## Prerequisites

You should be familiar with all of these concepts:
- WITH
- `UNNEST()`
- Subqueries on unnested arrays in tables
- Flatten tables
- Combinations of these concepts

## Example

Here's an example query to get started

```sql
SELECT 
  date,
  SUM(totals.visits) as sessions,
  COUNT(DISTINCT fullvisitorid) as visitors,
  SUM(totals.totalTransactionRevenue)/1000000 as revenue
FROM `bigquery-public-data.google_analytics_sample.ga_sessions_20170801` 
GROUP BY 1
LIMIT 1000
```
