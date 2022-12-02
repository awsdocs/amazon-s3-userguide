# Amazon S3 Storage Lens metrics use cases<a name="storage-lens-use-cases"></a>

You can use your Amazon S3 Storage Lens dashboard to visualize insights and trends, flag outliers, and receive recommendations\. S3 Storage Lens metrics are organized into categories that align with key use cases\. You can use these metrics to do the following: 
+ Identify cost\-optimization opportunities
+ Apply data\-protection best practices
+ Apply access\-management best practices
+ Improve the performance of application workloads

For example, with cost\-optimization metrics, you can identify opportunities to reduce your Amazon S3 storage costs\. You can identify buckets with multipart uploads that are more than 7\-days old or buckets that are accumulating noncurrent versions\.

Similarly, you can use data\-protection metrics to identify buckets that aren't following data\-protection best practices within your organization\. For example, you can identify buckets that don’t use AWS Key Management Service keys \(SSE\-KMS\) for default encryption or don't have S3 Versioning enabled\. 

With S3 Storage Lens access\-management metrics, you can identify bucket settings for S3 Object Ownership so that you can migrate access control list \(ACL\) permissions to bucket policies and disable ACLs\.

If you have [S3 Storage Lens advanced metrics](storage_lens_basics_metrics_recommendations.md) enabled, you can use detailed status\-code metrics to get counts for successful or failed requests that you can use to troubleshoot access or performance issues\. 

With advanced metrics, you can also access additional cost\-optimization and data\-protection metrics that you can use to identify opportunities to further reduce your overall S3 storage costs and better align with best practices for protecting your data\. For example, advanced cost\-optimization metrics include lifecycle rule counts that you can use to identify buckets that don't have lifecycle rules to expire incomplete multipart uploads that are more than 7 days old\. Advanced data\-protection metrics include replication rule counts\.

For more information about metrics categories, see [Metrics categories](storage_lens_basics_metrics_recommendations.md#storage_lens_basics_metrics_types)\. For a complete list of S3 Storage Lens metrics, see [Amazon S3 Storage Lens metrics glossary](storage_lens_metrics_glossary.md)\.

**Topics**
+ [Using Amazon S3 Storage Lens to optimize your storage costs](storage-lens-optimize-storage.md)
+ [S3 Storage Lens data protection](storage-lens-data-protection.md)
+ [Using S3 Storage Lens to audit Object Ownership settings](storage-lens-access-management.md)
+ [Using S3 Storage Lens metrics to improve performance](storage-lens-detailed-status-code.md)