--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Using the S3 console<a name="enable-replication"></a>

Replication is the automatic, asynchronous copying of objects across buckets in the same or different AWS Regions\. Replication copies newly created objects and object updates from a source bucket to a destination bucket or buckets\. For more information see [Replicating objects](replication.md)\.

You use the Amazon S3 console to add replication rules to the source bucket\. Replication rules define which source bucket objects to replicate and the destination bucket or buckets where the replicated objects are stored\. You can create a rule to replicate all the objects in a bucket or a subset of objects with a specific key name prefix, one or more object tags, or both\. A destination bucket can be in the same AWS account as the source bucket, or it can be in a different account\.

If you specify an object version ID to delete, Amazon S3 deletes that object version in the source bucket\. But it doesn't replicate the deletion in the destination bucket\. In other words, it doesn't delete the same object version from the destination bucket\. This protects data from malicious deletions\.

When you add a replication rule to a bucket, the rule is enabled by default, so it starts working as soon as you save it\. 

**Topics**
+ [Adding a replication rule](#enable-replication-add-rule)
+ [Grant the source bucket owner permission to encrypt using the AWS KMS CMK](#enable-replication-kms-key-policy)

## Adding a replication rule<a name="enable-replication-add-rule"></a>

Follow these steps to configure a replication rule when the destination bucket is in the same AWS account as the source bucket\.

If the destination bucket is in a different account from the source bucket, you must add a bucket policy to the destination bucket to grant the owner of the source bucket account permission to replicate objects in the destination bucket\. For more information, see [Granting permissions when source and destination buckets are owned by different AWS accounts](setting-repl-config-perm-overview.md#setting-repl-config-crossacct)\.

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the name of the bucket that you want\.

1. Choose **Management**, scroll down to **Replication rules**, and then choose **Create replication rule**\.

    

1. Under **Rule name**, enter a name for your rule to help identify the rule later\. The name is required and must be unique within the bucket\.

1. Set up an AWS Identity and Access Management \(IAM\) role that Amazon S3 can assume to replicate objects on your behalf\.

   To set up an IAM role, on the **Replication rule configuration** section, under **IAM role**, do one of the following:
   + We highly recommend that you choose **Create new role** to have Amazon S3 create a new IAM role for you\. When you save the rule, a new policy is generated for the IAM role that matches the source and destination buckets that you choose\. The name of the generated role is based on the bucket names and uses the following naming convention: **replication\_role\_for\_*source\-bucket*\_to\_*destination\-bucket***\.
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

   The new schema supports prefix and tag filtering and the prioritization of rules\. For more information about the new schema, see [Backward compatibility](replication-add-config.md#replication-backward-compat-considerations)\. The developer guide describes the XML used with the Amazon S3 API that works behind the user interface\. In the developer guide, the new schema is described as *replication configuration XML V2*\.

1. Under **Destination**, select the bucket where you want Amazon S3 to replicate objects\.
**Note**  
 The number of destination buckets is limited to the number of AWS Regions in a given partition\. A partition is a grouping of Regions\. AWS currently has three partitions: `aws` \(Standard Regions\), `aws-cn` \(China Regions\), and `aws-us-gov` \(AWS GovCloud \[US\] Regions\)\. You can use [service quotas](https://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html) to request an increase in your destination bucket limit\.
   + To replicate to a bucket or buckets in your account, select **Choose a bucket in this account**, and enter or browse for the destination bucket names\. 
   + To replicate to a bucket or buckets in a different AWS account, select **Choose a bucket in another account**, and enter the destination bucket account ID and name\. 

     If the destination is in a different account from the source bucket, you must add a bucket policy to the destination buckets to grant the owner of the source bucket account permission to replicate objects\. For more information, see [Granting permissions when source and destination buckets are owned by different AWS accounts](setting-repl-config-perm-overview.md#setting-repl-config-crossacct)\.
**Note**  
If versioning is not enabled on the destination bucket, you get a warning that contains an **Enable versioning** button\. Choose this button to enable versioning on the bucket\.

1. You have the following additional options while setting the **Destination**:
   + If you want to enable **Object Ownership** to help standardize ownership of new objects in the destination bucket, choose **Change object ownership to the destination bucket owner**\. For more information about this option, see [Controlling object ownership of uploaded objects using S3 Object Ownership](about-object-ownership.md)\.
   + If you want to replicate your data into a specific storage class in the destination, choose **Change the storage class for the replicated objects**\. Then choose the storage class that you want to use for the replicated objects in the destination\. If you don't choose this option, the storage class for replicated objects is the same class as the original objects\. 
   + If you want to enable delete marker replication in your replication configuration, select **Delete marker replication**\. For more information see, [Replicating delete markers between buckets](delete-marker-replication.md)\.
   + If you want to enable Amazon S3 replica modification sync in your replication configuration, select **Replica modification sync**\. For more information see, [Replicating metadata changes with Amazon S3 replica modification sync](replication-for-metadata-changes.md)\.
   + If you want to enable S3 replication metrics in your replication configuration, select **Replication metrics and events**\. For more information see, [Monitoring progress with replication metrics and Amazon S3 event notifications](replication-metrics.md)\.
   + If you want to enable S3 Replication Time Control \(S3 RTC\) in your replication configuration, select **S3 Replication Time Control**\. For more information about this option, see [Meeting compliance requirements using S3 Replication Time Control \(S3 RTC\)](replication-time-control.md)\.
**Note**  
When you use S3 RTC or S3 replication metrics, additional fees apply\.

1. To replicate objects in the source bucket that are encrypted with AWS Key Management Service \(AWS KMS\), under **Replication criteria**, select **Replicate objects encrypted with AWS KMS**\. Under **AWS KMS key for encrypting destination objects** are the source keys that you allow replication to use\. All source CMKs are included by default\. You can choose to narrow the CMK selection\. 

   Objects encrypted by AWS KMS CMKs that you do not select are not replicated\. A CMK or a group of CMKs is chosen for you, but you can choose the CMKs if you want\. For information about using AWS KMS with replication, see [Replicating objects created with server\-side encryption \(SSE\) using AWS KMS CMKs](replication-config-for-kms-objects.md)\.
**Important**  
When you replicate objects that are encrypted with AWS KMS, the AWS KMS request rate doubles in the source Region and increases in the destination Region by the same amount\. These increased call rates to AWS KMS are due to the way that data is re\-encrypted using the customer master key \(CMK\) that you define for the replication destination Region\. AWS KMS has a request rate limit that is per calling account per Region\. For information about the limit defaults, see [AWS KMS Limits \- Requests per Second: Varies](https://docs.aws.amazon.com/kms/latest/developerguide/limits.html#requests-per-second) in the *AWS Key Management Service Developer Guide*\.   
If your current Amazon S3 PUT object request rate during replication is more than half the default AWS KMS rate limit for your account, we recommend that you request an increase to your AWS KMS request rate limit\. To request an increase, create a case in the AWS Support Center at [Contact Us](https://aws.amazon.com/contact-us/)\. For example, suppose that your current PUT object request rate is 1,000 requests per second and you use AWS KMS to encrypt your objects\. In this case, we recommend that you ask AWS Support to increase your AWS KMS rate limit to 2,500 requests per second, in both your source and destination Regions \(if different\), to ensure that there is no throttling by AWS KMS\.   
To see your PUT object request rate in the source bucket, view `PutRequests` in the Amazon CloudWatch request metrics for Amazon S3\. For information about viewing CloudWatch metrics, see [Using the S3 console](configure-request-metrics-bucket.md#configure-metrics)

   If you chose to replicate objects encrypted with AWS KMS, enter the Amazon Resource Name \(ARN\) of the AWS KMS CMK to use to encrypt the replicas in the destination bucket\. You can find the ARN for your AWS KMS CMK in the IAM console, under **Encryption keys**\. Or, you can choose a CMK name from the drop\-down list\.

   For more information about creating an AWS KMS CMK, see [Creating Keys](https://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html) in the *AWS Key Management Service Developer Guide*\.
**Important**  
The Amazon S3 console lists only 100 AWS KMS CMKs per AWS Region\. If you have more than 100 CMKs in the same Region, you can see only the first 100 CMKs in the S3 console\. To use a KMS CMK that is not listed in the console, choose **Custom KMS ARN**, and enter the KMS CMK ARN\.

1. To finish, choose **Save**\.

1. After you save your rule, you can edit, enable, disable, or delete your rule by selecting your rule and choosing **Edit rule**\. 

## Grant the source bucket owner permission to encrypt using the AWS KMS CMK<a name="enable-replication-kms-key-policy"></a>

You must grant permissions to the account of the source bucket owner to encrypt using your AWS KMS CMK with a key policy\. The following procedure describes how to use the AWS Identity and Access Management \(IAM\) console to modify the key policy for the AWS KMS CMK that is being used to encrypt the replica objects in the destination bucket\.

**To grant permissions to encrypt using your AWS KMS CMK**

1. Sign in to the AWS Management Console using the AWS account that owns the AWS KMS CMK\. Open the AWS KMS console at [https://console\.aws\.amazon\.com/kms](https://console.aws.amazon.com/kms)\.

1. Choose the alias of the CMK that you want to encrypt with\.

1. In the **Key Policy** section of the page, choose **Switch to policy view**\.

1. Choose **Edit** to edit **Key Policy**\.

1. Using the **Key Policy** editor, insert the key policy provided by Amazon S3 into the existing key policy, and then choose **Save Changes**\. You might want to add the policy to the end of the existing policy\. 

For more information about creating and editing AWS KMS CMKs, see [Getting Started](https://docs.aws.amazon.com/kms/latest/developerguide/getting-started.html) in the *AWS Key Management Service Developer Guide*\. 