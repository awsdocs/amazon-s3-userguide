# Amazon S3 Storage Lens metrics glossary<a name="storage_lens_metrics_glossary"></a>

The Amazon S3 Storage Lens metrics glossary provides a complete list of free and advanced metrics for S3 Storage Lens\.

S3 Storage Lens offers free metrics for all dashboards and configurations, with the option to upgrade to advanced metrics\. 
+ **Free metrics** contain metrics that are relevant to your storage usage, such as the number of buckets and the objects in your account, and use\-case based metrics, such as cost\-optimization and data\-protection metrics\. All free metrics are collected daily, and data is available for queries for 14 days\. 
+ **Advanced metrics** include all the metrics in free metrics along with additional metrics, such as advanced data\-protection and cost\-optimization metrics, along with additional metric categories, such as activity metrics and detailed status\-code metrics\. Advanced metrics data is available for queries for 15 months\. 

  There are additional charges when you use S3 Storage Lens with advanced metrics and recommendations\. For more information, see [ Amazon S3 pricing](http://aws.amazon.com/s3/pricing/)\. For more information about advanced metrics and recommendations features, see [Metrics selection](storage_lens_basics_metrics_recommendations.md#storage_lens_basics_metrics_selection)\.

**Metric names**  
The **Metric name** column in the following table provides the name of each S3 Storage Lens in the S3 console\. The ******CloudWatch and export** column provides the name of each metric in Amazon CloudWatch and the metrics export file that you can configure in your S3 Storage Lens dashboard\. 

**Derived metric formulas**  
Derived metrics are not available for the metrics export and the CloudWatch publishing option\. However, you can use the metrics formulas shown in the **Derived metrics formula** column to compute them\.

**Interpreting Amazon S3 Storage Lens metrics unit multiples prefix symbols \(K, M, G, and so on\)**  
S3 Storage Lens metrics unit multiples are written with prefix symbols\. These prefix symbols match the International System of Units \(SI\) symbols that are standardized by the International Bureau of Weights and Measures \(BIPM\)\. These symbols are also used in the Unified Code for Units of Measure \(UCUM\)\. For more information, see [List of SI prefix symbols](https://www.bipm.org/en/measurement-units/#si-prefixes)\. 


**S3 Storage Lens metrics glossary**  

| Metric name | CloudWatch and export | Description | Tier | Category | Derived | Derived metric formula | 
| --- | --- | --- | --- | --- | --- | --- | 
| Total storage | StorageBytes | The total storage | Free | Summary | N | \- | 
| Object count  | ObjectCount | The total object count | Free | Summary | N | \- | 
| Average object size  | \- | The average object size | Free | Summary | Y | sum\(StorageBytes\)/sum\(ObjectCount\)  | 
| Active buckets  | \- | The total number of buckets in active usage with storage > 0 bytes | Free | Summary | Y | \- | 
| Buckets | \- | The total number of buckets | Free | Summary | Y | \- | 
| Accounts  | \- | The number of accounts whose storage is in scope | Free | Summary | Y | \- | 
| Current version bytes  | CurrentVersionStorageBytes | The number of bytes that are a current version of an object | Free | Cost optimization | N | \- | 
| % current version bytes  | \- | The percentage of bytes in scope that are current versions of objects | Free | Cost optimization | Y  | sum\(CurrentVersionStorageBytes\)/sum\(StorageBytes\) | 
| Current version object count  | CurrentVersionObjectCount  | The count of current version objects | Free | Cost optimization | N | \- | 
| % current version objects  | \- | The percentage of objects in scope that are a current version  | Free | Cost optimization | Y  | sum\(CurrentVersionObjectCount\)/sum\(ObjectCount\) | 
| Noncurrent version bytes  | NonCurrentVersionStorageBytes  | The number of noncurrent version bytes | Free | Cost optimization | N | \- | 
| % noncurrent version bytes  | \- | The percentage of bytes in scope that are noncurrent versions | Free | Cost optimization | Y  | sum\(NonCurrentVersionStorageBytes\)/sum\(StorageBytes\)  | 
| Noncurrent version object count  | NonCurrentVersionObjectCount  | The count of the noncurrent object versions | Free | Cost optimization | N | \- | 
| % noncurrent version objects  | \- | The percentage of objects in scope that are a noncurrent version | Free | Cost optimization | Y | sum\(NonCurrentVersionObjectCount\)/sum\(ObjectCount\) | 
| Delete marker bytes | DeleteMarkerStorageBytes | The number of bytes in scope that are delete markers | Free | Cost optimization | N | \- | 
| % delete marker bytes | \- | The percentage of bytes in scope that are delete markers | Free | Cost optimization | Y | sum\(DeleteMarkerStorageBytes\)/sum\(StorageBytes\) | 
| Delete marker object count  | DeleteMarkerObjectCount  | The total number of objects with a delete marker | Free | Cost optimization | N | \- | 
| % delete marker objects  | \- | The percentage of objects in scope with a delete marker  | Free | Cost optimization | Y | sum\(DeleteMarkerObjectCount\)/sum\(ObjectCount\) | 
| Incomplete multipart upload bytes  | IncompleteMultipartUploadStorageBytes  | The total bytes in scope for incomplete multipart uploads  | Free | Cost optimization | N | \- | 
| % incomplete multipart upload bytes | \- | The percentage of bytes in scope that are the result of incomplete multipart uploads  | Free | Cost optimization | Y | sum\(IncompleteMultipartUploadStorageBytes\)/sum\(StorageBytes\)  | 
| Incomplete multipart upload object count | IncompleteMultipartUploadObjectCount | The number of objects in scope that are incomplete multipart uploads  | Free | Cost optimization | N | \- | 
| % incomplete multipart upload objects | \- | The percentage of objects in scope that are incomplete multipart uploads  | Free | Cost optimization | Y | sum\(IncompleteMultipartUploadObjectCount\)/sum\(ObjectCount\)  | 
| Incomplete multipart upload storage bytes greater than 7 days old | IncompleteMPUStorageBytesOlderThan7Days | The total bytes in scope for incomplete multipart uploads that are more than 7 days old | Free | Cost optimization | N | \- | 
| % incomplete multipart upload storage bytes greater than 7 days old | \- | The percentage of bytes for incomplete multipart uploads that are more than 7 days old | Free | Cost optimization | Y | sum\(IncompleteMPUStorageBytesOlderThan7Days\)/sum\(StorageBytes\)  | 
| Incomplete multipart upload object count greater than 7 days old | IncompleteMPUObjectCountOlderThan7Days  | The number of objects that are incomplete multipart uploads more than 7 days old | Free | Cost optimization | N | \- | 
| % incomplete multipart upload object count greater than 7 days old | \- | The percentage of objects that are incomplete multipart uploads more than 7 days old | Free | Cost optimization | Y | sum\(IncompleteMPUObjectCountOlderThan7Days\)/sum\(ObjectCount\)  | 
| Transition lifecycle rule count | TransitionLifecycleRuleCount  | The count of lifecycle rules to transition objects to another storage class | Advanced | Cost optimization | N | \- | 
| Average transition lifecycle rules per bucket | \- | The average number of lifecycle rules to transition objects to another storage class | Advanced | Cost optimization | Y | sum\(TransitionLifecycleRuleCount \)/sum\(DistinctNumberOfBuckets\)  | 
| Expiration lifecycle rule count | ExpirationLifecycleRuleCount  | The count of lifecycle rules to expire objects | Advanced | Cost optimization | N | \- | 
| Average expiration lifecycle rules per bucket | \- | The average number of lifecycle rules to expire objects | Advanced | Cost optimization | Y | sum\(ExpirationLifecycleRuleCount\)/sum\(DistinctNumberOfBuckets\)  | 
| Noncurrent version transition lifecycle rule count | NoncurrentVersionTransitionLifecycleRuleCount  | The count of lifecycle rules to transition noncurrent object versions to another storage class | Advanced | Cost optimization | N |  | 
| Average noncurrent version transition lifecycle rules per bucket | \- | The average number of lifecycle rules to transition noncurrent object versions to another storage class | Advanced | Cost optimization | Y | sum\(NoncurrentVersionTransitionLifecycleRuleCount\)/sum\(DistinctNumberOfBuckets\)  | 
| Noncurrent version expiration lifecycle rule count | NoncurrentVersionExpirationLifecycleRuleCount  | The count of lifecycle rules to expire noncurrent object versions | Advanced | Cost optimization | N | \- | 
| Average noncurrent version expiration lifecycle rules per bucket | \- | The average number of lifecycle rules to expire noncurrent object versions | Advanced | Cost optimization | Y | sum\(NoncurrentVersionExpirationLifecycleRuleCount\)/sum\(DistinctNumberOfBuckets\)  | 
| Abort incomplete multipart upload lifecycle rule count | AbortIncompleteMPULifecycleRuleCount | The count of lifecycle rules to delete incomplete multipart uploads | Advanced | Cost optimization | N | \- | 
| Average abort incomplete multipart upload lifecycle rules per bucket | \- | The average number of lifecycle rules to delete incomplete multipart uploads | Advanced | Cost optimization | Y | sum\(AbortIncompleteMPULifecycleRuleCount\)/sum\(DistinctNumberOfBuckets\)  | 
| Expired object delete marker lifecycle rule count | ExpiredObjectDeleteMarkerLifecycleRuleCount  | The count of lifecycle rules to remove expired object delete markers | Advanced | Cost optimization | N | \- | 
| Average expired object delete marker lifecycle rules per bucket | \- | The average number of lifecycle rules to remove expired object delete markers | Advanced | Cost optimization | Y | sum\(ExpiredObjectDeleteMarkerLifecycleRuleCount\)/sum\(DistinctNumberOfBuckets\)  | 
| Total lifecycle rule count | TotalLifecycleRuleCount | The total count of lifecycle rules | Advanced | Cost optimization | N | \- | 
| Average lifecycle rule count per bucket | \- | The average number of lifecycle rules | Advanced | Cost optimization | Y | sum\(TotalLifecycleRuleCount\)/sum\(DistinctNumberOfBuckets\) | 
| Encrypted bytes | EncryptedStorageBytes | The total number of encrypted bytes  | Free | Data protection | N | \- | 
| % encrypted bytes | \- | The percentage of total bytes that are encrypted  | Free | Data protection | Y | sum\(EncryptedObjectCount\)/sum\(StorageBytes\) | 
| Encrypted object count  | EncryptedObjectCount | The total count of objects that are encrypted  | Free | Data protection | N | \- | 
| % encrypted objects  | \- | The percentage of objects that are encrypted  | Free | Data protection | Y | sum\(EncryptedStorageBytes\)/sum\(ObjectCount\) | 
| Unencrypted bytes  | UnencryptedStorageBytes  | The number of bytes that are unencrypted  | Free | Data protection | Y | sum\(StorageBytes\) \- sum\(EncryptedStorageBytes\) | 
| % unencrypted bytes | \- | The percentage of bytes that are unencrypted  | Free | Data protection | Y | sum\(UnencryptedStorageBytes\)/sum\(StorageBytes\) | 
| Unencrypted object count  | UnencryptedObjectCount | The total count of objects that are unencrypted  | Free | Data protection | Y | sum\(ObjectCount\) \- sum\(EncryptedObjectCount\) | 
| % unencrypted objects  | \- | The percentage of unencrypted objects  | Free | Data protection | Y | sum\(UnencryptedStorageBytes\)/sum\(ObjectCount\) | 
| Replicated storage bytes source | ReplicatedStorageBytesSource | The total number of bytes that are replicated  | Free | Data protection | N | \- | 
| % replicated bytes source  | \- | The percentage of total bytes that are replicated  | Free | Data protection | Y | sum\(ReplicatedStorageBytesSource\)/sum\(StorageBytes\)  | 
| Replicated object count source  | ReplicatedObjectCountSource | The count of replicated objects  | Free | Data protection | N | \- | 
| % replicated objects source  | \- | The percentage of total objects that are replicated  | Free | Data protection | Y | sum\(ReplicatedStorageObjectCount\)/sum\(ObjectCount\) | 
| Versioning\-enabled bucket count  | VersioningEnabledBucketCount | The count of buckets that have S3 Versioning enabled | Free | Data protection | N | \- | 
| % versioning\-enabled buckets  | \- | The percentage of buckets that have S3 Versioning enabled | Free | Data protection | Y | sum\(VersioningEnabledBucketCount\)/sum\(DistinctNumberOfBuckets\)  | 
| MFA delete\-enabled bucket count  | MFADeleteEnabledBucketCount | The count of buckets that have MFA \(multi\-factor authentication\) delete enabled | Free | Data protection | N | \- | 
| % MFA delete\-enabled buckets  | \- | The percentage of buckets that have MFA \(multi\-factor authentication\) delete enabled | Free | Data protection | Y | sum\(MFADeleteEnabledBucketCount\)/sum\(DistinctNumberOfBuckets\)  | 
| SSE\-KMS enabled bucket count  | SSEKMSEnabledBucketCount | The count of buckets that use server\-side encryption with AWS Key Management Service keys \(SSE\-KMS\) for default bucket encryption | Free | Data protection | N | \- | 
| % SSE\-KMS enabled buckets  | \- | The percentage of buckets that SSE\-KMS for default bucket encryption | Free | Data protection | Y | sum\(SSEKMSEnabledBucketCount\)/sum\(DistinctNumberOfBuckets\)  | 
| All unsupported signature requests  | AllUnsupportedSignatureRequests | The total number of requests that use unsupported AWS signature versions | Advanced | Data protection | N | \- | 
| % all unsupported signature requests  | \- | The percentage of requests that use unsupported AWS signature versions | Advanced | Data protection | Y | sum\(AllUnsupportedSignatureRequests\)/sum\(AllRequests\)  | 
| All unsupported TLS requests  | AllUnsupportedTLSRequests  | The number of requests that use unsupported Transport Layer Security \(TLS\) versions | Advanced | Data protection | N | \- | 
| % all unsupported TLS requests  | \- | The percentage of requests that use unsupported TLS versions | Advanced | Data protection | Y | sum\(AllUnsupportedTLSRequests\)/sum\(AllRequests\)  | 
| All SSE\-KMS requests  | AllSSEKMSRequests  | The total number of requests that specify SSE\-KMS | Advanced | Data protection | N | \- | 
| % all SSE\-KMS requests  | \- | The percentage of requests that specify SSE\-KMS | Advanced | Data protection | Y | sum\(AllSSEKMSRequests\)/sum\(AllRequests\)  | 
| Same\-Region Replication rule count  | SameRegionReplicationRuleCount  | The count of replication rules for Same\-Region Replication \(SRR\) | Advanced | Data protection | N | \- | 
| Average Same\-Region Replication rules per bucket  | \- | The average number of replication rules for SRR | Advanced | Data protection | Y | sum\(SameRegionReplicationRuleCount\)/sum\(DistinctNumberOfBuckets\)  | 
| Cross\-Region Replication rule count  | CrossRegionReplicationRuleCount  | The count of replication rules for Cross\-Region Replication \(CRR\) | Advanced | Data protection | N | \- | 
| Average Cross\-Region Replication rules per bucket  | \- | The average number of replication rules for CRR | Advanced | Data protection | Y | sum\(CrossRegionReplicationRuleCount\)/sum\(DistinctNumberOfBuckets\)  | 
| Same\-account replication rule count  | SameAccountReplicationRuleCount  | The count of replication rules for replication within the same account | Advanced | Data protection | N | \- | 
| Average same\-account replication rules per bucket  | \- | The average number of replication rules for replication within the same account | Advanced | Data protection | Y | sum\(SameAccountReplicationRuleCount\)/sum\(DistinctNumberOfBuckets\)  | 
| Cross\-account replication rule count  | CrossAccountReplicationRuleCount  | The count of replication rules for cross\-account replication | Advanced | Data protection | N | \- | 
| Average cross\-account replication rules per bucket  | \- | The average number of replication rules for cross\-account replication | Advanced | Data protection | Y | sum\(CrossAccountReplicationRuleCount\)/sum\(DistinctNumberOfBuckets\)  | 
| Invalid destination replication rule count  | InvalidDestinationReplicationRuleCount  | The count of replication rules with a replication destination that's not valid | Advanced | Data protection | N | \- | 
| Average invalid destination replication rules per bucket  | \- | The average number of replication rules with a replication destination that's not valid | Advanced | Data protection | Y | sum\(InvalidReplicationRuleCount\)/sum\(DistinctNumberOfBuckets\)  | 
| Total replication rule count  | \- | The total replication rule count | Advanced | Data protection | Y | \- | 
| Average replication rule count per bucket  | \- | The average total replication rule count | Advanced | Data protection | Y | sum\(all replication rule count metrics\)/sum\(DistinctNumberOfBuckets\)  | 
| Object Ownership bucket owner enforced bucket count  | ObjectOwnershipBucketOwnerEnforcedBucketCount  | The total count of buckets that have access control lists \(ACLs\) disabled by using the bucket owner enforced setting for Object Ownership | Free | Access management | N | \- | 
| % Object Ownership bucket owner enforced buckets  | \- | The percentage of buckets that have ACLs disabled by using the bucket owner enforced setting for Object Ownership | Free | Access management | Y | sum\(ObjectOwnershipBucketOwnerEnforcedBucketCount\)/sum\(DistinctNumberOfBuckets\)  | 
| Object Ownership bucket owner preferred bucket count  | ObjectOwnershipBucketOwnerPreferredBucketCount | The total count of buckets that use the bucket owner preferred setting for Object Ownership | Free | Access management | N | \- | 
| % Object Ownership bucket owner preferred buckets  | \- | The percentage of buckets that use the bucket owner preferred setting for Object Ownership | Free | Access management | Y | sum\(ObjectOwnershipBucketOwnerPreferredBucketCount\)/sum\(DistinctNumberOfBuckets\)  | 
| Object Ownership object writer bucket count  | ObjectOwnershipObjectWriterBucketCount | The total count of buckets that use the object writer setting for Object Ownership | Free | Access management | N | \- | 
| % Object Ownership object writer buckets  | \- | The percentage of buckets that use the object writer setting for Object Ownership | Free | Access management | Y | sum\(ObjectOwnershipObjectWriterBucketCount\)/sum\(DistinctNumberOfBuckets\)  | 
| Transfer Acceleration enabled bucket count  | TransferAccelerationEnabledBucketCount | The total count of buckets that have Transfer Acceleration enabled | Free | Performance | N | \- | 
| % Transfer Acceleration enabled buckets  | \- | The percentage of buckets that have Transfer Acceleration enabled | Free | Performance | Y | sum\(TransferAccelerationEnabledBucketCount\)/sum\(DistinctNumberOfBuckets\)  | 
| Event Notification enabled bucket count  | EventNotificationEnabledBucketCount | The total count of buckets that have Event Notifications enabled | Free | Events | N |  | 
| % Event Notification enabled buckets  | \- | The percentage of buckets that have Event Notifications enabled | Free | Events | Y | sum\(EventNotificationEnabledBucketCount\)/sum\(DistinctNumberOfBuckets\)  | 
| All requests  | AllRequests |  The total number of requests made   | Advanced | Activity | N | \- | 
| Get requests  | GetRequests |  The total number of `GET` requests made  | Advanced | Activity | N | \- | 
| Put requests  | PutRequests |  The total number of `PUT` requests made  | Advanced | Activity | N | \- | 
| Head requests  | HeadRequests | The total number of HEAD requests made | Advanced | Activity | N | \- | 
| Delete requests  | DeleteRequests | The total number of DELETE requests made | Advanced | Activity | N | \- | 
| List requests  | ListRequests | The total number of PUT requests made | Advanced | Activity | N | \- | 
| Post requests  | PostRequests | The total number of POST requests made | Advanced | Activity | N | \- | 
| Select requests  | SelectRequests | The total number of S3 Select requests | Advanced | Activity | N | \- | 
| Select scanned bytes  | SelectScannedBytes | The number of S3 Select bytes scanned  | Advanced | Activity | N | \- | 
| Select returned bytes  | SelectReturnedBytes | The number of S3 Select bytes returned | Advanced | Activity | N | \- | 
| Bytes downloaded  | BytesDownloaded | The number of bytes downloaded | Advanced | Activity | N | \- | 
| % retrieval rate  | \- | The percentage of bytes downloaded | Advanced | Activity | Y | sum\(BytesDownloaded\)/sum\(StorageBytes\)  | 
| Bytes uploaded  | BytesUploaded | The number of bytes uploaded | Advanced | Activity | N | \- | 
| % ingest ratio  | \- | The percentage of bytes uploaded | Advanced | Activity | Y | sum\(BytesUploaded\)/sum\(StorageBytes\)  | 
| 4xx errors  | 4xxErrors | The total number of HTTP 4xx status codes | Advanced | Activity | N | \- | 
| 5xx errors  | 5xxErrors | The total number of HTTP 5xx status codes | Advanced | Activity | N | \- | 
| Total errors  | \- | The sum of all 4xx and 5xx errors | Advanced | Activity | Y | sum\(4xxErrors\) \+ sum\(5xxErrors\)  | 
| % error rate  | \- |  The total number of 4xx and 5xx errors as a percentage of total requests  | Advanced | Activity | Y | sum\(TotalErrors\)/sum\(TotalRequests\)  | 
| 200 OK status count | 200OKStatusCount | The total count of 200 OK status codes | Advanced | Detailed status code | N | \- | 
| % 200 OK status  | \- |  The total number of 200 OK status codes as a percentage of total requests  | Advanced | Detailed status code | Y | sum\(200OKStatusCount\)/sum\(AllRequests\)  | 
| 206 Partial Content status count | 206PartialContentStatusCount | The total count of 206 Partial Content status codes | Advanced | Detailed status code | N | \- | 
| % 206 Partial Content status  | \- | The total number of 206 Partial Content status codes as a percentage of total requests | Advanced | Detailed status code | Y | sum\(206PartialContentStatusCount\)/sum\(AllRequests\)  | 
| 400 Bad Request error count |  400BadRequestErrorCount  | The total count of 400 Bad Request status codes | Advanced | Detailed status code | N | \- | 
| % 400 Bad Request errors  | \- | The total number of 400 Bad Request status codes as a percentage of total requests | Advanced | Detailed status code | Y | sum\(400BadRequestErrorCount\)/sum\(AllRequests\)  | 
| 403 Forbidden error count  |  403ForbiddenErrorCount  | The total count of 403 Forbidden status codes | Advanced | Detailed status code | N | \- | 
| % 403 Forbidden errors  | \- | The total number of 403 Forbidden status codes as a percentage of total requests | Advanced | Detailed status code | Y | sum\(403ForbiddenErrorCount\)/sum\(AllRequests\)  | 
| 404 Not Found error count  | 404NotFoundErrorCount | The total count of 404 Not Found status codes | Advanced | Detailed status code | N | \- | 
| % 404 Not Found errors  | \- | The total number of 404 Not Found status codes as a percentage of total requests | Advanced | Detailed status code | Y | sum\(404NotFoundErrorCount\)/sum\(AllRequests\)  | 
| 500 Internal Server Error count  | 500InternalServerErrorCount | The total count of 500 Internal Server Error status codes | Advanced | Detailed status code | N | \- | 
| % 500 Internal Server Errors  | \- | The total number of 500 Internal Server Error status codes as a percentage of total requests | Advanced | Detailed status code | Y | sum\(500InternalServerErrorCount\)/sum\(AllRequests\)  | 
| 503 Service Unavailable error count  | 503ServiceUnavailableErrorCount | The total count of 503 Service Unavailable status codes | Advanced | Detailed status code | N | \- | 
| % 503 Service Unavailable errors  | \- | The total number of 503 Service Unavailable status codes as a percentage of total requests | Advanced | Detailed status code | Y | sum\(503ServiceUnavailableErrorCount\)/sum\(AllRequests\)  | 