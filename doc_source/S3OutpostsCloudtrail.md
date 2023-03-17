# Monitoring S3 on Outposts with AWS CloudTrail logs<a name="S3OutpostsCloudtrail"></a>

Amazon S3 on Outposts is integrated with AWS CloudTrail, a service that provides a record of actions taken by a user, role, or an AWS service in S3 on Outposts\. You can use AWS CloudTrail to get information about S3 on Outposts bucket\-level and object\-level requests to audit and log your S3 on Outposts event activity\. To enable CloudTrail data events for all your Outposts buckets or for a list of specific Outposts buckets, you must [create a trail manually in CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-create-a-trail-using-the-console-first-time.html)\. For more information about CloudTrail log file entries, see [S3 on Outposts log file entries](https://docs.aws.amazon.com/AmazonS3/latest/userguide/cloudtrail-logging-understanding-s3-entries.html#cloudtrail-logging-understanding-s3outposts-entries)\.

**Note**  
It's a best practice to create a lifecycle policy for your AWS CloudTrail data event Outposts bucket\. Configure the lifecycle policy to periodically remove log files after the period of time that you need to audit them\. Doing so reduces the amount of data that Amazon Athena analyzes for each query\. For more information, see [Setting lifecycle configuration on a bucket](how-to-set-lifecycle-configuration-intro.md)\.
For examples of how to query CloudTrail logs, see the *AWS Big Data Blog* post [Analyze Security, Compliance, and Operational Activity Using AWS CloudTrail and Amazon Athena](http://aws.amazon.com/blogs/big-data/aws-cloudtrail-and-amazon-athena-dive-deep-to-analyze-security-compliance-and-operational-activity/)\. 

## Enable CloudTrail logging for objects in an S3 on Outposts bucket<a name="s3-outposts-add-bucket-events-cloudtrail"></a>

You can use the Amazon S3 console to configure an AWS CloudTrail trail to log data events for objects in an Amazon S3 on Outposts bucket\. CloudTrail supports logging S3 on Outposts object\-level API operations such as `GetObject`, `DeleteObject`, and `PutObject`\. These events are called *data events*\. 

By default, CloudTrail trails don't log data events\. However, you can configure trails to log data events for S3 on Outposts buckets that you specify, or to log data events for all the S3 on Outposts buckets in your AWS account\. For more information, see [Logging Amazon S3 API calls using AWS CloudTrail](cloudtrail-logging.md)\. 

CloudTrail does not populate data events in the CloudTrail event history\. Additionally, not all S3 on Outposts bucket–level API operations are populated in the CloudTrail event history\. For more information about how to query CloudTrail logs, see [Using Amazon CloudWatch Logs filter patterns and Amazon Athena to query CloudTrail logs](https://aws.amazon.com/premiumsupport/knowledge-center/find-cloudtrail-object-level-events/) on the AWS Knowledge Center\.

To configure a trail to log data events for an S3 on Outposts bucket, you can use either the AWS CloudTrail console or the Amazon S3 console\. If you are configuring a trail to log data events for all the S3 on Outposts buckets in your AWS account, it's easier to use the CloudTrail console\. For information about using the CloudTrail console to configure a trail to log S3 on Outposts data events, see [Data events](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/logging-data-events-with-cloudtrail.html#logging-data-events) in the *AWS CloudTrail User Guide*\. 

**Important**  
Additional charges apply for data events\. For more information, see [AWS CloudTrail pricing](https://aws.amazon.com/cloudtrail/pricing/)\. 

The following procedure shows how to use the Amazon S3 console to configure a CloudTrail trail to log data events for an S3 on Outposts bucket\.

**Note**  
The AWS account that creates the bucket owns it and is the only one that can configure S3 on Outposts data events to be sent to AWS CloudTrail\.

**To enable CloudTrail data events logging for objects in an S3 on Outposts bucket**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Outposts buckets**\.

1. Choose the name of the Outposts bucket whose data events you want to log by using CloudTrail\.

1. Choose **Properties**\.

1. In the **AWS CloudTrail data events** section, choose **Configure in CloudTrail**\. 

   The AWS CloudTrail console opens\. 

   You can create a new CloudTrail trail or reuse an existing trail and configure S3 on Outposts data events to be logged in your trail\. 

1. On the CloudTrail console **Dashboard** page, choose **Create trail**\.

1. On the **Step 1 Choose trail attributes** page, provide a name for the trail, choose an S3 bucket to store trail logs, specify any other settings that you want, and then choose **Next**\.

1. On the **Step 2 Choose log events** page, under **Event type**, choose **Data events**\.

   For **Data event type**, choose **S3 Outposts**\. Choose **Next**\.
**Note**  
When you create a trail and configure data event logging for S3 on Outposts, you must specify the data event type correctly\.  
If you use the CloudTrail console, choose **S3 Outposts** for **Data event type**\. For information about how to create trails in the CloudTrail console, see [Creating and updating a trail with the console](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/logging-data-events-with-cloudtrail.html#logging-data-events) in the *AWS CloudTrail User Guide*\. For information about how to configure S3 on Outposts data event logging in the CloudTrail console, see [ Logging data events for Amazon S3 Objects](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/logging-data-events-with-cloudtrail.html#logging-data-events-examples) in the *AWS CloudTrail User Guide*\.
If you use the AWS Command Line Interface \(AWS CLI\) or the AWS SDKs, set the `resources.type` field to `AWS::S3Outposts::Object`\. For more information about how to log S3 on Outposts data events with the AWS CLI, see [Log S3 on Outposts events](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/logging-data-events-with-cloudtrail.html#creating-data-event-selectors-CLI-outposts) in the *AWS CloudTrail User Guide*\.
If you use the CloudTrail console or the Amazon S3 console to configure a trail to log data events for an S3 on Outposts bucket, the Amazon S3 console shows that object\-level logging is enabled for the bucket\. 

1. On the **Step 3 Review and create** page, review the trail attributes and the log events that you configured\. Then, choose **Create trail**\.

**To disable CloudTrail data events logging for objects in an S3 on Outposts bucket**

1. Sign in to the AWS Management Console and open the CloudTrail console at [https://console\.aws\.amazon\.com/cloudtrail/](https://console.aws.amazon.com/cloudtrail/)\.

1. In the left navigation pane, choose **Trails**\.

1. Choose the name of the trail that you created to log events for your S3 on Outposts bucket\.

1. On the details page for your trail, choose **Stop logging** in the upper\-right corner\.

1. In the dialog box that appears, choose **Stop logging**\. 