# Managing S3 on Outposts capacity with Amazon CloudWatch metrics<a name="S3OutpostsCapacity"></a>

To help manage the fixed S3 capacity on your Outpost, we recommend that you create CloudWatch alerts that tell you when your storage utilization exceeds a certain threshold\. For more information about the CloudWatch metrics for S3 on Outposts, see [CloudWatch metrics](#S3OutpostsCloudWatchMetrics)\. If there is not enough space to store an object on your Outpost, the API returns an insufficient capacity exemption \(ICE\)\. To free up space, you can create CloudWatch alarms that trigger explicit data deletion, or use a lifecycle expiration policy to expire objects\. To save data before deletion, you can use AWS DataSync to copy data from your Amazon S3 on Outposts bucket to an S3 bucket in an AWS Region\. For more information about using DataSync, see [Getting Started with AWS DataSync](https://docs.aws.amazon.com/datasync/latest/userguide/getting-started.html) in the *AWS DataSync User Guide*\. 

## CloudWatch metrics<a name="S3OutpostsCloudWatchMetrics"></a>

The `S3Outposts` namespace includes the following metrics for Amazon S3 on Outposts buckets\. You can monitor the total number of S3 on Outposts bytes provisioned, the total free bytes available for objects, and the total size of all objects for a given bucket\. Bucket or account\-related metrics exist for all direct S3 usage\. Indirect S3 usage, such as storing Amazon Elastic Block Store local snapshots or Amazon Relational Database Service backups on an Outpost, consumes S3 capacity, but is not included in bucket or account\-related metrics\. For more information about Amazon EBS local snapshots, see [ Amazon EBS local snapshots on Outposts](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/snapshots-outposts.html)\. To see your Amazon EBS cost report, visit [https://console\.aws\.amazon\.com/billing/](https://console.aws.amazon.com/billing/)\.

**Note**  
S3 on Outposts supports only the following metrics, and no other Amazon S3 metrics\.  
Because S3 on Outposts has a fixed capacity limit, we recommend creating CloudWatch alarms to notify you when your storage utilization exceeds a certain threshold\.


| Metric | Description | Time Period | Units | Type | 
| --- | --- | --- | --- | --- | 
| OutpostTotalBytes |  The total provisioned capacity in bytes for an Outpost\.   | 5 minutes | Bytes | S3 on Outposts | 
| OutpostFreeBytes |  The count of free bytes available on an Outpost to store customer data\.  | 5 minutes | Bytes | S3 on Outposts | 
| BucketUsedBytes |  The total size of all objects for the given bucket\.  | 5 minutes | Bytes | S3 on Outposts\. Direct S3 usage only\. | 
| AccountUsedBytes |  The total size of all objects for the specified Outposts account\.  | 5 minutes | Bytes | S3 on Outposts\. Direct S3 usage only\. | 