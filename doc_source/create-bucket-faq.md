# Default settings for new S3 buckets FAQ<a name="create-bucket-faq"></a>

Starting in April 2023, Amazon S3 will change the default settings for S3 Block Public Access and S3 Object Ownership \(ACLs disabled\) for all new S3 buckets\. For new buckets created after this update, all S3 Block Public Access settings will be enabled, and S3 access control lists \(ACLs\) will be disabled\. These defaults are the recommended best practices for securing data in Amazon S3\. You can adjust these settings after creating your bucket\. For more information, see [Heads\-Up: Amazon S3 Security Changes Are Coming in April of 2023](http://aws.amazon.com/blogs/aws/heads-up-amazon-s3-security-changes-are-coming-in-april-of-2023/) in the * AWS News Blog*\.

The following sections answer questions about this update that will occur in April 2023\. 

**In April 2023, will the new default settings take effect for all newly created buckets?**  
Yes\. During the month of April in 2023, the new default settings will gradually be rolled out across all AWS Regions, including the AWS GovCloud \(US\) Regions and the AWS China Regions\. 

**How long will it take before this rollout covers all AWS Regions?**  
This update will take several weeks to roll out\. We will publish a What's New post when we start to deploy this update\.

**How will I know that the update is complete?**  
We will announce completion of the rollout in a What's New post\. After the update is complete, all new S3 buckets will automatically have Block Public Access enabled and ACLs disabled by default\. You can adjust these settings after creating your S3 bucket\. 

**Will Amazon S3 update my existing bucket configurations?**  
No\. There will be no changes to the configuration of existing buckets\. After the update is complete for your AWS Region, the new default settings will apply only to new S3 buckets\.

**Can I enable Block Public Access or disable ACLs for new buckets before the update is complete?**  
Yes\. You can enable Block Public Access or disable ACLs after you create an S3 bucket before this update is complete for your AWS Region\. For more information about these settings, see [Blocking public access to your Amazon S3 storage](access-control-block-public-access.md) and [Controlling ownership of objects and disabling ACLs for your bucket](about-object-ownership.md)\.

**How do I prepare for this update?**  
The majority of S3 use cases don't require public access or ACLs\. For most customers, no action is required\. If you have use cases that require public bucket access or the use of ACLs, you can disable Block Public Access or enable ACLs after you create an S3 bucket\. In these cases, you might need to update automation scripts, AWS CloudFormation templates, or other infrastructure configuration tools to configure these settings\. For more information, see [Blocking public access to your Amazon S3 storage](access-control-block-public-access.md) and [Controlling ownership of objects and disabling ACLs for your bucket](about-object-ownership.md)\. Also, see [https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-s3-bucket-publicaccessblockconfiguration.html](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-s3-bucket-publicaccessblockconfiguration.html) and [https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-s3-bucket-ownershipcontrols.html](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-s3-bucket-ownershipcontrols.html) in the *AWS CloudFormation User Guide*\. 

**Are there any changes to Amazon S3 pricing as a result of this change?**  
No\. You will be billed for storage, requests, and other Amazon S3 features as you normally would\. For pricing, see [Amazon S3 pricing](http://aws.amazon.com/s3/pricing/)\. 