# Viewing the properties for an S3 bucket<a name="view-bucket-properties"></a>

You can view and configure the properties for an Amazon S3 bucket, including settings for versioning, tags, default encryption, logging, notifications, and more\.

## Using the S3 console<a name="view-bucket"></a>

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the name of the bucket that you want to view the properties for\.

1. Choose **Properties**\.

1. On the **Properties** page, you can configure the following properties for the bucket\.
   + **Bucket Versioning** – Keep multiple versions of an object in one bucket by using versioning\. By default, versioning is disabled for a new bucket\. For information about enabling versioning, see [Enabling versioning on buckets](manage-versioning-examples.md)\.
   + **Tags** – With AWS cost allocation, you can use bucket tags to annotate billing for your use of a bucket\. A tag is a key\-value pair that represents a label that you assign to a bucket\. To add tags, choose **Tags**, and then choose **Add tag**\. For more information, see [Using cost allocation S3 bucket tags](CostAllocTagging.md)\. 
   + **Default encryption** – Enabling default encryption provides you with automatic server\-side encryption\. Amazon S3 encrypts an object before saving it to a disk and decrypts the object when you download it\. For more information, see [Setting default server\-side encryption behavior for Amazon S3 buckets](bucket-encryption.md)\. 
   + **Server access logging** – Get detailed records for the requests that are made to your bucket with server access logging\. By default, Amazon S3 doesn't collect server access logs\. For information about enabling server access logging, see [Enabling Amazon S3 server access logging](enable-server-access-logging.md)
   + **AWS CloudTrail data events** – Use CloudTrail to log data events\. By default, trails don't log data events\. Additional charges apply for data events\. For more information, see [Logging Data Events for Trails](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/logging-data-events-with-cloudtrail.html) in the *AWS CloudTrail User Guide*\.
   + **Event notifications** – Enable certain Amazon S3 bucket events to send notification messages to a destination whenever the events occur\. To enable events, choose **Create event notification**, and then specify the settings you want to use\. For more information, see [Enabling and configuring event notifications using the Amazon S3 console](enable-event-notifications.md)\.
   + **Transfer acceleration** – Enable fast, easy, and secure transfers of files over long distances between your client and an S3 bucket\. For information about enabling transfer acceleration, see [Enabling and using S3 Transfer Acceleration](transfer-acceleration-examples.md)\.
   + **Object Lock** – Use S3 Object Lock to prevent an object from being deleted or overwritten for a fixed amount of time or indefinitely\. For more information, see [Using S3 Object Lock](object-lock.md)\.
   + **Requester Pays** – Enable Requester Pays if you want the requester \(instead of the bucket owner\) to pay for requests and data transfers\. For more information, see [Using Requester Pays buckets for storage transfers and usage](RequesterPaysBuckets.md)\. 
   + **Static website hosting** – You can host a static website on Amazon S3\. To enable static website hosting, choose **Static website hosting**, and then specify the settings you want to use\. For more information, see [Hosting a static website using Amazon S3](WebsiteHosting.md)\.

## Using the AWS CLI<a name="view-bucket-cli"></a>

You can also use the AWS Command Line Interface \(AWS CLI\) to view the properties for an S3 bucket\. For more information, see the following commands in the *AWS CLI Command Reference*\.
+ [get\-bucket\-tagging](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3api/get-bucket-tagging.html) 
+  [get\-bucket\-versioning](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3api/get-bucket-versioning.html) 
+  [get\-bucket\-encryption](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3api/get-bucket-encryption.html) 
+  [ get\-bucket\-notification\-configuration](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3api/get-bucket-notification-configuration.html) 
+  [get\-bucket\-logging](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3api/get-bucket-logging.html) 

For information about the AWS CLI, see [What is the AWS Command Line Interface?](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-welcome.html) in the *AWS Command Line Interface User Guide*\. 