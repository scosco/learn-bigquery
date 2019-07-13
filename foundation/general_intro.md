# General introduction

## Video: Analyzing Big Data in less time with Google BigQuery
Get a quick overview of BigQuery in case you've never seen it:
 - Build several highly-effective analytics solutions with Google BigQuery
 - Provide a clear road map of BigQuery capabilities
 - Explain how to quickly find answers and examples online
 - Share how to best evaluate BigQuery for your use cases

[![Analyzing Big Data in less time with Google BigQuery](http://img.youtube.com/vi/qqbYrQGSibQ/0.jpg)](http://www.youtube.com/watch?v=qqbYrQGSibQ)

## BigQuery Structure
### Projects
A project contains information such as subscribed service API(s), authentication information, billing information and IAM which determines access to the Datasets and the Jobs. Projects are not BigQuery specific, but manage resources on Google Cloud level.

### Datasets
A dataset is a grouping mechanism that holds zero or more tables. A dataset is the lowest level unit of access control. You cannot control access at the table level. A dataset is contained within a specific project. Each dataset can be shared with individual users. Datasets are also referenced in the SQL statements when interacting with BigQuery. 

### Tables
Row-column structures that contain actual data. They belong to a Dataset. You cannot control access at the table level, you do it at Dataset level. For information about the related API type, see Tables.

### Jobs
Jobs are used to start all potentially long-running actions, such as queries, table import, and export requests. Shorter actions, such as list or get requests, are not managed by a job resource. Each job has a job id. A very good use of job id is when you load a large dataset. BigQuery rejects load job with the same job id. Therefore, guaranteeing that data would not be loaded twice.

![BigQuery Structure](foundation/img/bq-structure.png) [Image source](https://cloud.google.com/bigquery/docs)

## BigQuery Storage
BigQuery stores data in columns. Most queries only work on a small number of fields and BigQuery only needs to read those relevant columns to execute a query. Since each column has data of same type, BigQuery could compress the column data much more effectively.

![BigQuery Storage](foundation/img/bq-storage.png) [Image source](https://cloud.google.com/bigquery/docs)

Each column in a table is stored in a data file. For example, a table that contains three columns will equate to 3 files. Each file is replicated a minimum of 3 times and dispersed across multiple zones within a region. Each file is also compressed and encrypted while it is stored on the disk. This is done for performance and insures an additional level of security. When a column is requested, BigQuery finds the file on the first available disk.

The slide shows that a table that contains 3 columns will be stored in 3 separate files on 3 different persistent disk drives in A zone. The files will also be replicated in B zone and C zone to ensure file integrity. The example also shows that C zone has two copies of column 1 on separate disks.

![BigQuery Storage distr](foundation/img/bq-storage-distr.png) [Image source](https://cloud.google.com/bigquery/docs)

## Architecture

BigQuery has a dynamic serving tree that looks different for each query (sized by query needs)
 - BigQuery can handle complex queries much better (multi-stage queries are several times faster)
 - Multiple stages, joins, are a separate level in the tree
 - BigQuery does not need to do several passes through the tree as it did previously when the tree was static

Example - Count of babies by state, year
```sql
SELECT state, year, COUNT(*) AS count_babies
FROM 'bigquery-public-data.samples.natality'
WHERE year >= 1980 and year < 1990
GROUP BY state, year
ORDER BY 3 desc
LIMIT 10
```
![BigQuery Execution](foundation/img/bq-exec.png) [Image source](https://cloud.google.com/bigquery/docs)
 - Because of the columnar data format, only two data fields need to be read from the storage (state, and year) in this query.
 - With a distributed file system, the workers read in chunks of the data in parallel, and apply the filter and aggregation. Shuffling may also occur between stages. The number of workers processing data at each stage is dynamically allocated according to the needs of the query.
 - The workers pass the data to the master in the final stage. The master does the final aggregation, sorting, limiting, and returning the data to the client.
You can use the [query plan explanation](https://cloud.google.com/bigquery/query-plan-explanation) after running the query to understand its execution.
