# Create an Amazon S3 Multi\-Region Access Point<a name="multi-region-access-point-create-examples"></a>

The following example demonstrates how to create a Multi\-Region Access Point by using the Amazon S3 console\.

## Using the S3 console<a name="multi-region-access-point-create-console"></a>

**To create a Multi\-Region Access Point**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Multi\-Region Access Points**\.

1. Choose **Create Multi\-Region Access Points** to begin creating your Multi\-Region Access Point\.

1. On the **Multi\-Region Access Point** page, supply a name for the Multi\-Region Access Point in the **Multi\-Region Access Point name** field\.

1. Select the buckets that will be associated with this Multi\-Region Access Point\. You can choose buckets that are in your account, or you can choose buckets from other accounts\.
**Note**  
You must add at least one bucket from either your account or other accounts\. Also, be aware that Multi\-Region Access Points support only one bucket per AWS Region\. Therefore, you can’t add two buckets from the same Region\. [AWS Regions that are disabled by default](https://docs.aws.amazon.com/general/latest/gr/rande-manage.html) are not supported\.
   + To add a bucket that is in your account, choose **Add buckets**\. A list of all the buckets in your account displays\. You can search for your bucket by name, or sort the bucket names in alphabetical order\.
   + To add a bucket from another account, choose **Add bucket from other accounts**\. Make sure that you know the exact bucket name and AWS account ID because you can't search or browse for buckets in other accounts\.
**Note**  
You must enter a valid AWS account ID and bucket name\. The bucket must also be in a supported Region, or you will encounter an error when you try to create your Multi\-Region Access Point\. For the list of Regions that support Multi\-Region Access Points, see [Multi\-Region Access Points restrictions and limitations](https://docs.aws.amazon.com/AmazonS3/latest/userguide/MultiRegionAccessPointRestrictions.html)\.

1. \(Optional\) If you need to remove a bucket that you added, choose **Remove**\.
**Note**  
You can’t add or remove buckets to this Multi\-Region Access Point after you’ve finished creating it\.

1. Under **Block Public Access settings for this Multi\-Region Access Point**, select the Block Public Access settings that you want to apply to the Multi\-Region Access Point\. By default, all Block Public Access settings are enabled for new Multi\-Region Access Points\. We recommend that you leave all settings enabled unless you know that you have a specific need to disable any of them\.
**Note**  
You can't change the Block Public Access settings for a Multi\-Region Access Point after the Multi\-Region Access Point has been created\. Therefore, if you're going to block public access, make sure that your applications work correctly without public access before you create a Multi\-Region Access Point\.

1. Choose **Create Multi\-Region Access Point**\.

**Important**  
When you add a bucket that's owned by another account to your Multi\-Region Access Point, the bucket owner must also update their bucket policy to grant access permissions to the Multi\-Region Access Point\. Otherwise, the Multi\-Region Access Point won't be able to retrieve data from that bucket\. For example policies that show how to grant such access, see [Multi\-Region Access Point policy examples](https://docs.aws.amazon.com/AmazonS3/latest/userguide/MultiRegionAccessPointPermissions.html#MultiRegionAccessPointPolicyExamples)\.

## Using the AWS CLI<a name="multi-region-access-point-create-cli"></a>

You can use the AWS CLI to create a Multi\-Region Access Point\. When you create the Multi\-Region Access Point, you must provide all the buckets that it will support\. You can't add buckets to the Multi\-Region Access Point after it has been created\. 

 The following example creates a Multi\-Region Access Point with two buckets by using the AWS CLI\. To use this example command, replace the `user input placeholders` with your own information\.

```
aws s3control create-multi-region-access-point --account-id 111122223333 --details '{
        "Name": "simple-multiregionaccesspoint-with-two-regions",
        "PublicAccessBlock": {
            "BlockPublicAcls": true,
            "IgnorePublicAcls": true,
            "BlockPublicPolicy": true,
            "RestrictPublicBuckets": true
        },
        "Regions": [
            { "Bucket": "DOC-EXAMPLE-BUCKET1" }, 
            { "Bucket": "DOC-EXAMPLE-BUCKET2" } 
        ]
    }' --region us-west-2
```