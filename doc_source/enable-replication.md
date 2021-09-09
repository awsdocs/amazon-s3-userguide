# Using the S3 console<a name="enable-replication"></a>

Follow these steps to configure a replication rule when the destination bucket is in the same AWS account as the source bucket\.

If the destination bucket is in a different account from the source bucket, you must add a bucket policy to the destination bucket to grant the owner of the source bucket account permission to replicate objects in the destination bucket\. For more information, see [Granting permissions when source and destination buckets are owned by different AWS accounts](setting-repl-config-perm-overview.md#setting-repl-config-crossacct)\.

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the name of the bucket that you want\.

1. Choose **Management**, scroll down to **Replication rules**, and then choose **Create replication rule**\.

    

1. Under **Rule name**, enter a name for your rule to help identify the rule later\. The name is required and must be unique within the bucket\.

1. Set up an AWS Identity and Access Management \(IAM\) role that Amazon S3 can assume to replicate objects on your behalf\.

   To set up an IAM role, on the **Replication rule configuration** section, under **IAM role**, do one of the following:
   + We highly recommend that you choose **Create new role** to have Amazon S3 create a new IAM role for you\. When you save the rule, a new policy is generated for the IAM role that matches the source and destination buckets that you choose\.
   + You can choose to use an existing IAM role\. If you do, you must choose a role that grants Amazon S3 the necessary permissions for replication\. Replication fails if this role does not grant Amazon S3 sufficient permissions to follow your replication rule\.
**Important**  
When you add a replication rule to a bucket, you must have the `iam:PassRole` permission to be able to pass the IAM role that grants Amazon S3 replication permissions\. For more information, see [Granting a user permissions to pass a role to an AWS service](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_passrole.html) in the *IAM User Guide*\.

1. Under **Status**, see that **Enabled** is selected by default\. An enabled rule starts to work as soon as you save it\. If you want to enable the rule later, select **Disabled**\.

1.  If the bucket has existing replication rules, you are instructed to set a priority for the rule\. You must set a priority for the rule to avoid conflicts caused by objects that are included in the scope of more than one rule\. In the case of overlapping rules, Amazon S3 uses the rule priority to determine which rule to apply\. The higher the number, the higher the priority\. For more information about rule priority, see [Replication configuration](replication-add-config.md)\.

1. In the **Replication rule configuration**, under **Source bucket**, you have the following options for setting the replication source:
   + To replicate the whole bucket, choose **This rule applies to all objects in the bucket**\. 
   + To replicate all objects that have the same prefix, choose **Limit the scope of this rule using one or more filters**\. This limits replication to all objects that have names that begin with the string \(for example `pictures`\)\. Enter a prefix in the box\. 
**Note**  
If you enter a prefix that is the name of a folder, you must use **/** \(forward slash\) as the last character \(for example, `pictures/`\)\.
   + To replicate all objects with one or more object tags, select **Add tag** and enter the key\-value pair in the boxes\. Repeat the procedure to add another tag\. You can combine a prefix and tags\. For more information about object tags, see [Categorizing your storage using tags](object-tagging.md)\.

   The new schema supports prefix and tag filtering and the prioritization of rules\. For more information about the new schema, see [Backward compatibility](replication-add-config.md#replication-backward-compat-considerations)\. For more information about the XML used with the Amazon S3 API that works behind the user interface, see [Replication configuration](replication-add-config.md)\. The new schema is described as *replication configuration XML V2*\.

1. Under **Destination**, select the bucket where you want Amazon S3 to replicate objects\.
**Note**  
 The number of destination buckets is limited to the number of AWS Regions in a given partition\. A partition is a grouping of Regions\. AWS currently has three partitions: `aws` \(Standard Regions\), `aws-cn` \(China Regions\), and `aws-us-gov` \(AWS GovCloud \[US\] Regions\)\. You can use [service quotas](https://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html) to request an increase in your destination bucket limit\.
   + To replicate to a bucket or buckets in your account, select **Choose a bucket in this account**, and enter or browse for the destination bucket names\. 
   + To replicate to a bucket or buckets in a different AWS account, select **Choose a bucket in another account**, and enter the destination bucket account ID and name\. 

     If the destination is in a different account from the source bucket, you must add a bucket policy to the destination buckets to grant the owner of the source bucket account permission to replicate objects\. For more information, see [Granting permissions when source and destination buckets are owned by different AWS accounts](setting-repl-config-perm-overview.md#setting-repl-config-crossacct)\.
**Note**  
If versioning is not enabled on the destination bucket, you get a warning that contains an **Enable versioning** button\. Choose this button to enable versioning on the bucket\.

1. You have the following additional options while setting the **Destination**:
   + If you want to enable **Object Ownership** to help standardize ownership of new objects in the destination bucket, choose **Change object ownership to the destination bucket owner**\. For more information about this option, see [Controlling ownership of uploaded objects using S3 Object Ownership](about-object-ownership.md)\.
   + If you want to replicate your data into a specific storage class in the destination, choose **Change the storage class for the replicated objects**\. Then choose the storage class that you want to use for the replicated objects in the destination\. If you don't choose this option, the storage class for replicated objects is the same class as the original objects\. 
   + If you want to enable delete marker replication in your replication configuration, select **Delete marker replication**\. For more information see, [Replicating delete markers between buckets](delete-marker-replication.md)\.
   + If you want to enable Amazon S3 replica modification sync in your replication configuration, select **Replica modification sync**\. For more information see, [Replicating metadata changes with Amazon S3 replica modification sync](replication-for-metadata-changes.md)\.
   + If you want to enable S3 replication metrics in your replication configuration, select **Replication metrics and events**\. For more information see, [Monitoring progress with replication metrics and Amazon S3 event notifications](replication-metrics.md)\.
   + If you want to enable S3 Replication Time Control \(S3 RTC\) in your replication configuration, select **S3 Replication Time Control**\. For more information about this option, see [Meeting compliance requirements using S3 Replication Time Control \(S3 RTC\)](replication-time-control.md)\.
**Note**  
When you use S3 RTC or S3 replication metrics, additional fees apply\.

1. To replicate objects in the source bucket that are encrypted with AWS Key Management Service \(AWS KMS\), under **Replication criteria**, select **Replicate objects encrypted with AWS KMS**\. Under **AWS KMS key for encrypting destination objects** are the source keys that you allow replication to use\. All source KMS keys are included by default\. You can choose to narrow the KMS key selection\. 

   Objects encrypted by AWS KMS keys that you do not select are not replicated\. A KMS key or a group of KMS keys is chosen for you, but you can choose the KMS keys if you want\. For information about using AWS KMS with replication, see [Replicating objects created with server\-side encryption \(SSE\) using KMS keys](replication-config-for-kms-objects.md)\.
**Important**  
When you replicate objects that are encrypted with AWS KMS, the AWS KMS request rate doubles in the source Region and increases in the destination Region by the same amount\. These increased call rates to AWS KMS are due to the way that data is re\-encrypted using the KMS key that you define for the replication destination Region\. AWS KMS has a request rate limit that is per calling account per Region\. For information about the limit defaults, see [AWS KMS Limits \- Requests per Second: Varies](https://docs.aws.amazon.com/kms/latest/developerguide/limits.html#requests-per-second) in the *AWS Key Management Service Developer Guide*\.   
If your current Amazon S3 PUT object request rate during replication is more than half the default AWS KMS rate limit for your account, we recommend that you request an increase to your AWS KMS request rate limit\. To request an increase, create a case in the AWS Support Center at [Contact Us](https://aws.amazon.com/contact-us/)\. For example, suppose that your current PUT object request rate is 1,000 requests per second and you use AWS KMS to encrypt your objects\. In this case, we recommend that you ask AWS Support to increase your AWS KMS rate limit to 2,500 requests per second, in both your source and destination Regions \(if different\), to ensure that there is no throttling by AWS KMS\.   
To see your PUT object request rate in the source bucket, view `PutRequests` in the Amazon CloudWatch request metrics for Amazon S3\. For information about viewing CloudWatch metrics, see [Using the S3 console](configure-request-metrics-bucket.md#configure-metrics)

   If you chose to replicate objects encrypted with AWS KMS, enter the Amazon Resource Name \(ARN\) of the AWS KMS key to use to encrypt the replicas in the destination bucket\. You can find the ARN for your KMS key in the IAM console, under **Encryption keys**\. Or, you can choose a KMS key name from the drop\-down list\.

   For more information about creating an AWS KMS key, see [Creating Keys](https://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html) in the *AWS Key Management Service Developer Guide*\.
**Important**  
The Amazon S3 console lists only 100 KMS keys per AWS Region\. If you have more than 100 KMS keys in the same Region, you can see only the first 100 KMS keys in the S3 console\. To use a KMS key that is not listed in the console, choose **Custom KMS ARN**, and enter the KMS key ARN\.

1. To finish, choose **Save**\.

1. After you save your rule, you can edit, enable, disable, or delete your rule by selecting your rule and choosing **Edit rule**\. 