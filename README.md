# Google Cloud Professional Data Engineer Certification: Exam Prep Guide

- Find all the information about the Google Cloud Professional Data Engineer certification [here](https://cloud.google.com/learn/certification/data-engineer).

- Check [exam guide](https://services.google.com/fh/files/misc/professional_data_engineer_exam_guide_english.pdf).

- The official Google Cloud [docs](https://cloud.google.com/docs).

- The Data Engineer Learning Path on [Google Cloud Skills Boost](https://www.cloudskillsboost.google/paths/16).

-----------------------

❗ This RP contains my personal notes that helped me to prepare for the PDE exam.

- [Cloud Storage](https://github.com/janaom/gcp-professional-data-engineer-exam-prep-guide/blob/main/README.md#cloud-storage)
- [File Formats](https://github.com/janaom/gcp-professional-data-engineer-exam-prep-guide/blob/main/README.md#file-formats)
- [Analytics Hub](https://github.com/janaom/gcp-professional-data-engineer-exam-prep-guide/blob/main/README.md#analytics-hub-link)
- [Bigtable](https://github.com/janaom/gcp-professional-data-engineer-exam-prep-guide/blob/main/README.md#bigtable-link)
- [Spanner](https://github.com/janaom/gcp-professional-data-engineer-exam-prep-guide/blob/main/README.md#spanner-link)
- [BigQuery](https://github.com/janaom/gcp-professional-data-engineer-exam-prep-guide/blob/main/README.md#bigquery-link)
- [Dataflow](https://github.com/janaom/gcp-professional-data-engineer-exam-prep-guide/blob/main/README.md#dataflow-link)
- [Dataplex](https://github.com/janaom/gcp-professional-data-engineer-exam-prep-guide/blob/main/README.md#dataplex-link)
- [Data Catalog](https://github.com/janaom/gcp-professional-data-engineer-exam-prep-guide/blob/main/README.md#data-catalog-link)
- [Pub/Sub](https://github.com/janaom/gcp-professional-data-engineer-exam-prep-guide/blob/main/README.md#pubsub-link)
- [Dataform](https://github.com/janaom/gcp-professional-data-engineer-exam-prep-guide/blob/main/README.md#dataform-link)
- [Datastream](https://github.com/janaom/gcp-professional-data-engineer-exam-prep-guide/blob/main/README.md#datastream-link)
- [Data Fusion](https://github.com/janaom/gcp-professional-data-engineer-exam-prep-guide/blob/main/README.md#data-fusion-link)
- [Dataproc](https://github.com/janaom/gcp-professional-data-engineer-exam-prep-guide/blob/main/README.md#dataproc-link)
- [Memorystore for Redis](https://github.com/janaom/gcp-professional-data-engineer-exam-prep-guide/blob/main/README.md#memorystore-for-redis-link)
- [Dataprep](https://github.com/janaom/gcp-professional-data-engineer-exam-prep-guide/blob/main/README.md#dataprep-link)
- [Security](https://github.com/janaom/gcp-professional-data-engineer-exam-prep-guide/blob/main/README.md#security)
- [Cloud SQL](https://github.com/janaom/gcp-professional-data-engineer-exam-prep-guide/blob/main/README.md#migrationtransfer-options)
- [Migration/transfer options](https://github.com/janaom/gcp-professional-data-engineer-exam-prep-guide/blob/main/README.md#migrationtransfer-options)

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

## Understand slots ([link](https://cloud.google.com/bigquery/docs/slots?hl=en))

A BigQuery slot is a virtual CPU used by BigQuery to execute SQL queries. During the query execution, BigQuery automatically calculates how many slots a query requires, depending on the query size and complexity.

You have a choice of using an on-demand pricing model or a capacity-based pricing model. Both models use slots for data processing. With a capacity-based model, you can pay for dedicated or autoscaled query processing capacity. The capacity-based model gives you explicit control over slots and analytics capacity, whereas the on-demand model does not.

Customers on the capacity-based pricing model explicitly choose how many slots to reserve. Your queries run within that capacity, and you pay for that capacity continuously every second it's deployed. For example, if you purchase 2,000 BigQuery slots, your queries in aggregate are limited to using 2,000 virtual CPUs at any given time. You have this capacity until you delete it, and you pay for 2,000 slots until you delete them.

Projects on the BigQuery on-demand pricing model are subject to per-project slot quota with transient burst capability. Most users on the on-demand model find the default slot capacity more than sufficient. Depending on the workload, access to more slots improves query performance. To check how many slots your account uses, see BigQuery monitoring.

# Dataflow ([link](https://cloud.google.com/dataflow/docs))

Dataflow is a managed service for executing a wide variety of data processing patterns. The documentation on this site shows you how to deploy your batch and streaming data processing pipelines using Dataflow, including directions for using service features.

The Apache Beam SDK is an open source programming model that enables you to develop both batch and streaming pipelines. You create your pipelines with an Apache Beam program and then run them on the Dataflow service. The Apache Beam documentation provides in-depth conceptual information and reference material for the Apache Beam programming model, SDKs, and other runners.

Typical use cases for Dataflow include the following ([link](https://cloud.google.com/dataflow/docs/overview)):

    Data movement: Ingesting data or replicating data across subsystems.
    ETL (extract-transform-load) workflows that ingest data into a data warehouse such as BigQuery.
    Powering BI dashboards.
    Applying ML in real time to streaming data.
    Processing sensor data or log data at scale.


## Programming model for Apache Beam ([link](https://cloud.google.com/dataflow/docs/concepts/beam-programming-model))

Dataflow is based on the open-source Apache Beam project. This document describes the Apache Beam programming model.

Apache Beam is an open source, unified model for defining both batch and streaming pipelines. The Apache Beam programming model simplifies the mechanics of large-scale data processing. Using one of the Apache Beam SDKs, you build a program that defines the pipeline. Then, you execute the pipeline on a specific platform such as Dataflow. This model lets you concentrate on the logical composition of your data processing job, rather than managing the orchestration of parallel processing.

Apache Beam insulates you from the low-level details of distributed processing, such as coordinating individual workers, sharding datasets, and other such tasks. Dataflow fully manages these low-level details.

A pipeline is a graph of transformations that are applied to collections of data. In Apache Beam, a collection is called a PCollection, and a transform is called a PTransform. A PCollection can be bounded or unbounded. A bounded PCollection has a known, fixed size, and can be processed using a batch pipeline. Unbounded PCollections must use a streaming pipeline, because the data is processed as it arrives.

Apache Beam provides connectors to read from and write to different systems, including Google Cloud services and third-party technologies such as Apache Kafka.

### Apache Beam concepts


Pipelines

    A pipeline encapsulates the entire series of computations that are involved in reading input data, transforming that data, and writing output data. The input source and output sink can be the same type or of different types, letting you convert data from one format to another. Apache Beam programs start by constructing a Pipeline object, and then using that object as the basis for creating the pipeline's datasets. Each pipeline represents a single, repeatable job.
    
PCollection

    A PCollection represents a potentially distributed, multi-element dataset that acts as the pipeline's data. Apache Beam transforms use PCollection objects as inputs and outputs for each step in your pipeline. A PCollection can hold a dataset of a fixed size or an unbounded dataset from a continuously updating data source.
    
Transforms

    A transform represents a processing operation that transforms data. A transform takes one or more PCollections as input, performs an operation that you specify on each element in that collection, and produces one or more PCollections as output. A transform can perform nearly any kind of processing operation, including performing mathematical computations on data, converting data from one format to another, grouping data together, reading and writing data, filtering data to output only the elements you want, or combining data elements into single values.
    
ParDo

    ParDo is the core parallel processing operation in the Apache Beam SDKs, invoking a user-specified function on each of the elements of the input PCollection. ParDo collects the zero or more output elements into an output PCollection. The ParDo transform processes elements independently and possibly in parallel.
    
Pipeline I/O

    Apache Beam I/O connectors let you read data into your pipeline and write output data from your pipeline. An I/O connector consists of a source and a sink. All Apache Beam sources and sinks are transforms that let your pipeline work with data from several different data storage formats. You can also write a custom I/O connector.
    
Aggregation

    Aggregation is the process of computing some value from multiple input elements. The primary computational pattern for aggregation in Apache Beam is to group all elements with a common key and window. Then, it combines each group of elements using an associative and commutative operation.
    
User-defined functions (UDFs)

    Some operations within Apache Beam allow executing user-defined code as a way of configuring the transform. For ParDo, user-defined code specifies the operation to apply to every element, and for Combine, it specifies how values should be combined. A pipeline might contain UDFs written in a different language than the language of your runner. A pipeline might also contain UDFs written in multiple languages.
    
Runner

    Runners are the software that accepts a pipeline and executes it. Most runners are translators or adapters to massively parallel big-data processing systems. Other runners exist for local testing and debugging.
    
Source

    A transform that reads from an external storage system. A pipeline typically reads input data from a source. The source has a type, which may be different from the sink type, so you can change the format of data as it moves through the pipeline.
    
Sink

    A transform that writes to an external data storage system, like a file or a database.
    
TextIO

    A PTransform for reading and writing text files. The TextIO source and sink support files compressed with gzip and bzip2. The TextIO input source supports JSON. However, for the Dataflow service to be able to parallelize input and output, your source data must be delimited with a line feed. You can use a regular expression to target specific files with the TextIO source. Dataflow supports general wildcard patterns. Your glob expression can appear anywhere in the path. However, Dataflow does not support recursive wildcards (**). 

Event time

    The time a data event occurs, determined by the timestamp on the data element itself. This contrasts with the time the actual data element gets processed at any stage in the pipeline.
    
Windowing

    Windowing enables grouping operations over unbounded collections by dividing the collection into windows of finite collections according to the timestamps of the individual elements. A windowing function tells the runner how to assign elements to an initial window, and how to merge windows of grouped elements. Apache Beam lets you define different kinds of windows or use the predefined windowing functions.
    
Watermarks

    Apache Beam tracks a watermark, which is the system's notion of when all data in a certain window can be expected to have arrived in the pipeline. Apache Beam tracks a watermark because data is not guaranteed to arrive in a pipeline in time order or at predictable intervals. In addition, it's not guaranteed that data events will appear in the pipeline in the same order that they were generated.
    
Trigger

    Triggers determine when to emit aggregated results as data arrives. For bounded data, results are emitted after all of the input has been processed. For unbounded data, results are emitted when the watermark passes the end of the window, indicating that the system believes all input data for that window has been processed. Apache Beam provides several predefined triggers and lets you combine them. 

### Decide how to join datasets ([link](https://cloud.google.com/dataflow/docs/guides/pipeline-best-practices#joins))

Joining datasets is a common use case for data pipelines. You can use side inputs or the CoGroupByKey transform to perform joins in your pipeline. Each has benefits and downsides.

Side inputs provide a flexible way to solve common data processing problems, such as data enrichment and keyed lookups. Unlike PCollection objects, side inputs are mutable and can be determined at runtime. For example, the values in a side input might be computed by another branch in your pipeline or determined by calling a remote service.

Dataflow supports side inputs by persisting data into persistent storage, similar to a shared disk. This configuration makes the complete side input available to all workers.

However, side input sizes can be very large and might not fit into worker memory. Reading from a large side input can cause performance issues if workers need to constantly read from persistent storage.

### Use dead-letter queues for error handling ([link](https://cloud.google.com/dataflow/docs/guides/pipeline-best-practices#dead-letter-queues))

Sometimes your pipeline can't process elements. Data issues are a common cause. For example, an element that contains badly formatted JSON can cause parsing failures.

Although you can catch exceptions within the DoFn.ProcessElement method, log the error, and drop the element, this approach both loses the data and prevents the data from being inspected later for manual handling or troubleshooting.

Instead, use a pattern called a dead-letter queue (unprocessed messages queue). Catch exceptions in the DoFn.ProcessElement method and log errors. Instead of dropping the failed element, use branching outputs to write failed elements into a separate PCollection object. These elements are then written to a data sink for later inspection and handling with a separate transform.

Use Cloud Monitoring to apply different monitoring and alerting policies for your pipeline's dead-letter queue. For example, you can visualize the number and size of elements processed by your dead-letter transform and configure alerting to trigger if certain threshold conditions are met.

## Configure internet access and firewall rules ([link](https://cloud.google.com/dataflow/docs/guides/routes-firewall))

Firewall rules let you allow or deny traffic to and from your VMs. If your Dataflow jobs use Dataflow Shuffle or Streaming Engine, then you only need to ensure that firewal rules allow access to Google Cloud APIs. Otherwise, you must configure additional firewalls rules so that Dataflow VMs can send and receive network traffic on TCP port 12345 for streaming jobs and on TCP port 12346 for batch jobs. A project owner, editor, or security administrator must create necessary firewall rules in the VPC network used by your Dataflow VMs.

# Dataplex ([link](https://cloud.google.com/dataplex/docs/introduction))

Dataplex is a data fabric that unifies distributed data and automates data management and governance for that data.

Dataplex lets you do the following:

    Build a domain-specific data mesh across data that's stored in multiple Google Cloud projects, without any data movement.
    Consistently govern and monitor data with a single set of permissions.
    Discover and curate metadata across various silos using catalog capabilities. For more information, see Dataplex Catalog overview.
    Securely query metadata by using BigQuery and open source tools, such as Spark SQL, Presto, and HiveQL.
    Run data quality and data lifecycle management tasks, including serverless Spark tasks.
    (Deprecated) Explore data using fully managed, serverless Spark environments with access to notebooks and Spark SQL queries.

### Why use Dataplex?

Enterprises have data that's distributed across data lakes, data warehouses, and data marts. Using Dataplex, you can do the following:

    Discover data
    Curate data
    Unify data without any data movement
    Organize data based on your business needs
    Centrally manage, monitor, and govern data

Dataplex lets you standardize and unify metadata, security policies, governance, classification, and data lifecycle management across this distributed data.

### How Dataplex works

Dataplex manages data in a way that doesn't require data movement or duplication. As you identify new data sources, Dataplex harvests the metadata for both structured and unstructured data, using built-in data quality checks to enhance integrity.

Dataplex automatically registers all metadata in a unified metastore. You can access data and metadata using various services and tools including the following:

    Google Cloud services, such as BigQuery, Dataproc Metastore, Data Catalog.
    Open source tools, such as Apache Spark and Presto.

### Terminology

Dataplex abstracts away the underlying data storage systems, by using the following constructs:

    Lake: A logical construct representing a data domain or business unit. For example, to organize data based on group usage, you can set up a lake for each department (for example, retail, sales, finance).

    Zone: A subdomain within a lake, which is useful to categorize data by the following:
        Stage: for example, landing, raw, curated data analytics, and curated data science
        Usage: for example, data contract
        Restrictions: for example, security controls and user access levels

    Zones are of two types:

        Raw zone: contains data that is in its raw format and not subject to strict type-checking.

        Curated zone: contains data that is cleaned, formatted, and ready for analytics. The data is columnar, Hive-partitioned, and stored in Parquet, Avro, Orc files, or BigQuery tables. Data undergoes type-checking- for example, to prohibit the use of CSV files because they don't perform as well for SQL access.

    Asset: maps to data stored in either Cloud Storage or BigQuery. You can map data stored in separate Google Cloud projects as assets into a single zone.

    Entity: represents metadata for structured and semi-structured data (for example, table), and unstructured data (for example, fileset).

# Data Catalog ([link](https://cloud.google.com/data-catalog/docs/concepts/overview))

Dataplex's Data Catalog feature is a central inventory of an organization's data assets. Data Catalog automatically catalogs metadata from Google Cloud sources such as BigQuery, Vertex AI, Pub/Sub, Spanner, Bigtable, and more. Data Catalog also indexes table and fileset metadata from Cloud Storage through discovery.

You can discover data with Dataplex's governed organization-wide metadata search capability. You can further enrich metadata with critical business context, and enable lineage tracking, data profiling, data quality checks, and access control capabilities.

Using Data Catalog, organizations can achieve better data discovery, metadata management, and governance.

### Why do you need Data Catalog?

Most organizations deal with a large and growing number of data assets. Data stakeholders (consumers, producers, and administrators) within an organization face multiple challenges, including the following:

    Searching for insightful data:
        Data consumers don't know the location and origin of data. They have to navigate data "swamps".
        Data consumers don't know what data to use to get insights because most data isn't well documented and, even if documented, isn't well maintained.
        Data can't be found and is often lost when it resides only in people's minds.

    Understanding data:
        Is the data fresh, clean, validated, approved for use in production?
        Which dataset out of several duplicate sets is relevant and up-to-date?
        How does one dataset relate to another?
        Who is using the data and who is the owner?
        Who and what processes are transforming the data?

    Making data useful:

        Data producers don't have an efficient way to put forward their data for consumers. If there's no self-service, consumers may overwhelm producers. Several data engineers can't manually provide data to thousands of data analysts.

        Valuable time is lost if data consumers have to find out how to request data access, wait without a defined response time, escalate, and wait again.

Without the right tools, the challenges become a major obstacle to the efficient use of data. Data Catalog provides a centralized repository that lets organizations achieve the following:

    Gain a unified view to reduce the pain of searching for the right data.
    Support data-driven decision making and accelerate the insight time by enriching data with technical and business metadata.
    Improve data management to increase operational efficiency and productivity.
    Take ownership over the data to improve trust and confidence in it.

### How Data Catalog works

Data Catalog can catalog asset metadata from different Google Cloud systems.

You can also use Data Catalog APIs to integrate with custom data sources.

After your data is cataloged, you can add your own metadata to these assets using tags.

![dc-gcp-overview](https://github.com/user-attachments/assets/1913a2e8-1b22-4534-978a-0e1bcbd5709a)

### Automatic cataloging of assets

For a given project, Data Catalog automatically catalogs the following Google Cloud assets:

    Analytics Hub linked datasets
    BigQuery datasets, tables, models, routines, and connections
    Bigtable instances, clusters, and tables (including column family details)
    Dataplex lakes, zones, tables, and filesets
    Dataproc Metastore services, databases, and tables
    Pub/Sub topics
    Spanner instances, databases, tables, and views
    Vertex AI models, datasets, and Vertex AI Feature Store resources

In addition to cataloging assets within the project IDs for which you have metadata access, Data Catalog can catalog data stored in the BigQuery projects that contain public datasets.

### Catalog non-Google Cloud assets

To catalog metadata from non-Google Cloud systems in your organization, you can use the following:

    Community-contributed connectors to multiple popular on-premises data sources
    Manually build on the Data Catalog APIs for custom entries

### Access Data Catalog

You can access Data Catalog functionalities using:

    Dataplex in the Google Cloud console

    gcloud command-line interface (CLI)

    Data Catalog APIs

    Cloud Client Libraries

# Pub/Sub ([link](https://cloud.google.com/pubsub/docs/overview))

Pub/Sub is an asynchronous and scalable messaging service that decouples services producing messages from services processing those messages.

Pub/Sub allows services to communicate asynchronously, with latencies typically on the order of 100 milliseconds.

Pub/Sub is used for streaming analytics and data integration pipelines to load and distribute data. It's equally effective as a messaging-oriented middleware for service integration or as a queue to parallelize tasks.

Pub/Sub lets you create systems of event producers and consumers, called publishers and subscribers. Publishers communicate with subscribers asynchronously by broadcasting events, rather than by synchronous remote procedure calls (RPCs).

Publishers send events to the Pub/Sub service, without regard to how or when these events are to be processed. Pub/Sub then delivers events to all the services that react to them. In systems communicating through RPCs, publishers must wait for subscribers to receive the data. However, the asynchronous integration in Pub/Sub increases the flexibility and robustness of the overall system.

## Pull subscriptions ([link](https://cloud.google.com/pubsub/docs/pull))

In a pull subscription, a subscriber client requests messages from the Pub/Sub server.

The pull mode can use one of the two service APIs, Pull or StreamingPull. To run the chosen API, you can select a Google-provided high-level client library, or a low-level auto-generated client library. You can also choose between asynchronous and synchronous message processing.

## Push subscriptions ([link](https://cloud.google.com/pubsub/docs/push))

In push delivery, Pub/Sub initiates requests to your subscriber application to deliver messages. Messages are delivered to a publicly addressable server or a webhook, such as an HTTPS POST request.

Push subscriptions minimize dependencies on Pub/Sub-specific client libraries and authentication mechanisms. They also work well with serverless and autoscaling service technologies, such as Cloud Run functions, Cloud Run, and Google Kubernetes Engine.

# Dataform ([link](https://cloud.google.com/dataform/docs/overview))

Dataform is a service for data analysts to develop, test, version control, and schedule complex SQL workflows for data transformation in BigQuery.

Dataform lets you manage data transformation in the Extraction, Loading, and Transformation (ELT) process for data integration. After raw data is extracted from source systems and loaded into BigQuery, Dataform helps you to transform it into a well-defined, tested, and documented suite of data tables.

Dataform lets you perform the following data transformation actions:

    Develop and execute SQL workflows for data transformation.
    Collaborate with team members on SQL workflow development through Git.
    Manage a large number of tables and their dependencies.
    Declare source data and manage table dependencies.
    View a visualization of the dependency tree of your SQL workflow.
    Manage data with SQL code in a central repository.
    Reuse code with JavaScript.
    Test data correctness with quality tests on source and output tables.
    Version control SQL code.
    Document data tables inside SQL code.

# Datastream ([link](https://cloud.google.com/datastream/docs/overview))

Datastream is a serverless and easy-to-use change data capture (CDC) and replication service that lets you synchronize data reliably, and with minimal latency.

Datastream provides seamless replication of data from operational databases into BigQuery. In addition, Datastream supports writing the change event stream into Cloud Storage, and offers streamlined integration with Dataflow templates to build custom workflows for loading data into a wide range of destinations, such as Cloud SQL and Spanner. You can also use Datastream to leverage the event stream directly from Cloud Storage to realize event-driven architectures. Datastream supports Oracle, MySQL, SQL Server, and PostgreSQL (including AlloyDB for PostgreSQL) sources.

Benefits of Datastream include:

    Seamless setup of ELT (Extract, Load, Transform) pipelines for low-latency data replication to enable near real-time insights in BigQuery.
    Being serverless so there are no resources to provision or manage, and the service scales up and down automatically, as needed, with minimal downtime.
    Easy-to-use setup and monitoring experiences that achieve super-fast time-to-value.
    Integration across the best of Google Cloud data services' portfolio for data integration across Datastream, Dataflow, Pub/Sub, BigQuery, and more.
    Synchronizing and unifying data streams across heterogeneous databases and applications.
    Security, with private connectivity options and the security you expect from Google Cloud.
    Being accurate and reliable, with transparent status reporting and robust processing flexibility in the face of data and schema changes.
    Supporting multiple use cases, including analytics, database replication, and synchronization for migrations and hybrid-cloud configurations, and for building event-driven architectures.

# Data Fusion ([link](https://cloud.google.com/data-fusion/docs/concepts/overview))

Cloud Data Fusion is a fully managed, cloud-native, enterprise data integration service for quickly building and managing data pipelines. The Cloud Data Fusion web interface lets you build scalable data integration solutions. It lets you connect to various data sources, transform the data, and then transfer it to various destination systems, without having to manage the infrastructure.

An example of the pipeline ([link](https://cloud.google.com/data-fusion/docs/tutorials/targeting-campaign-pipeline))

<img width="1036" alt="complete-pipeline-studio" src="https://github.com/user-attachments/assets/e955d1c7-af31-4d0e-b8d2-912adf538f88" />

# Dataproc ([link](https://cloud.google.com/dataproc/docs/concepts/overview))

Dataproc is a managed Spark and Hadoop service that lets you take advantage of open source data tools for batch processing, querying, streaming, and machine learning. Dataproc automation helps you create clusters quickly, manage them easily, and save money by turning clusters off when you don't need them. With less time and money spent on administration, you can focus on your jobs and your data.

### Dataproc Workflow Templates ([link](https://cloud.google.com/dataproc/docs/concepts/workflows/workflow-schedule-solutions#dataproc_workflow_templates))

Dataproc Workflow templates provide a flexible and easy-to-use mechanism for managing and executing workflows. A Workflow Template is a reusable workflow configuration. It defines a graph of jobs with information on where to run those jobs.

### Graceful Decommissioning ([link](https://cloud.google.com/dataproc/docs/concepts/configuring-clusters/scaling-clusters#graceful_decommissioning))

When you downscale a cluster, work in progress may stop before completion. If you are using Dataproc v 1.2 or later, you can use Graceful Decommissioning, which incorporates Graceful Decommission of YARN Nodes to finish work in progress on a worker before it is removed from the Cloud Dataproc cluster.

# Memorystore for Redis ([link](https://cloud.google.com/memorystore/docs/redis/memorystore-for-redis-overview))

Memorystore for Redis provides a fully-managed service that is powered by the Redis in-memory data store to build application caches that provide sub-millisecond data access.

Memorystore for Redis offers several advantages over self-managed Redis:

    Deploy what fits your needs. Memorystore for Redis allows you the flexibility to choose from different service tiers and sizes that fit your performance and operational needs. With a few clicks, you have the option to deploy a Basic Tier standalone Redis instance or a Standard Tier high availability Redis instance up to 300 GB.
    
    Easily scale to get blazing speed. With Memorystore for Redis, you can easily achieve your latency and throughput targets by scaling up your Redis instances with minimal impact to your application's availability. Start with the lowest tier and smallest size, then grow your Redis instance as the needs of your application change. For applications that need scaling of read queries, you can scale the queries across five read replicas using the read endpoint.

    Highly available and more secure. Redis instances are protected from the internet using private IPs and are further secured using Identity and Access Management role-based access control and in-transit encryption. Standard high availability instances provide up to five replicas replicated across zones and provide a 99.9% availability SLA.
    
    Focus on your application. Memorystore for Redis automates the complex operational tasks that are required to deploy and manage Redis. Tasks like provisioning, replication, failover, and monitoring are all automated. Applications connect to a single endpoint, which simplifies management and operations. Additionally, integration with Cloud Monitoring makes it easy to monitor your Redis instances.

    Redis Protocol Compatible. Memorystore for Redis is fully Redis protocol compliant. You can move your applications using open source Redis to use Memorystore for Redis without any code changes. There is no need to learn new tools: all existing tools and client libraries just work.

### What it's good for

Memorystore for Redis provides a fast, in-memory store for use cases that require fast, real-time processing of data. From simple caching use cases to real time analytics, Memorystore for Redis provides the performance you need.

    Caching: Cache is an integral part of modern application architectures. Memorystore for Redis provides low latency access and high throughput for heavily accessed data, compared to accessing the data from a disk based backend store. Session management, frequently accessed queries, scripts, and pages are common examples of caching.

    Gaming: Gaming is about capturing and keeping the user's attention. One key aspect that keeps users hooked on a game is the leaderboard. Everyone wants to see how they are progressing and where they stand. Making this experience snappy is critical, and with its in-memory store and data structure like Sorted Set, Memorystore for Redis makes it easy to maintain a sorted list of scores while providing uniqueness of elements. Player Profile is another piece of information that can be accessed frequently. Redis hash makes it fast and easy to store and access profile data.

    Stream Processing: Whether processing a Twitter feed or stream of data from IoT devices, Memorystore for Redis is a perfect fit for streaming solutions. Combined with Dataflow, Memorystore for Redis provides a scalable, fast in-memory store for storing intermediate data that thousands of clients can access with very low latency.

### What is a manual failover? ([link](https://cloud.google.com/memorystore/docs/redis/about-manual-failover))

A standard tier Memorystore for Redis instance uses a replica node to back up the primary node. A normal failover occurs when the primary node becomes unhealthy, causing the replica to be designated as the new primary. A manual failover differs from a normal failover because you initiate it yourself. For more information on how Memorystore for Redis replication works, see High availability.

### Why initiate a manual failover?

Initiating a manual failover allows you to test how your application responds to a failover. This knowledge can ensure a smoother failover process if an unexpected failover occurs later on.

### Optional data protection mode

The two available data protection modes are:

    limited-data-loss mode (default).
    force-data-loss mode.

### How data protection modes work

The limited-data-loss mode minimizes data loss by verifying that the difference in data between the primary and replica is below 30 MB before initiating the failover. The offset on the primary is incremented for each byte of data that must be synchronized to its replicas. In the limited-data-loss mode, the failover will abort if the greatest offset delta between the primary and each replica is 30MB or greater. If you can tolerate more data loss and want to aggressively execute the failover, try setting the data protection mode to force-data-loss.

The force-data-loss mode employs a chain of failover strategies to aggressively execute the failover. It does not check the offset delta between the primary and replicas before initiating the failover; you can potentially lose more than 30MB of data changes.

### When to run a manual failover

Manual failovers using the default limited-data-loss protection mode only succeed if the bytes pending replication metric is less than 30MB. If you want to run a manual failover with bytes pending replication higher than 30MB, use the force-data-loss protection mode.

If you are trying to preserve as much data as possible, temporarily stop your application from writing to the Redis instance, and wait to run your manual failover until the bytes pending replication metric is as low as you deem acceptable.

# Dataprep ([link](https://docs.trifacta.com/Dataprep/en/product-overview.html)) 

Dataprep by Trifacta enables you to explore, combine, and transform diverse datasets for downstream analysis.

Within an enterprise, data required for key decisions typically resides in various silos. It comes in different formats featuring different types. It is often inconsistent. It may require refactoring in some form for different audiences. All of this work must be done before you can begin extracting information valuable to the organization. Data preparation (or data wrangling) has been a constant challenge for decades, and that challenge has only amplified as data volumes have exploded.

![uuid-ecb60b4a-391d-fe3d-08db-441dbd2a7af0](https://github.com/user-attachments/assets/5e6ebcd5-0037-405c-9d26-544b111d16f0)

In Dataprep by Trifacta the primary object that you create is the recipe. A recipe is a sequence of transformation steps that you create to transform your source dataset. When you select suggestions, choose options from the handy toolbar, or select values from a data histogram, you begin building new steps in your recipe. After selecting, you can modify them through the Transform Builder, a context panel where your configured transformation can be modified and the changes previewed before saving them.

When you finish your recipe, you run a job to generate results. A job executes your set of recipe steps on the source data, without modifying the source, for delivery to a specified output, which defines location, format, compression and other settings.

Datasets, recipes, and outputs can be grouped together into objects called flows. A flow is a unit of organization in the platform.

Depending on your product, flows can be shared between users, scheduled for automated execution, and exported and imported into the platform. In this manner, you can build and test your recipes, chain together sets of datasets and recipes in a flow, share your work with others, and operationalize your production datasets for automated execution.

# Security 

## Data Loss Prevention (DLP) ([link](https://cloud.google.com/sensitive-data-protection/docs/libraries))

Client libraries make it easier to access Google Cloud APIs from a supported language. Although you can use Google Cloud APIs directly by making raw requests to the server, client libraries provide simplifications that significantly reduce the amount of code you need to write.

Read more about the Cloud Client Libraries and the older Google API Client Libraries in Client libraries explained.

The Cloud Data Loss Prevention API (DLP API) is part of Sensitive Data Protection. Sensitive Data Protection client libraries mentioned on this page are supported on Compute Engine, App Engine - Flexible Environment, Google Kubernetes Engine, and Cloud Run functions. Sensitive Data Protection client library for Java is supported on Java 8 on App Engine standard environment.

If you are using Java 7 on App Engine standard environment, or App Engine - Standard environment with Go, PHP, or Python, use the REST Interface to access Sensitive Data Protection.

## Cloud KMS ([link](https://cloud.google.com/kms/docs/key-management-service))

Cloud Key Management Service (Cloud KMS) lets you create and manage cryptographic keys for use in compatible Google Cloud services and in your own applications. Using Cloud KMS, you can do the following:

    Generate software or hardware keys, import existing keys into Cloud KMS, or link external keys in your compatible external key management (EKM) system.
    Use customer-managed encryption keys (CMEKs) in Google Cloud products with CMEK integration. CMEK integrations use your Cloud KMS keys to encrypt or "wrap" your data encryption keys (DEKs). Wrapping DEKs with key encryption keys (KEKs) is called envelope encryption.
    Use Cloud KMS Autokey to automate provisioning and assignment. With Autokey, you don't need to provision key rings, keys, and service accounts ahead of time. Instead, they are generated on demand as part of resource creation.
    Use Cloud KMS keys for encryption and decryption operations. For example, you can use the Cloud KMS API or client libraries to use your Cloud KMS keys for client-side encryption.
    Use Cloud KMS keys to create or verify digital signatures or message authentication code (MAC) signatures.

### Google-owned and Google-managed encryption keys (Google Cloud default encryption)

By default, data at rest in Google Cloud is protected by keys in Keystore, Google Cloud's internal key management service. Keys in Keystore are managed automatically by Google Cloud, with no configuration required on your part. Most services automatically rotate keys for you. Keystore supports a primary key version and a limited number of older key versions. The primary key version is used to encrypt new data encryption keys. Older key versions can still be used to decrypt existing data encryption keys. You can't view or manage these keys or review key usage logs. Data from multiple customers might use the same key encryption key.

This default encryption uses cryptographic modules that are validated to be FIPS 140-2 Level 1 compliant.

### Customer-managed encryption keys (CMEKs) ([link](https://cloud.google.com/kms/docs/key-management-service))

Cloud KMS keys that are used to protect your resources in CMEK-integrated services are customer-managed encryption keys (CMEKs). You can own and control CMEKs, while delegating key creation and assignment tasks to Cloud KMS Autokey. To learn more about automating provisioning for CMEKs, see Cloud Key Management Service with Autokey.

You can use your Cloud KMS keys in compatible services to help you meet the following goals:

    Own your encryption keys.
    
    Control and manage your encryption keys, including choice of location, protection level, creation, access control, rotation, use, and destruction.
    
    Selectively delete data protected by your keys in the case of off-boarding or to remediate security events (crypto-shredding).
    
    Create dedicated, single-tenant keys that establish a cryptographic boundary around your data.
    
    Log administrative and data access to encryption keys.
    
    Meet current or future regulation that requires any of these goals.

When you use Cloud KMS keys with CMEK-integrated services, you can use organization policies to ensure that CMEKs are used as specified in the policies. For example, you can set an organization policy that ensures that your compatible Google Cloud resources use your Cloud KMS keys for encryption. Organization policies can also specify which project the key resources must reside in.

The features and level of protection provided depend on the protection level of the key:

- Software keys - You can generate software keys in Cloud KMS and use them in all Google Cloud locations. You can create symmetric keys with automatic rotation or asymmetric keys with manual rotation. Customer-managed software keys use FIPS 140-2 Level 1 validated software cryptography modules. You also have control over the rotation period, Identity and Access Management (IAM) roles and permissions, and organization policies that govern your keys. You can use your software keys with many compatible Google Cloud resources.

- Imported software keys - You can import software keys that you created elsewhere for use in Cloud KMS. You can import new key versions to manually rotate imported keys. You can use IAM roles and permissions and organization policies to govern usage of your imported keys.

- Hardware keys and Cloud HSM - You can generate hardware keys in a cluster of FIPS 140-2 Level 3 Hardware Security Modules (HSMs). You have control over the rotation period, IAM roles and permissions, and organization policies that govern your keys. When you create HSM keys using Cloud HSM, Google Cloud manages the HSM clusters so you don't have to. You can use your HSM keys with many compatible Google Cloud resources—the same services that support software keys. For the highest level of security compliance, use hardware keys.

- External keys and Cloud EKM - You can use keys that reside in an external key manager (EKM). Cloud EKM lets you use keys held in a supported key manager to secure your Google Cloud resources. You connect to your EKM over a Virtual Private Cloud (VPC). Some Google Cloud services that support Cloud KMS keys don't support Cloud EKM keys.

To learn more about which Cloud KMS locations support which protection levels, see Cloud KMS locations.

# Cloud SQL ([link](https://cloud.google.com/sql/docs/introduction))

Cloud SQL is a fully managed relational database service for MySQL, PostgreSQL, and SQL Server. This frees you from database administration tasks so that you have more time to manage your data.

This page discusses basic concepts and terminology for Cloud SQL, which provides SQL data storage for Google Cloud. For a more in-depth explanation of key concepts, see the key terms and features pages. For information about how Cloud SQL databases compare with one another, see Cloud SQL feature support by database engine.

### Connect to a Cloud SQL managed database

Connecting to a Cloud SQL managed database is similar to connecting to a self-managed database. Depending on how you configure it, your Cloud SQL instance has a public IP address (which can be accessed from outside of Google Cloud, using the internet), or a private IP address (which can only be accessed through a Virtual Private Cloud (VPC) network). In addition, Cloud SQL provides different authorization options to control who is allowed to connect to your instance, such as the Cloud SQL Auth Proxy.

### About replication in Cloud SQL ([link](https://cloud.google.com/sql/docs/mysql/replication))

Cloud SQL supports the following types of replicas:

    Read replicas
    Cross-region read replicas
    Cascading read replicas
    External read replicas
    Cloud SQL replicas, when replicating from an external server

# Migration/transfer options 

## Transfer Appliance ([link](https://cloud.google.com/transfer-appliance/docs/4.0/overview))

Transfer Appliance is a high-capacity storage device that enables you to transfer and securely ship your data to a Google upload facility, where we upload your data to Cloud Storage. For Transfer Appliance capacities and requirements, refer to the Specifications page.

With a typical network bandwidth of 100 Mbps, 300 terabytes of data takes about 9 months to upload. However, with Transfer Appliance, you can receive the appliance and capture 300 terabytes of data in under 25 days. Your data can be accessed in Cloud Storage within another 25 days, all without consuming any outbound network bandwidth.

## Storage Transfer Service ([link](https://cloud.google.com/storage-transfer/docs/overview))

Storage Transfer Service enables seamless data movement across object and file storage systems, including:

    Amazon S3, Azure Blob Storage, or Cloud Storage to Cloud Storage
    On-premises storage to Cloud Storage, or Cloud Storage to on-premises
    Between on-premises storage systems
    From publicly-accessible URLs to Cloud Storage
    From HDFS to Cloud Storage
    Storage Transfer Service is optimized for transfers involving more than 1TiB of data. For smaller transfers, see our recommendations.

With Storage Transfer Service, you can:

    Automate data transfers: Eliminate the need for manual processes and custom scripts.
    Transfer data at scale: Move petabytes of data quickly and reliably.
    Optimize network performance: Choose between Google-managed transfers for simplicity or self-hosted agents for granular control over network routing and bandwidth consumption.
    Support diverse storage systems: Transfer data seamlessly between cloud providers and on-premises environments.

## BigQuery Data Transfer Service ([link](https://cloud.google.com/bigquery/docs/dts-introduction))

The BigQuery Data Transfer Service automates data movement into BigQuery on a scheduled, managed basis. Your analytics team can lay the foundation for a BigQuery data warehouse without writing a single line of code.

You can access the BigQuery Data Transfer Service using the:

    Google Cloud console
    bq command-line tool
    BigQuery Data Transfer Service API
    
After you configure a data transfer, the BigQuery Data Transfer Service automatically loads data into BigQuery on a regular basis. You can also initiate data backfills to recover from any outages or gaps. You cannot use the BigQuery Data Transfer Service to transfer data out of BigQuery.

In addition to loading data into BigQuery, BigQuery Data Transfer Service is used for two BigQuery operations: dataset copies and scheduled queries.

