--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# View and edit the properties of the Amazon S3 on Outposts buckets with Amazon S3 console<a name="s3-outposts-edit-bucket-properties"></a>

With Amazon S3 on Outposts, you can create S3 buckets on your AWS Outposts and easily store and retrieve objects on\-premises for applications that require local data access, local data processing, and data residency\. S3 on Outposts provides a new storage class, `OUTPOSTS` which uses the S3 APIs, and is designed to durably and redundantly store data across multiple devices and servers on your AWS Outposts\. You communicate with your Outposts bucket using an access point and endpoint connection over a virtual private cloud \(VPC\)\. You can use the same APIs and features on Outposts buckets as you do on Amazon S3, such as access policies, encryption, and tagging\. You can use S3 on Outposts through the AWS Management Console, AWS CLI, AWS SDKs, or REST API\. For more information, see [Using Amazon S3 on Outposts](S3onOutposts.md)\.

**Note**  
The AWS account that creates the bucket owns it and is the only one that can commit actions to it\. Buckets have configuration properties, including Outpost, tags, default encryption, access point settings including, VPC and an access point policy for accessing the objects in the bucket, and other metadata\. For more information see, [Amazon S3 on Outposts specifications](S3OnOutpostsRestrictionsLimitations.md#S3OnOutpostsSpecifications)\.  
The Amazon S3 console doesn’t support S3 on Outposts object actions\. For that you will need to use the REST API, AWS CLI, or AWS SDKs\.

As a bucket owner, you can change the following for your S3 on Outposts bucket:

**Topics**
+ [Add and remove tag for the Amazon S3 on Outposts buckets with Amazon S3 console](s3-outposts-add-bucket-tags.md)
+ [Add your S3 on Outposts bucket to CloudTrail](s3-outposts-add-bucket-events-cloudtrail.md)
+ [Manage your S3 on Outposts bucket permissions](s3-outposts-bucket-edit-permissions.md)
+ [Managing your S3 on Outposts bucket's lifecycle rules](#s3-outposts-bucket-lifecycle)
+ [Managing your S3 on Outposts bucket access points](s3-outposts-bucket-edit-outpost-access-point.md)

## Managing your Amazon S3 on Outposts bucket's lifecycle rules with Amazon S3 console<a name="s3-outposts-bucket-lifecycle"></a>

Lifecycle rules for S3 on Outposts buckets are limited to object deletion and stopping incomplete multipart uploads\. You can Use lifecycle rules to define when to trigger object deletion based on age or date\. You can create, enable, disable, delete a lifecycle rule\.

**Note**  
The AWS account that creates the bucket owns it and is the only one can create, enable, disable, delete a lifecycle rule\.

### To create your Amazon S3 on Outposts bucket's lifecycle rule with Amazon S3 console<a name="s3-outposts-bucket-create-lifecycle"></a>

**To create an S3 on Outposts bucket lifecycle rule:**

1. Sign in to the [Amazon S3 console](https://console.aws.amazon.com/s3)\.

1. Choose **Outposts buckets** from the left navigation menu\.

1. Choose the Outposts bucket that you want to create a lifecyle rule for\.

1. Choose the **Management** tab and choose **Create Lifecycle rule**\.

1. In the **lifecycle rule configuration** section:

1. Enter the **lifecycle rule name**

1. 

   1. Choose**rule scope**\.
**Warning**  
If you want the rule to apply to specific objects, you must use a filter to identify those objects\. Choose "Limit the scope to specific objects or tags\."

   1. If selected, add a prefix filter to limit the scope of this rule to a single prefix\.

   1. If selected, add a tags to limit the scope of this rule to the key value pairs added below\.

1. In the **lifecycle rule trigger** section, choose the **rule trigger** based on a specific date or object's age\.



### To enable or disable your Amazon S3 on Outposts bucket's lifecycle rule with Amazon S3 console<a name="s3-outposts-bucket-enable-lifecycle"></a>

**To enable or disable an S3 on Outposts bucket lifecycle rule:**

1. Sign in to the [Amazon S3 console](https://console.aws.amazon.com/s3)\.

1. Choose **Outposts buckets** from the left navigation menu\.

1. Choose the Outposts bucket that you want to enable pr disable a lifecyle rule for\.

1. Choose the **Management** tab and choose the **Lifecycle rule** that you want to enable or disable \.

1. Choose **Enable or disable rule** from the **Action** menu\.



### To delete your Amazon S3 on Outposts bucket's lifecycle rule with Amazon S3 console<a name="s3-outposts-bucket-delete-lifecycle"></a>

**To delete an S3 on Outposts bucket lifecycle rule:**

1. Sign in to the [Amazon S3 console](https://console.aws.amazon.com/s3)\.

1. Choose **Outposts buckets** from the left navigation menu\.

1. Choose the Outposts bucket that you want to delete a lifecyle rule for\.

1. Choose the **Management** tab and choose the **Lifecycle rule** that you want to delete\.

1. Choose the **Delete** button\.



### To create your Amazon S3 on Outposts bucket's lifecycle rule with Amazon S3 console<a name="s3-outposts-bucket-edit-lifecycle"></a>

**To edit an S3 on Outposts bucket lifecycle rule:**

1. Sign in to the [Amazon S3 console](https://console.aws.amazon.com/s3)\.

1. Choose **Outposts buckets** from the left navigation menu\.

1. Choose the Outposts bucket that you want to edit a lifecyle rule for\.

1. Choose the **Management** tab and choose **Lifecycle rule** that you want to edit\.

1. In the **lifecycle rule configuration** section:

1. Update the **lifecycle rule name**

1. 

   1. Update**rule scope**\.
**Warning**  
If you want the rule to apply to specific objects, you must use a filter to identify those objects\. Choose "Limit the scope to specific objects or tags\."

   1. If selected, add a prefix filter to limit the scope of this rule to a single prefix\.

   1. If selected, add a tags to limit the scope of this rule to the key value pairs added below\.

1. In the **lifecycle rule trigger** section, update the **rule trigger** based on a specific date or object's age\.

