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
