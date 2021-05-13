# Adding an Amazon S3 on Outposts bucket to AWS CloudTrail using the console<a name="s3-outposts-add-bucket-events-cloudtrail"></a>

You can configure your S3 on Outposts bucket to emit to CloudTrail logs\.

**Note**  
The AWS account that creates the bucket owns it and is the only one can configure Amazon S3 data events to be sent to AWS CloudTrail\.

**To add an S3 on Outposts bucket to CloudTrail**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the navigation pane, choose **Outposts buckets**\.

1. Choose the Outposts bucket whose data events you want to log using CloudTrail\.

1. Proceed to the section **AWS CloudTrail data events**, and choose **Configure in CloudTrail**\. 

   For more information, see [Enabling CloudTrail event logging for S3 buckets and objects](enable-cloudtrail-logging-for-s3.md)\.