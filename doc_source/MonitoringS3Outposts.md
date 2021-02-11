--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Monitoring Amazon S3 on Outposts<a name="MonitoringS3Outposts"></a>

You can monitor and manage Amazon S3 on Outposts storage capacity using Amazon CloudWatch Events and AWS CloudTrail logs\.

**Topics**
+ [Managing S3 on Outposts capacity with Amazon CloudWatch Events](#S3OutpostsCapacity)
+ [Managing S3 on Outposts capacity with AWS CloudTrail logs](#S3OutpostsCloudtrail)

## Managing S3 on Outposts capacity with Amazon CloudWatch Events<a name="S3OutpostsCapacity"></a>

If there is not enough space to store an object on your Outpost, the API returns an insufficient capacity exemption \(ICE\)\. To avoid this, you can create CloudWatch alerts that tell you when storage utilization exceeds a threshold\. For more information, see [Amazon S3 on Outposts CloudWatch metrics](metrics-dimensions.md#s3-outposts-cloudwatch-metrics)\.

You can use this method to free up space by explicitly deleting data, using a lifecycle expiration policy, or copying data from your S3 on Outposts bucket to an S3 bucket in an AWS Region using AWS DataSync\. For more information about using DataSync, see [Getting Started with AWS DataSync](https://docs.aws.amazon.com/datasync/latest/userguide/getting-started.html) in the *AWS DataSync User Guide*\.

## Managing S3 on Outposts capacity with AWS CloudTrail logs<a name="S3OutpostsCloudtrail"></a>

Your Amazon S3 on Outposts management events are available via AWS CloudTrail logs\. For more information, see [Logging Amazon S3 API calls using CloudTrail](https://docs.aws.amazon.com/AmazonS3/latest/dev/cloudtrail-logging.html)\. 

In addition, you can optionally enable logging for data events in CloudTrail\. For more information, see [Using the Amazon S3 console](enable-cloudtrail-logging-for-s3.md#enable-cloudtrail-events)\. 