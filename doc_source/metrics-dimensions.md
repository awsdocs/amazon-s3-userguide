# Metrics and dimensions<a name="metrics-dimensions"></a>

The storage metrics and dimensions that Amazon S3 sends to CloudWatch are listed below\.

**Topics**
+ [Amazon S3 daily storage metrics for buckets in CloudWatch](#s3-cloudwatch-metrics)
+ [Amazon S3 request metrics in CloudWatch](#s3-request-cloudwatch-metrics)
+ [Amazon S3 replication metrics in CloudWatch](#s3-cloudwatch-replication-metrics)
+ [Amazon S3 Storage Lens metrics in CloudWatch](#storage-lens-metrics-cloudwatch-publish)
+ [Amazon S3 on Outposts metrics in CloudWatch](#s3-outposts-cloudwatch-metrics)
+ [Amazon S3 dimensions in CloudWatch](#s3-cloudwatch-dimensions)
+ [Amazon S3 Storage Lens dimensions in CloudWatch](#storage-lens-dimensions)

## Amazon S3 daily storage metrics for buckets in CloudWatch<a name="s3-cloudwatch-metrics"></a>

The `AWS/S3` namespace includes the following daily storage metrics for buckets\.


| Metric | Description | 
| --- | --- | 
| BucketSizeBytes |  The amount of data in bytes stored in a bucket in the STANDARD storage class, INTELLIGENT\_TIERING storage class, Standard\-Infrequent Access \(STANDARD\_IA\) storage class, OneZone\-Infrequent Access \(ONEZONE\_IA\), Reduced Redundancy Storage \(RRS\) class, S3 Glacier Instant Retrieval storage class, Deep Archive Storage \(S3 Glacier Deep Archive\) class or, S3 Glacier Flexible Retrieval \(GLACIER\) storage class\. This value is calculated by summing the size of all objects and metadata in the bucket \(both current and noncurrent objects\), including the size of all parts for all incomplete multipart uploads to the bucket\.  Valid storage type filters: `StandardStorage`, `IntelligentTieringFAStorage`, `IntelligentTieringIAStorage`, `IntelligentTieringAAStorage`, `IntelligentTieringAIAStorage`, `IntelligentTieringDAAStorage`, `StandardIAStorage`, `StandardIASizeOverhead`, `StandardIAObjectOverhead`, `OneZoneIAStorage`, `OneZoneIASizeOverhead`, `ReducedRedundancyStorage`, `GlacierInstantRetrievalStorage`, `GlacierStorage`, `GlacierStagingStorage`, `GlacierObjectOverhead`, `GlacierS3ObjectOverhead`, `DeepArchiveStorage`, `DeepArchiveObjectOverhead`, `DeepArchiveS3ObjectOverhead` and, `DeepArchiveStagingStorage` \(see the `StorageType` dimension\)  Units: Bytes Valid statistics: Average  | 
| NumberOfObjects |  The total number of objects stored in a bucket for all storage classes\. This value is calculated by counting all objects in the bucket \(both current and noncurrent objects\) and the total number of parts for all incomplete multipart uploads to the bucket\. Valid storage type filters: `AllStorageTypes` \(see the `StorageType` dimension\) Units: Count Valid statistics: Average  | 

## Amazon S3 request metrics in CloudWatch<a name="s3-request-cloudwatch-metrics"></a>

The `AWS/S3` namespace includes the following request metrics\.


| Metric | Description | 
| --- | --- | 
| AllRequests |  The total number of HTTP requests made to an Amazon S3 bucket, regardless of type\. If you're using a metrics configuration with a filter, then this metric only returns the HTTP requests that meet the filter's requirements\. Units: Count Valid statistics: Sum  | 
| GetRequests |  The number of HTTP GET requests made for objects in an Amazon S3 bucket\. This doesn't include list operations\. Units: Count Valid statistics: Sum  Paginated list\-oriented requests, like [List Multipart Uploads](https://docs.aws.amazon.com/AmazonS3/latest/API/mpUploadListMPUpload.html), [List Parts](https://docs.aws.amazon.com/AmazonS3/latest/API/mpUploadListParts.html), [Get Bucket Object versions](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketGETVersion.html), and others, are not included in this metric\.   | 
| PutRequests |  The number of HTTP PUT requests made for objects in an Amazon S3 bucket\. Units: Count Valid statistics: Sum  | 
| DeleteRequests |  The number of HTTP DELETE requests made for objects in an Amazon S3 bucket\. This also includes [Delete Multiple Objects](https://docs.aws.amazon.com/AmazonS3/latest/API/multiobjectdeleteapi.html) requests\. This metric shows the number of requests, not the number of objects deleted\. Units: Count Valid statistics: Sum  | 
| HeadRequests |  The number of HTTP HEAD requests made to an Amazon S3 bucket\. Units: Count Valid statistics: Sum  | 
| PostRequests |  The number of HTTP POST requests made to an Amazon S3 bucket\. Units: Count Valid statistics: Sum  [Delete Multiple Objects](https://docs.aws.amazon.com/AmazonS3/latest/API/multiobjectdeleteapi.html) and [SELECT Object Content](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectSELECTContent.html) requests are not included in this metric\.    | 
| SelectRequests |  The number of Amazon S3 [SELECT Object Content](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectSELECTContent.html) requests made for objects in an Amazon S3 bucket\.  Units: Count Valid statistics: Sum  | 
| SelectBytesScanned |  The number of bytes of data scanned with Amazon S3 [SELECT Object Content](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectSELECTContent.html) requests in an Amazon S3 bucket\.   Units: Bytes  Valid statistics: Average \(bytes per request\), Sum \(bytes per period\), Sample Count, Min, Max \(same as p100\), any percentile between p0\.0 and p99\.9  | 
| SelectBytesReturned |  The number of bytes of data returned with Amazon S3 [SELECT Object Content](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectSELECTContent.html) requests in an Amazon S3 bucket\.   Units: Bytes  Valid statistics: Average \(bytes per request\), Sum \(bytes per period\), Sample Count, Min, Max \(same as p100\), any percentile between p0\.0 and p99\.9  | 
| ListRequests |  The number of HTTP requests that list the contents of a bucket\. Units: Count Valid statistics: Sum  | 
| BytesDownloaded |  The number of bytes downloaded for requests made to an Amazon S3 bucket, where the response includes a body\. Units: Bytes Valid statistics: Average \(bytes per request\), Sum \(bytes per period\), Sample Count, Min, Max \(same as p100\), any percentile between p0\.0 and p99\.9  | 
| BytesUploaded |  The number of bytes uploaded that contain a request body, made to an Amazon S3 bucket\. Units: Bytes Valid statistics: Average \(bytes per request\), Sum \(bytes per period\), Sample Count, Min, Max \(same as p100\), any percentile between p0\.0 and p99\.9  | 
| 4xxErrors |  The number of HTTP 4xx client error status code requests made to an Amazon S3 bucket with a value of either 0 or 1\. The `average` statistic shows the error rate, and the `sum` statistic shows the count of that type of error, during each period\. Units: Count Valid statistics: Average \(reports per request\), Sum \(reports per period\), Min, Max, Sample Count  | 
| 5xxErrors |  The number of HTTP 5xx server error status code requests made to an Amazon S3 bucket with a value of either 0 or 1\. The `average` statistic shows the error rate, and the `sum` statistic shows the count of that type of error, during each period\. Units: Counts Valid statistics: Average \(reports per request\), Sum \(reports per period\), Min, Max, Sample Count  | 
| FirstByteLatency |  The per\-request time from the complete request being received by an Amazon S3 bucket to when the response starts to be returned\. Units: Milliseconds Valid statistics: Average, Sum, Min, Max\(same as p100\), Sample Count, any percentile between p0\.0 and p100  | 
| TotalRequestLatency |  The elapsed per\-request time from the first byte received to the last byte sent to an Amazon S3 bucket\. This includes the time taken to receive the request body and send the response body, which is not included in `FirstByteLatency`\. Units: Milliseconds Valid statistics: Average, Sum, Min, Max\(same as p100\), Sample Count, any percentile between p0\.0 and p100  | 

## Amazon S3 replication metrics in CloudWatch<a name="s3-cloudwatch-replication-metrics"></a>

You can monitor the progress of replication with S3 replication metrics by tracking bytes pending, operations pending, and replication latency\. For more information, see [Monitoring progress with replication metrics](https://docs.aws.amazon.com/AmazonS3/latest/dev/replication-metrics.html)\.

**Note**  
You can enable alarms for your replication metrics on Amazon CloudWatch\. When you set up alarms for your replication metrics, set the **Missing data treatment** field to **Treat missing data as ignore \(maintain the alarm state\)**\.


| Metric | Description | 
| --- | --- | 
| ReplicationLatency |  The maximum number of seconds by which the replication destination Region is behind the source Region for a given replication rule\.  Units: Seconds Valid statistics: Max  | 
| BytesPendingReplication |  The total number of bytes of objects pending replication for a given replication rule\. Units: Bytes Valid statistics: Max  | 
| OperationsPendingReplication |  The number of operations pending replication for a given replication rule\. Units: Counts Valid statistics: Max  | 

## Amazon S3 Storage Lens metrics in CloudWatch<a name="storage-lens-metrics-cloudwatch-publish"></a>

You can publish S3 Storage Lens usage and activity metrics to Amazon CloudWatch to create a unified view of your operational health in CloudWatch [dashboards](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Dashboards.html)\. S3 Storage Lens metrics are published to the `AWS/S3/Storage-Lens` namespace in CloudWatch\. The CloudWatch publishing option is available for S3 Storage Lens dashboards upgraded to advanced metrics and recommendations\.

For a list of S3 Storage Lens metrics published to CloudWatch, see [Amazon S3 Storage Lens metrics glossary](storage_lens_metrics_glossary.md)\. For a complete list of dimensions, see [Dimensions](storage-lens-cloudwatch-metrics-dimensions.md#storage-lens-cloudwatch-dimensions)\.

## Amazon S3 on Outposts metrics in CloudWatch<a name="s3-outposts-cloudwatch-metrics"></a>

The `S3Outposts` namespace includes the following metrics for Amazon S3 on Outposts buckets\. You can monitor the total number of S3 on Outposts bytes provisioned, the total free bytes available for objects, and the total size of all objects for a given bucket\. 

**Note**  
S3 on Outposts supports only the following metrics, and no other Amazon S3 metrics\.  
Because S3 on Outposts has fixed capacity, you can create CloudWatch alerts that alert you when your storage utilization exceeds a certain threshold\.


| Metric | Description | 
| --- | --- | 
| OutpostTotalBytes |  The total provisioned capacity in bytes for an Outpost\.  Units: Bytes Period: 5 minutes  | 
| OutpostFreeBytes |  The count of free bytes available on an Outpost to store customer data\. Units: Bytes Period: 5 minutes  | 
| BucketUsedBytes |  The total size of all objects for the given bucket\. Units: Counts Period: 5 minutes  | 
| AccountUsedBytes |  The total size of all objects for the specified Outposts account\.  Units: Bytes Period: 5 minutes  | 

## Amazon S3 dimensions in CloudWatch<a name="s3-cloudwatch-dimensions"></a>

The following dimensions are used to filter Amazon S3 metrics\.


|  Dimension  |  Description  | 
| --- | --- | 
|  BucketName  |  This dimension filters the data that you request for the identified bucket only\.  | 
|  StorageType  |  This dimension filters the data that you have stored in a bucket by the following types of storage:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/metrics-dimensions.html)  | 
| FilterId | This dimension filters metrics configurations that you specify for request metrics on a bucket, for example, a prefix or a tag\. You specify a filter ID when you create a metrics configuration\. For more information, see [Creating a metrics configuration](https://docs.aws.amazon.com/AmazonS3/latest/userguide/metrics-configurations.html)\. | 

## Amazon S3 Storage Lens dimensions in CloudWatch<a name="storage-lens-dimensions"></a>

For a list of dimensions used to filter S3 Storage Lens metrics in CloudWatch, see [Dimensions](storage-lens-cloudwatch-metrics-dimensions.md#storage-lens-cloudwatch-dimensions)\.