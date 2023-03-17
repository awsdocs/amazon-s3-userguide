# Enabling CloudTrail event logging for S3 buckets and objects<a name="enable-cloudtrail-logging-for-s3"></a>

You can use CloudTrail data events to get information about bucket and object\-level requests in Amazon S3\. To enable CloudTrail data events for all of your buckets or for a list of specific buckets, you must [create a trail manually in CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-create-a-trail-using-the-console-first-time.html)\. 

**Note**  
The default setting for CloudTrail is to find only management events\. Check to ensure that you have the data events enabled for your account\.
 With an S3 bucket that is generating a high workload, you could quickly generate thousands of logs in a short amount of time\. Be mindful of how long you choose to enable CloudTrail data events for a busy bucket\. 

 CloudTrail stores Amazon S3 data event logs in an S3 bucket of your choosing\. Consider using a bucket in a separate AWS account to better organize events from multiple buckets that you might own into a central place for easier querying and analysis\. AWS Organizations helps you create an AWS account that is linked to the account that owns the bucket that you're monitoring\. For more information, see [What is AWS Organizations?](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_introduction.html) in the *AWS Organizations User Guide*\.

When you create a trail in CloudTrail, in the data events section, you can select the **Select all S3 buckets in your account** check box to log all object\-level events\. 

**Note**  
It's a best practice to create a lifecycle configuration for your AWS CloudTrail data event bucket\. Configure the lifecycle configuration to periodically remove log files after the period of time you believe you need to audit them\. Doing so reduces the amount of data that Athena analyzes for each query\. For more information, see [Setting lifecycle configuration on a bucket](how-to-set-lifecycle-configuration-intro.md)\.
For more information about logging format, see [Logging Amazon S3 API calls using AWS CloudTrail](cloudtrail-logging.md)\.
For examples of how to query CloudTrail logs, see the *AWS Big Data Blog* post [Analyze Security, Compliance, and Operational Activity Using AWS CloudTrail and Amazon Athena](http://aws.amazon.com/blogs/big-data/aws-cloudtrail-and-amazon-athena-dive-deep-to-analyze-security-compliance-and-operational-activity/)\. 

## Enable logging for objects in a bucket using the console<a name="enable-cloudtrail-events"></a>

You can use the Amazon S3 console to configure an AWS CloudTrail trail to log data events for objects in an S3 bucket\. CloudTrail supports logging Amazon S3 object\-level API operations such as `GetObject`, `DeleteObject`, and `PutObject`\. These events are called *data events*\. 

By default, CloudTrail trails don't log data events, but you can configure trails to log data events for S3 buckets that you specify, or to log data events for all the Amazon S3 buckets in your AWS account\. For more information, see [Logging Amazon S3 API calls using AWS CloudTrail](cloudtrail-logging.md)\. 

CloudTrail does not populate data events in the CloudTrail event history\. Additionally, not all bucket\-level actions are populated in the CloudTrail event history\. For more information about the Amazon S3 bucket–level API actions tracked by CloudTrail logging, see [Amazon S3 bucket\-level actions that are tracked by CloudTrail logging](cloudtrail-logging-s3-info.md#cloudtrail-bucket-level-tracking)\. For more information about how to query CloudTrail logs, see the AWS Knowledge Center article about [using Amazon CloudWatch Logs filter patterns and Amazon Athena to query CloudTrail logs](https://aws.amazon.com/premiumsupport/knowledge-center/find-cloudtrail-object-level-events/)\.

To configure a trail to log data events for an S3 bucket, you can use either the AWS CloudTrail console or the Amazon S3 console\. If you are configuring a trail to log data events for all the Amazon S3 buckets in your AWS account, it's easier to use the CloudTrail console\. For information about using the CloudTrail console to configure a trail to log S3 data events, see [ Data events](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/logging-data-events-with-cloudtrail.html#logging-data-events) in the *AWS CloudTrail User Guide*\. 

**Important**  
Additional charges apply for data events\. For more information, see [AWS CloudTrail pricing](https://aws.amazon.com/cloudtrail/pricing/)\. 

The following procedure shows how to use the Amazon S3 console to configure a CloudTrail trail to log data events for an S3 bucket\.

**To enable CloudTrail data events logging for objects in an S3 bucket**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the name of the bucket\.

1. Choose **Properties**\.

1. Under **AWS CloudTrail data events**, choose **Configure in CloudTrail**\. 

   You can create a new CloudTrail trail or reuse an existing trail and configure Amazon S3 data events to be logged in your trail\. For information about how to create trails in the CloudTrail console, see [Creating and updating a trail with the console](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/logging-data-events-with-cloudtrail.html#logging-data-events) in the *AWS CloudTrail User Guide*\. For information about how to configure Amazon S3 data event logging in the CloudTrail console, see[ Logging data events for Amazon S3 Objects](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/logging-data-events-with-cloudtrail.html#logging-data-events-examples) in the *AWS CloudTrail User Guide*\. 
**Note**  
If you use the CloudTrail console or the Amazon S3 console to configure a trail to log data events for an S3 bucket, the Amazon S3 console shows that object\-level logging is enabled for the bucket\. 

**To disable CloudTrail data events logging for objects in an S3 bucket**

1. Sign in to the AWS Management Console and open the CloudTrail console at [https://console\.aws\.amazon\.com/cloudtrail/](https://console.aws.amazon.com/cloudtrail/)\.

1. In the left navigation pane, choose **Trails**\.

1. Choose the name of the trail that you created to log events for your bucket\.

1. On the details page for your trail, choose **Stop logging** in the upper\-right corner\.

1. In the dialog box that appears, choose **Stop logging**\. 

For information about enabling object\-level logging when you create an S3 bucket, see [Creating a bucket](create-bucket-overview.md)\.

For more information about CloudTrail logging with S3 buckets, see the following topics:
+ [Viewing the properties for an S3 bucket](view-bucket-properties.md)
+ [Logging Amazon S3 API calls using AWS CloudTrail](cloudtrail-logging.md)
+ [ Working with CloudTrail Log Files](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-working-with-log-files.html) in the *AWS CloudTrail User Guide*