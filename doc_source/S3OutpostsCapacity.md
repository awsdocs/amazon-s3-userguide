# Managing S3 on Outposts capacity with Amazon CloudWatch metrics<a name="S3OutpostsCapacity"></a>

If there is not enough space to store an object on your Outpost, the API returns an insufficient capacity exemption \(ICE\)\. To avoid this, you can create CloudWatch alerts that tell you when storage utilization exceeds a certain threshold\. For more information, see [Amazon S3 on Outposts metrics in CloudWatch](metrics-dimensions.md#s3-outposts-cloudwatch-metrics)\. 

You can use this method to free up space by explicitly deleting data, using a lifecycle expiration policy, or copying data from your Amazon S3 on Outposts bucket to an S3 bucket in an AWS Region by using AWS DataSync\. For more information about using DataSync, see [Getting Started with AWS DataSync](https://docs.aws.amazon.com/datasync/latest/userguide/getting-started.html) in the *AWS DataSync User Guide*\.

## CloudWatch metrics<a name="S3OutpostsCloudWatchMetrics"></a>

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