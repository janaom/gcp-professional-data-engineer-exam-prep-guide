- All info about the Google Cloud Professional Data Engineer certification can be found [here](https://cloud.google.com/learn/certification/data-engineer).

- Check [exam guide](https://services.google.com/fh/files/misc/professional_data_engineer_exam_guide_english.pdf).

- The official Google Cloud [docs](https://cloud.google.com/docs).

- The Data Engineer Learning Path on [Google Cloud Skills Boost](https://www.cloudskillsboost.google/paths/16).

-----------------------

❗ This RP contains my personal notes that helped me to prepare for the PDE exam.

------------------------------

# Cloud Storage

You can select from the following location types ([link](https://cloud.google.com/storage/docs/locations)):

    A region is a specific geographic place, such as São Paulo.

    A dual-region is a specific pair of regions, such as Tokyo and Osaka.
        Dual-region pairings can be predefined or configurable.

    A multi-region is a large geographic area, such as the United States, that contains two or more geographic places.

![image](https://github.com/user-attachments/assets/849c4723-d333-4eeb-973e-821e60aa09d5)

## Autoclass ([link](https://cloud.google.com/storage/docs/autoclass))

The Autoclass feature automatically transitions objects in your bucket to appropriate storage classes based on each object's access pattern. The feature moves data that is not accessed to colder storage classes to reduce storage cost and moves data that is accessed to Standard storage to optimize future accesses. Autoclass simplifies and automates cost saving for your Cloud Storage data.

### Should you use Autoclass?

When enabled, Autoclass reduces the amount of data management you need to do and eliminates certain charges that apply for other buckets. Autoclass is a useful feature to enable for the following general access patterns:

    Your data has a variety of access frequencies.
    The access patterns for your data are unknown or unpredictable.

However, Autoclass is not recommended if the majority of your bucket's data fits into the use cases of specific storage classes. For example, say your bucket has two use cases: some data is accessed weekly while some data is backup data that is never meant to be accessed. In this scenario, Autoclass is not recommended if you know which objects fall into each of those use cases.

Autoclass is also not recommended if other Google Cloud services regularly read data from the bucket. For example, Autoclass is not recommended if you use Sensitive Data Protection to scan the content of your bucket.

### Transition behavior

Once Autoclass is enabled, objects at least 128 KiB in size transition between storage classes as follows:

    If an object's data is accessed, the object transitions to Standard storage.

    Any object that isn't accessed for 30 days transitions to Nearline storage.

If the bucket is configured to use Nearline storage as the terminal storage class, Autoclass only changes the state of an object stored in Nearline storage if that object is accessed.

## Data availability and durability ([link](https://cloud.google.com/storage/docs/availability-durability))

Objects stored in a dual-region or multi-region bucket are stored redundantly in at least two separate geographic places.

    For dual-regions, you select the specific regions in which your objects are stored.

    For multi-regions, the specific data centers used for storing your data are determined by Cloud Storage as needed, but are located within the geographic boundary of the multi-region and are separated by at least 100 miles. This provides redundancy across regions at a lower storage cost than dual-regions.

    In the unlikely event of a region-wide outage, such as one caused by a natural disaster, dual-region and multi-region buckets remain available, with no need to change storage paths.

Objects stored in dual-region and multi-region buckets are typically replicated across geographic places using default replication.

    If one of the places an object is stored becomes unavailable after the object is successfully uploaded but prior to it being replicated to the second location, Cloud Storage's strong consistency ensures that stale versions of the object won't be served and that subsequent overwrites aren't reverted when the region becomes available again.

    Objects stored in dual-regions can optionally use turbo replication to achieve a faster, more predictable replication across regions.

## Turbo replication

Turbo replication provides faster redundancy across regions for data in your dual-region buckets, which reduces the risk of data loss exposure and helps support uninterrupted service following a regional outage. When enabled, turbo replication is designed to replicate 100% of newly written objects to the two regions that constitute a dual-region within the recovery point objective of 15 minutes, regardless of object size.

Note that even for default replication, most objects finish replication within minutes.

While redundancy across regions and turbo replication help support business continuity and disaster recovery (BCDR) efforts, administrators should plan and implement a full BCDR architecture that's appropriate for their workload.

For more information, see the Step-by-step guide to designing disaster recovery for applications in Google Cloud.

### Limitations

    Turbo replication is only available for buckets in dual-regions.

    Turbo replication cannot be managed through the XML API, including creating a new bucket with turbo replication enabled.

    When turbo replication is enabled on a bucket, it can take up to 10 seconds before it begins to apply to newly written objects.

    Object writes that began prior to enabling turbo replication on a bucket replicate across regions at the default replication rate.
        Object composition that uses any source objects written using default replication in the last 12 hours creates a composite object that also uses default replication.


# File Formats

## Avro ([link](https://cloud.google.com/bigquery/docs/loading-data-cloud-storage-avro))

Avro is an open source data format that bundles serialized data with the data's schema in the same file.

When you load Avro data from Cloud Storage, you can load the data into a new table or partition, or you can append to or overwrite an existing table or partition. When your data is loaded into BigQuery, it is converted into columnar format for Capacitor (BigQuery's storage format).

### Advantages of Avro

Avro is the preferred format for loading data into BigQuery. Loading Avro files has the following advantages over CSV and JSON (newline delimited):

    The Avro binary format:
        Is faster to load. The data can be read in parallel, even if the data blocks are compressed.
        Doesn't require typing or serialization.
        Is easier to parse because there are no encoding issues found in other formats such as ASCII.
    When you load Avro files into BigQuery, the table schema is automatically retrieved from the self-describing source data.

## CSV ([link](https://cloud.google.com/bigquery/docs/loading-data-cloud-storage-csv))

When you load CSV data from Cloud Storage, you can load the data into a new table or partition, or you can append to or overwrite an existing table or partition. When your data is loaded into BigQuery, it is converted into columnar format for Capacitor (BigQuery's storage format).

### Limitations

You are subject to the following limitations when you load data into BigQuery from a Cloud Storage bucket:

    If your dataset's location is set to a value other than the US multi-region, then the Cloud Storage bucket must be in the same region or contained in the same multi-region as the dataset.
    BigQuery does not guarantee data consistency for external data sources. Changes to the underlying data while a query is running can result in unexpected behavior.
    BigQuery does not support Cloud Storage object versioning. If you include a generation number in the Cloud Storage URI, then the load job fails.

When you load CSV files into BigQuery, note the following:

    CSV files don't support nested or repeated data.
    Remove byte order mark (BOM) characters. They might cause unexpected issues.
    If you use gzip compression, BigQuery cannot read the data in parallel. Loading compressed CSV data into BigQuery is slower than loading uncompressed data. See Loading compressed and uncompressed data.
    You cannot include both compressed and uncompressed files in the same load job.
    The maximum size for a gzip file is 4 GB.
    Loading CSV data using schema autodetection does not automatically detect headers if all of the columns are string types. In this case, add a numerical column to the input or declare the schema explicitly.
    When you load CSV or JSON data, values in DATE columns must use the dash (-) separator and the date must be in the following format: YYYY-MM-DD (year-month-day).
    When you load JSON or CSV data, values in TIMESTAMP columns must use a dash (-) or slash (/) separator for the date portion of the timestamp, and the date must be in one of the following formats: YYYY-MM-DD (year-month-day) or YYYY/MM/DD (year/month/day). The hh:mm:ss (hour-minute-second) portion of the timestamp must use a colon (:) separator.
    Your files must meet the CSV file size limits described in the load jobs limits.

### Encoding

If you don't specify an encoding, or if you specify UTF-8 encoding when the CSV file is not UTF-8 encoded, BigQuery attempts to convert the data to UTF-8. Generally, if the CSV file is ISO-8859-1 encoded, your data will be loaded successfully, but it may not exactly match what you expect. If the CSV file is UTF-16BE, UTF-16LE, UTF-32BE, or UTF-32LE encoded, the load might fail. To avoid unexpected failures, specify the correct encoding by using the --encoding flag.

# Analytics Hub ([link](https://cloud.google.com/bigquery/docs/analytics-hub-introduction))

Analytics Hub is a data exchange platform that lets you share data and insights at scale across organizational boundaries with a robust security and privacy framework. With Analytics Hub, you can discover and access a data library curated by various data providers. This data library also includes Google-provided datasets.

As an Analytics Hub user, you can perform the following tasks:

    As an Analytics Hub publisher, you can monetize data by sharing it with your partner network or within your own organization in real time. Listings let you share data without replicating the shared data. You can build a catalog of analytics-ready data sources with granular permissions that let you deliver data to the right audiences. You can also manage subscriptions and view the usage metrics for your listings.

### Limitations

Analytics Hub has the following limitations:

    A shared dataset can have a maximum of 1,000 linked datasets.

    A shared topic can have a maximum of 10,000 Pub/Sub subscriptions. This limit includes linked Pub/Sub subscriptions and Pub/Sub subscriptions created outside of Analytics Hub (for example, directly from Pub/Sub).

    A dataset with unsupported resources cannot be selected as a shared dataset when you create a listing. For more information about the BigQuery objects that Analytics Hub supports, see Shared datasets in this document.

    You can't set IAM roles or IAM policies on individual tables within a linked dataset. Apply them at the linked dataset level instead.

    You can't attach IAM tags on tables within a linked dataset. Apply them at the linked dataset level instead.

    Linked datasets created before July 25, 2023 are not backfilled by the subscription resource. Only subscriptions created after July 25, 2023 work with the API methods.

# Bigtable ([link](https://cloud.google.com/bigtable/docs))

Bigtable is a low-latency NoSQL database service for machine learning, operational analytics, and user-facing operations. It's a wide-column, key-value store that can scale to billions of rows and thousands of columns. With Bigtable, you can replicate your data to regions across the world for high availability and data resiliency.

### What it's good for ([link](https://cloud.google.com/bigtable/docs/overview))

Bigtable is ideal for applications that need high throughput and scalability for key-value data, where each value is typically no larger than 10 MB. Bigtable also excels as a storage engine for batch MapReduce operations, stream processing/analytics, and machine-learning applications.

You can use Bigtable to store and query all of the following types of data:

    Time-series data, such as CPU and memory usage over time for multiple servers.
    Marketing data, such as purchase histories and customer preferences.
    Financial data, such as transaction histories, stock prices, and currency exchange rates.
    Internet of Things data, such as usage reports from energy meters and home appliances.
    Graph data, such as information about how users are connected to one another.

### Bigtable storage model

Bigtable stores data in massively scalable tables, each of which is a sorted key-value map. The table is composed of rows, each of which typically describes a single entity, and columns, which contain individual values for each row. Each row is indexed by a single row key, and columns that are related to one another are typically grouped into a column family. Each column is identified by a combination of the column family and a column qualifier, which is a unique name within the column family.

![image](https://github.com/user-attachments/assets/6289f025-dac6-4ab1-95a7-5f60972f6bf9)

### Causes of slower performance ([link](https://cloud.google.com/bigtable/docs/performance#slower-perf))

- You read a large number of non-contiguous row keys or row ranges in a single read request.
- Your table's schema is not designed correctly.
- The rows in your Bigtable table contain large amounts of data.
- The rows in your Bigtable table contain a very large number of cells.
- The cluster doesn't have enough nodes.
- The Bigtable cluster was scaled up or scaled down recently.
- The Bigtable cluster uses HDD disks.
- There are issues with the network connection.
- You are using replication but your application is using an out-of-date client library.
- You enabled replication but didn't add more nodes to your clusters.

### Troubleshoot performance issues ([link](https://cloud.google.com/bigtable/docs/performance#troubleshooting))

- Look at the Key Visualizer scans for your table.
- Try commenting out the code that performs Bigtable reads and writes.
- Ensure that you're creating as few clients as possible.
- Make sure you're reading and writing many different rows in your table.
- Verify that you see approximately the same performance for reads and writes.
- Use the right type of write requests for your data.
- Check the latency for a single row.
- Use a separate app profile for each workload.
- Enable client-side metrics.

# Spanner ([link](https://cloud.google.com/spanner/docs))

Spanner is a fully managed, mission-critical database service that brings together relational, graph, key-value, and search. It offers transactional consistency at global scale, automatic, synchronous replication for high availability, and support for two SQL dialects: GoogleSQL (ANSI 2011 with extensions) and PostgreSQL. 

### Transactions overview ([link](https://cloud.google.com/spanner/docs/transactions))

A transaction in Spanner is a set of reads and writes that execute atomically at a single logical point in time across columns, rows, and tables in a database.

Spanner supports these transaction modes:

    Locking read-write. These transactions rely on pessimistic locking and, if necessary, two-phase commit. Locking read-write transactions may abort, requiring the application to retry.

    Read-only. This transaction type provides guaranteed consistency across several reads, but does not allow writes. By default, read-only transactions execute at a system-chosen timestamp that guarantees external consistency, but they can also be configured to read at a timestamp in the past. Read-only transactions do not need to be committed and do not take locks. In addition, read-only transactions might wait for in-progress writes to complete before executing.

# BigQuery ([link](https://cloud.google.com/bigquery/docs))

BigQuery is Google Cloud's fully managed, petabyte-scale, and cost-effective analytics data warehouse that lets you run analytics over vast amounts of data in near real time. With BigQuery, there's no infrastructure to set up or manage, letting you focus on finding meaningful insights using GoogleSQL and taking advantage of flexible pricing models across on-demand and flat-rate options.

### BigQuery analytics ([link](https://cloud.google.com/bigquery/docs/introduction#bigquery-analytics))

Descriptive and prescriptive analysis uses include business intelligence, ad hoc analysis, geospatial analytics, and machine learning. You can query data stored in BigQuery or run queries on data where it lives using external tables or federated queries including Cloud Storage, Bigtable, Spanner, or Google Sheets stored in Google Drive.

    ANSI-standard SQL queries (SQL:2011 support) including support for joins, nested and repeated fields, analytic and aggregation functions, multi-statement queries, and a variety of spatial functions with geospatial analytics - Geographic Information Systems.
    Create views to share your analysis.
    Business intelligence tool support including BI Engine with Looker Studio, Looker, Google Sheets, and 3rd party tools like Tableau and Power BI.
    BigQuery ML provides machine learning and predictive analytics.
    BigQuery Studio offers features such as Python notebooks, and version control for both notebooks and saved queries. These features make it easier for you to complete your data analysis and machine learning (ML) workflows in BigQuery.
    Query data outside of BigQuery with external tables and federated queries.

## Introduction to the BigQuery Storage Write API ([link](https://cloud.google.com/bigquery/docs/write-api))

The BigQuery Storage Write API is a unified data-ingestion API for BigQuery. It combines streaming ingestion and batch loading into a single high-performance API. You can use the Storage Write API to stream records into BigQuery in real time or to batch process an arbitrarily large number of records and commit them in a single atomic operation.

### Advantages of using the Storage Write API
- Exactly-once delivery semantics.
- Stream-level transactions.
- Transactions across streams.
- Efficient protocol.
- Schema update detection.
- Lower cost.

## Introduction to views ([link](https://cloud.google.com/bigquery/docs/views-intro))

A view is a virtual table defined by a SQL query. You can use views to provide an easily reusable name for a complex query or a limited set of data that you can then authorize other users to access. Once you create a view, a user can then query the view as they would a table. Query results contain only the data from the tables and fields specified in the query that defines the view.

The query that defines the view is run each time the view is queried. If you frequently query a large or computationally expensive view, then you should consider creating a materialized view.

BigQuery views are commonly used to:

    Abstract and store calculation and join logic in a common object to simplify query use
    Provide access to a subset of data and calculation logic without accessing to the base tables

You can also use a view as a data source for a visualization tool such as Looker Studio.

### Comparison to materialized views

Views are virtual and provide a reusable reference to a set of data, but do not physically store any data. Materialized views are defined using SQL, like a regular view, but physically store the data which BigQuery uses to improve performance. For further comparison, see materialized views features.

## Introduction to materialized views ([link](https://cloud.google.com/bigquery/docs/materialized-views-intro))

This document provides an overview of BigQuery support for materialized views. Before you read this document, familiarize yourself with BigQuery and BigQuery's logical views.

In BigQuery, materialized views are precomputed views that periodically cache the results of a query for increased performance and efficiency. BigQuery leverages precomputed results from materialized views and whenever possible reads only changes from the base tables to compute up-to-date results. Materialized views can be queried directly or can be used by the BigQuery optimizer to process queries to the base tables.

Queries that use materialized views are generally faster and consume fewer resources than queries that retrieve the same data only from the base tables. Materialized views can significantly improve the performance of workloads that have the characteristic of common and repeated queries.

### Limitations of materialized views over BigLake tables

    Partitioning of the materialized view is not supported. The base tables can use hive partitioning but the materialized view storage cannot be partitioned in BigLake tables. This means that any deletion in a base table causes a full refresh of the materialized view. For more details see Incremental updates.
    The -max_staleness option value of the materialized view must be greater than that of the BigLake base table.
    Joins between BigQuery managed tables and BigLake tables are not supported in a single materialized view definition.

## External tables ([link](https://cloud.google.com/bigquery/docs/tables-intro#external_tables))

External tables are stored outside out of BigQuery storage and refer to data that's stored outside of BigQuery. For more information, see Introduction to external data sources. External tables include the following types:

    BigLake tables, which reference structured data stored in data stores such as Cloud Storage, Amazon Simple Storage Service (Amazon S3), and Azure Blob Storage. These tables let you enforce fine-grained security at the table level.

    For information about how to create BigLake tables, see the following topics:
        Cloud Storage
        Amazon S3
        Blob Storage

    Object tables, which reference unstructured data stored in data stores such as Cloud Storage.

    For information about how to create object tables, see Create object tables.

    Non-BigLake external tables, which reference structured data stored in data stores such as Cloud Storage, Google Drive, and Bigtable. Unlike BigLake tables, these tables don't let you enforce fine-grained security at the table level.

    For information about how to create non-BigLake external tables, see the following topics:
        Cloud Storage
        Google Drive
        Bigtable


## Introduction to BigLake external tables ([link](https://cloud.google.com/bigquery/docs/biglake-intro))

This document provides an overview of BigLake and assumes familiarity with database tables and Identity and Access Management (IAM). To query data stored in the supported data stores, you must first create BigLake tables and then query them using GoogleSQL syntax:

    Create Cloud Storage BigLake tables and then query.
    Create Amazon S3 BigLake tables and then query.
    Create Azure Blob Storage BigLake tables and then query.

You can also upgrade an external table to BigLake. For more information, see Upgrade an external table to BigLake.

BigLake tables let you query structured data in external data stores with access delegation. Access delegation decouples access to the BigLake table from access to the underlying data store. An external connection associated with a service account is used to connect to the data store. Because the service account handles retrieving data from the data store, you only have to grant users access to the BigLake table. This lets you enforce fine-grained security at the table level, including row-level and column-level security. For BigLake tables based on Cloud Storage, you can also use dynamic data masking. To learn more about multi-cloud analytic solutions using BigLake tables with Amazon S3 or Blob Storage data, see BigQuery Omni.

BigLake tables support the following formats:

    Avro
    CSV
    Delta Lake
    Iceberg
    JSON
    ORC
    Parquet

## Introduction to partitioned tables ([link](https://cloud.google.com/bigquery/docs/partitioned-tables))

A partitioned table is divided into segments, called partitions, that make it easier to manage and query your data. By dividing a large table into smaller partitions, you can improve query performance and control costs by reducing the number of bytes read by a query. You partition tables by specifying a partition column which is used to segment the table.

If a query uses a qualifying filter on the value of the partitioning column, BigQuery can scan the partitions that match the filter and skip the remaining partitions. This process is called pruning.

In a partitioned table, data is stored in physical blocks, each of which holds one partition of data. Each partitioned table maintains various metadata about the sort properties across all operations that modify it. The metadata lets BigQuery more accurately estimate a query cost before the query is run.

### When to use partitioning

Consider partitioning a table in the following scenarios:

    You want to improve the query performance by only scanning a portion of a table.
    Your table operation exceeds a standard table quota and you can scope the table operations to specific partition column values allowing higher partitioned table quotas.
    You want to determine query costs before a query runs. BigQuery provides query cost estimates before the query is run on a partitioned table. Calculate a query cost estimate by pruning a partitioned table, then issuing a query dry run to estimate query costs.
    You want any of the following partition-level management features:
        Set a partition expiration time to automatically delete entire partitions after a specified period of time.
        Write data to a specific partition using load jobs without affecting other partitions in the table.
        Delete specific partitions without scanning the entire table.

Consider clustering a table instead of partitioning a table in the following circumstances:

    You need more granularity than partitioning allows.
    Your queries commonly use filters or aggregation against multiple columns.
    The cardinality of the number of values in a column or group of columns is large.
    You don't need strict cost estimates before query execution.
    Partitioning results in a small amount of data per partition (approximately less than 10 GB). Creating many small partitions increases the table's metadata, and can affect metadata access times when querying the table.
    Partitioning results in a large number of partitions, exceeding the limits on partitioned tables.
    Your DML operations frequently modify (for example, every few minutes) most partitions in the table.

In such cases, table clustering lets you accelerate queries by clustering data in specific columns based on user-defined sort properties.

You can also combine clustering and table partitioning to achieve finer-grained sorting. For more information about this approach, see Combining clustered and partitioning tables.

### Types of partitioning

- Integer range partitioning
- Time-unit column partitioning
- Ingestion time partitioning
- Select daily, hourly, monthly, or yearly partitioning


## Introduction to clustered tables ([link](https://cloud.google.com/bigquery/docs/clustered-tables))

Clustered tables in BigQuery are tables that have a user-defined column sort order using clustered columns. Clustered tables can improve query performance and reduce query costs.

In BigQuery, a clustered column is a user-defined table property that sorts storage blocks based on the values in the clustered columns. The storage blocks are adaptively sized based on the size of the table. Colocation occurs at the level of the storage blocks, and not at the level of individual rows; for more information on colocation in this context, see Clustering.

A clustered table maintains the sort properties in the context of each operation that modifies it. Queries that filter or aggregate by the clustered columns only scan the relevant blocks based on the clustered columns, instead of the entire table or table partition. As a result, BigQuery might not be able to accurately estimate the bytes to be processed by the query or the query costs, but it attempts to reduce the total bytes at execution.

### When to use clustering

Clustering addresses how a table is stored so it's generally a good first option for improving query performance. You should therefore always consider clustering given the following advantages it provides:

    Unpartitioned tables larger than 64 MB are likely to benefit from clustering. Similarly, table partitions larger than 64 MB are also likely to benefit from clustering. Clustering smaller tables or partitions is possible, but the performance improvement is usually negligible.
    If your queries commonly filter on particular columns, clustering accelerates queries because the query only scans the blocks that match the filter.
    If your queries filter on columns that have many distinct values (high cardinality), clustering accelerates these queries by providing BigQuery with detailed metadata for where to get input data.
    Clustering enables your table's underlying storage blocks to be adaptively sized based on the size of the table.

You might consider partitioning your table in addition to clustering. In this approach, you first segment data into partitions, and then you cluster the data within each partition by the clustering columns. Consider this approach in the following circumstances:

    You need a strict query cost estimate before you run a query. The cost of queries over clustered tables can only be determined after the query is run. Partitioning provides granular query cost estimates before you run a query.
    Partitioning your table results in an average partition size of at least 10 GB per partition. Creating many small partitions increases the table's metadata, and can affect metadata access times when querying the table.
    You need to continually update your table but still want to take advantage of long-term storage pricing. Partitioning enables each partition to be considered separately for eligibility for long term pricing. If your table is not partitioned, then your entire table must not be edited for 90 consecutive days to be considered for long term pricing.

For more information, see Combine clustered and partitioned tables.




