# Amazon S3 Storage Lens metrics glossary<a name="storage_lens_metrics_glossary"></a>

By default, all dashboards are configured with *free metrics*, which include *usage metrics* aggregated down to the bucket level, and data is available for queries for 14 days\. This means that you can see all the usage metrics that S3 Storage Lens aggregates, and your metrics are available for queries 14 days from the day the data was aggregated\. 

*Advanced metrics and recommendations* include *usage* metrics that can be aggregated by prefix and *activity* metrics\. Activity metrics can be aggregated by bucket\. Data is available for queries for 15 months\. There are additional charges when you use S3 Storage Lens with advanced metrics and recommendations\. For more information, see [ Amazon S3 pricing](http://aws.amazon.com/s3/pricing/)\.

**Amazon CloudWatch publishing option**  
You can publish S3 Storage Lens usage and activity metrics to Amazon CloudWatch to create a unified view of your operational health in CloudWatch [dashboards](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Dashboards.html)\. CloudWatch publishing is an option that you can enable in *Advanced metrics and recommendations*\. Storage Lens metrics are published to the `AWS/S3/Storage-Lens` namespace\. For a list of S3 Storage Lens dimensions in CloudWatch, see [Dimensions](storage-lens-cloudwatch-metrics-dimensions.md#storage-lens-cloudwatch-dimensions)\. For a list of S3 Storage Lens metrics in CloudWatch, see the **Metric name in CloudWatch and export** column in the following table\.

**Derived metrics**  
Derived metrics are not available for the metrics export and CloudWatch publishing option\. However, you can use the metrics formula shown in the **Derived metrics formula** column to compute them\.

**Interpreting Amazon S3 Storage Lens metrics unit multiples prefix symbols \(K, M, G, etc\.\)**  
S3 Storage Lens metrics unit multiples are written using prefix symbols that are represented using the International System of Units \(SI\) symbols that are standardized by the International Bureau of Weights and Measures \(BIPM\)\. They are also used in the Unified Code for Units of Measure \(UCUM\)\. For more information, see [List of SI prefixes symbols](https://www.bipm.org/en/measurement-units/#si-prefixes)\. 


| Metric name | Metric name in CloudWatch and export | Description | Free | Type | Category | Derived? | Derived metric formula | 
| --- | --- | --- | --- | --- | --- | --- | --- | 
|  Total Storage  |  StorageBytes  | The total storage |  Y  |  Usage  |  Summary |  N  |   | 
|  Object Count  | ObjectCount | The total object count |  Y  |  Usage  |  Summary |  N  |   | 
|  \# Avg Object Size  | NA | The average object size |  Y  |  Usage  |  Summary |  Y  | Sum\(StorageBytes\)/ sum\(ObjectCount\)  | 
| \# of Active Buckets  | NA | The total number of buckets in active usage with storage > 0 bytes |  Y  |  Usage  |  Summary |  Y  | DistinctCount\[Bucketname\]  | 
| \# Accounts  | NA | The number of accounts whose storage is in scope |  Y  |  Usage  |  Summary |  Y  | DistinctCount\[AccountID\]  | 
| Current Version Storage Bytes  | NA | The number of bytes that are a current version  |  Y  |  Usage  | Data Protection, Cost Efficiency  |  N  |   | 
| % Current Version Bytes  | NA | The percentage of bytes in scope that are current version |  Y  |  Usage  | Data Protection, Cost Efficiency  |  Y  | Sum\(CurrentVersion Bytes\) /sum\(Storage Bytes\)  | 
| Current Version Object Count  |  CurrentVersionObjectCount  | The count of current version objects |  Y  |  Usage  | Data Protection, Cost Efficiency  |  N  |   | 
| % Current Version Objects  | NA | The percentage of objects in scope that are a noncurrent version  |  Y  |  Usage  | Data Protection, Cost Efficiency  |  Y  | Sum\(CurrentVersion Objects\)/sum\(ObjectCount\)  | 
| Non\-Current Version Storage Bytes  |  NonCurrentVersionStorageBytes  | The number of noncurrent versioned bytes  |  Y  |  Usage  | Data Protection, Cost Efficiency  |  N  |   | 
| % Non\-Current Version Bytes  | NA | The percentage of bytes in scope that are noncurrent version |  Y  |  Usage  | Data Protection, Cost Efficiency  |  Y  | Sum\(NonCurrentVersionSto rageBytes\)/ Sum\(StorageBytes\)  | 
| Non\-Current Version Object Count  |  NonCurrentVersionObjectCount  | The count of the noncurrent version objects |  Y  |  Usage  | Data Protection, Cost Efficiency  |  N  |   | 
| % Non\-Current Version Objects  | NA | The percentage of objects in scope that are a noncurrent version |  Y  |  Usage  | Data Protection, Cost Efficiency  |  Y  | Sum\(NonCurrentVersionObjectCount\)/Sum\(ObjectCount\)  | 
| Delete Marker Object Count  |  DeleteMarkerObjectCount  | The total number of objects with a delete marker |  Y  |  Usage  |  Cost Efficiency  |  N  |   | 
| % Delete Marker Objects  | NA | The percentage of objects in scope with a delete marker  |  Y  |  Usage  |  Cost Efficiency  |  Y  |   | 
| Encrypted Storage Bytes  |  EncryptedStorageBytes  | The total number of encrypted bytes using [Amazon S3 server\-side encryption](https://docs.aws.amazon.com/AmazonS3/latest/userguide/serv-side-encryption.html ) |  Y  |  Usage  | Data Protection  |  N  |   | 
| % Encrypted Bytes | NA | The percentage of total bytes in scope that are encrypted using [Amazon S3 server\-side encryption](https://docs.aws.amazon.com/AmazonS3/latest/userguide/serv-side-encryption.html ) |  Y  |  Usage  | Data Protection  |  Y  | Sum\(EncryptedStorageBytes\)/ Sum\(StorageBytes\)  | 
| Encrypted Object Count  |  EncryptedObjectCount  | The total object counts that are encrypted using [Amazon S3 server\-side encryption](https://docs.aws.amazon.com/AmazonS3/latest/userguide/serv-side-encryption.html ) |  Y  |  Usage  | Data Protection  |  N  |   | 
| % Encrypted Objects  | NA | The percentage of objects in scope that are encrypted using [Amazon S3 server\-side encryption](https://docs.aws.amazon.com/AmazonS3/latest/userguide/serv-side-encryption.html ) |  Y  |  Usage  | Data Protection  |  Y  | Sum\(EncryptedStorageBytes\)/Sum\(ObjectCount\)  | 
| Unencrypted Storage Bytes  | NA | The number of bytes in scope that are unencrypted  |  Y  |  Usage  | Data Protection  |  Y  | Sum\(StorageBytes\) \- sum\(EncryptedStorageBytes\)  | 
| % Unencrypted Bytes  | NA | The percentage of bytes in scope that are unencrypted  |  Y  |  Usage  | Data Protection  |  Y  | Sum\(UnencryptedStorageBytes\)/ Sum\(StorageBytes\)  | 
| Unencrypted Object Count  | NA | The count of the objects that are unencrypted  |  Y  |  Usage  | Data Protection  |  Y  | Sum\(ObjectCounts\) \- sum\(EncryptedObjectCounts\)  | 
| % Unencrypted Objects  | NA | The percentage of unencrypted objects  |  Y  |  Usage  | Data Protection  |  Y  | Sum\(UnencryptedStorageBytes\)/ Sum\(ObjectCount\)  | 
| Replicated Storage Bytes  |  ReplicatedStorageBytes  | The total number of bytes in scope that are replicated  |  Y  |  Usage  | Data Protection  |  N  |   | 
| % Replicated Bytes  | NA | The percentage of total bytes in scope that are replicated  |  Y  |  Usage  | Data Protection  |  Y  | Sum\(ReplicatedStorageBytes\)/ Sum\(StorageBytes\)  | 
| Replicated Object Count  |  ReplicatedObjectCount  | The count of replicated objects  |  Y  |  Usage  | Data Protection  |  N  |   | 
| % Replicated Objects  | NA | The percentage of total objects that are replicated  |  Y  |  Usage  | Data Protection  |  Y  | Sum\(ReplicatedObjects\)/ Sum\(ObjectCount\)  | 
| Object Lock Enabled Storage Bytes  |  ObjectLockEnabledStorageBytes  | The total number of bytes in scope that have Object Lock enabled |  Y  |  Usage  | Data Protection  |  N  |   | 
| % Object Lock Bytes  | NA | The percentage of total bytes in scope that have Object Lock enabled  |  Y  |  Usage  | Data Protection  |  Y  | Sum\(ObjectLockBytes\)/ Sum\(StorageBytes\)  | 
| Object Lock Enabled Object Count  |  ObjectLockEnabledObjectCount  | The total number of objects in scope that have Object Lock enabled |  Y  |  Usage  | Data Protection  |  N  |   | 
| % Object Lock Objects  | NA | The percentage of objects in scope that have Object Lock enabled |  Y  |  Usage  | Data Protection  |  Y  | Sum\(ObjectLockObjects\)/ Sum\(ObjectCount\)  | 
| Incomplete Multipart Upload Storage Bytes  |  IncompleteMultipartUploadStorageBytes  | The total bytes in scope with incomplete multipart uploads  |  Y  |  Usage  |  Cost Efficiency  |  N  |   | 
| % Incomplete MPU Bytes  | NA | The percentage of bytes in scope that are results of incomplete multipart uploads  |  Y  |  Usage  |  Cost Efficiency  |  Y  | Sum\(IncompleteMPUbytes\)/ Sum\(StorageBytes\)  | 
| Incomplete Multipart Upload Object Count  |  IncompleteMultipartUploadObjectCount  | The number of objects in scope that are incomplete multipart uploads  |  Y  |  Usage  |  Cost Efficiency  |  N  |   | 
| % Incomplete MPU Objects  | NA | The percentage of objects in scope that are incomplete multipart uploads  |  Y  |  Usage  |  Cost Efficiency  |  Y  | Sum\(IncompleteMPUobjects \)/Sum\( ObjectCount\)  | 
| All Requests  |  AllRequests  | The total number of requests made  |  N  |  Activity |  Summary, Activity |  N  |   | 
| Get Requests  |  GetRequests  | The total number of GET requests made  |  N  |  Activity |  Activity |  N  |   | 
| Put Requests  |  PutRequests  | The total number of PUT requests made  |  N  |  Activity |  Activity |  N  |   | 
| Head Requests |  HeadRequests  | The total number of head requests made  |  N  |  Activity |  Activity |  N  |   | 
| Delete Requests  |  DeleteRequests  | The total number of delete requests made  |  N  |  Activity |  Activity |  N  |   | 
| List Requests |  ListRequests  | The total number of list requests made  |  N  |  Activity |  Activity |  N  |   | 
| Post Requests  |  PostRequests  | The total number of post requests made  |  N  |  Activity |  Activity |  N  |   | 
| Select Requests |  SelectRequests  | The total number of select requests |  N  |  Activity |  Activity |  N  |   | 
| Select Scanned Bytes |  SelectBytesScanned  | The number of select bytes scanned |  N  |  Activity |  Activity |  N  |   | 
| Select Returned Bytes  |  SelectBytesReturned  | The number of select bytes returned  |  N  |  Activity |  Activity |  N  |   | 
| Bytes Downloaded  |  BytesDownloaded  | The number of bytes in scope that were downloaded  |  N  |  Activity |  Activity |  N  |   | 
| % Retrieval Rate  | NA | The percentage of retrieval rate  |  N  |  Activity |  Activity, Cost Efficiency  |  Y  | Sum\(BytesDownloaded\)/Sum\(StorageBytes\)  | 
| Bytes Uploaded  |  BytesUploaded  | The number of bytes uploaded  |  N  |  Activity |  Activity |  N  |   | 
| % Ingest Ratio  | NA | The number of bytes loaded as a percentage of total storage bytes in scope  |  N  |  Activity |  Activity, Cost Efficiency  |  Y  | Sum\(BytesUploaded\) /Sum\(Storage Bytes\)  | 
| 4xx Errors  |  4xxErrors  | The total 4xx errors in scope  |  N  |  Activity |  Activity |  N  |   | 
| 5xx Errors  |  5xxErrors  | The total 5xx errors in scope  |  N  |  Activity |  Activity |  N  |   | 
| Total Errors | NA | The sum of all the \(4xx\) and \(5xx\) errors  |  N  |  Activity |  Activity |  Y  |  Sum\(4xxErrors\) \+ Sum\(5xxErrors\) | 
| % Error Rate | NA | The total errors as a percent of total requests |  N  |  Activity |  Activity |  Y  |  Sum\(TotalErrors\)/ Sum\(TotalRequests\)  | 