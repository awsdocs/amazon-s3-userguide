# Managing S3 on Outposts capacity with AWS CloudTrail logs<a name="S3OutpostsCloudtrail"></a>

Your Amazon S3 on Outposts management events are available via AWS CloudTrail logs\. For more information, see [Logging Amazon S3 API calls using AWS CloudTrail](cloudtrail-logging.md)\. 

In addition, you can optionally enable logging for data events in CloudTrail\. For more information, see [Enable logging for objects in a bucket using the console](enable-cloudtrail-logging-for-s3.md#enable-cloudtrail-events)\. 

## Configuring your S3 on Outposts bucket for CloudTrail logging using the S3 console<a name="s3-outposts-add-bucket-events-cloudtrail"></a>

The following procedure shows you how to configure your S3 on Outposts bucket to emit to AWS CloudTrail logs\.

**Note**  
The AWS account that creates the bucket owns it and is the only one that can configure Amazon S3 data events to be sent to AWS CloudTrail\.

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Outposts buckets**\.

1. Choose the Outposts bucket whose data events you want to log by using CloudTrail\.

1. Go to the section **AWS CloudTrail data events**, and choose **Configure in CloudTrail**\. 

   For more information, see [Enabling CloudTrail event logging for S3 buckets and objects](enable-cloudtrail-logging-for-s3.md)\.