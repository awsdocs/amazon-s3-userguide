# Creating Multi\-Region Access Points<a name="CreatingMultiRegionAccessPoints"></a>

To create a Multi\-Region Access Point in Amazon S3, you specify the name, choose one bucket in each AWS Region that you want to serve requests for the Multi\-Region Access Point, and configure the Amazon S3 Block Public Access settings for the Multi\-Region Access Point\. You provide this information in a create request, which Amazon S3 processes asynchronously\. Amazon S3 provides a token that you can use to monitor the status of the asynchronous creation request\. 

Make sure to resolve security warnings, errors, general warnings, and suggestions from AWS Identity and Access Management Access Analyzer before you save your policy\. IAM Access Analyzer runs policy checks to validate your policy against IAM [policy grammar](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_grammar.html) and [best practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)\. These checks generate findings and provide actionable recommendations to help you author policies that are functional and conform to security best practices\. To learn more about validating policies using IAM Access Analyzer, see [IAM Access Analyzer policy validation](https://docs.aws.amazon.com/IAM/latest/UserGuide/access-analyzer-policy-validation.html) in the *IAM User Guide*\. To view a list of the warnings, errors, and suggestions that are returned by IAM Access Analyzer, see [IAM Access Analyzer policy check reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/access-analyzer-reference-policy-checks.html)\.

When you use the API, the request to create a Multi\-Region Access Point is asynchronous\. When you submit a request to create a Multi\-Region Access Point, Amazon S3 synchronously authorizes the request\. It then immediately returns a token that you can use to track the progress of the creation request\. For more information about tracking asynchronous requests to create and manage Multi\-Region Access Points, see [Managing Multi\-Region Access Points](ManagingMultiRegionAccessPoints.md)\. 

After you create the Multi\-Region Access Point, you can create an access control policy for it\. Each Multi\-Region Access Point can have an associated policy\. A Multi\-Region Access Point policy is a resource\-based policy that allows you to limit the use of the Multi\-Region Access Point by resource, user, or other conditions\.

**Note**  
 For an application or user to be able to access an object through a Multi\-Region Access Point, both the access policy for the Multi\-Region Access Point and the access policy for the underlying buckets that contain the object must permit the request\. When the two policies are different, the more restrictive policy takes precedence\. 

Using a bucket with a Multi\-Region Access Point does not change the bucket's behavior when the bucket is accessed through the existing bucket name or an Amazon Resource Name \(ARN\)\. All existing operations against the bucket continue to work as before\. Restrictions that you include in a Multi\-Region Access Point policy apply only to requests that are made through the Multi\-Region Access Point\. 

You can update the policy for a Multi\-Region Access Point after creating it, but you can't delete the policy\. The closest possible approximation to deleting a policy is to update the Multi\-Region Access Point policy to deny all permissions\. 

**Topics**
+ [Rules for naming Amazon S3 Multi\-Region Access Points](#multi-region-access-point-naming)
+ [Rules for choosing buckets for Amazon S3 Multi\-Region Access Points](#multi-region-access-point-buckets)
+ [Blocking public access with Amazon S3 Multi\-Region Access Points](#multi-region-access-point-block-public-access)
+ [Creating Amazon S3 Multi\-Region Access Points](#multi-region-access-point-create-examples)
+ [Configuring a Multi\-Region Access Point for use with AWS PrivateLink](MultiRegionAccessPointsPrivateLink.md)

## Rules for naming Amazon S3 Multi\-Region Access Points<a name="multi-region-access-point-naming"></a>

When you create a Multi\-Region Access Point, you give it a name, which is a string that you choose\. You can't change the name of the Multi\-Region Access Point after it is created\. The name must be unique in your AWS account, and it must conform to the naming requirements listed in [Multi\-Region Access Point restrictions and limitations](MultiRegionAccessPointRestrictions.md)\. To help you identify the Multi\-Region Access Point, use a name that is meaningful to you, to your organization, or that reflects the scenario\. 

You use this name when invoking Multi\-Region Access Point management operations, such as `GetMultiRegionAccessPoint` and `PutMultiRegionAccessPointPolicy`\. The name is not used to send requests to the Multi\-Region Access Point, and it doesn’t need to be exposed to clients who make requests using the Multi\-Region Access Point\. 

When Amazon S3 creates a Multi\-Region Access Point, it automatically assigns an alias to it\. This alias is a unique alphanumeric string that ends in `.mrap`\. The alias is used to construct the hostname and the Amazon Resource Name \(ARN\) for a Multi\-Region Access Point\. The fully qualified name is also based on the alias for the Multi\-Region Access Point\.

You can’t determine the name of a Multi\-Region Access Point from its alias, so you can disclose an alias without risk of exposing the name, purpose, or owner of the Multi\-Region Access Point\. Amazon S3 selects the alias for each new Multi\-Region Access Point, and the alias can’t be changed\. For more information about addressing a Multi\-Region Access Point, see [Making requests using a Multi\-Region Access Point](MultiRegionAccessPointRequests.md)\. 

Multi\-Region Access Point aliases are unique throughout time and aren’t based on the name or configuration of a Multi\-Region Access Point\. If you create a Multi\-Region Access Point, and then delete it and create another one with the same name and configuration, the second Multi\-Region Access Point will have a different alias than the first\. New Multi\-Region Access Points can never have the same alias as a previous Multi\-Region Access Point\.

## Rules for choosing buckets for Amazon S3 Multi\-Region Access Points<a name="multi-region-access-point-buckets"></a>

Each Multi\-Region Access Point is associated with the Regions where you want to fulfill requests\. The Multi\-Region Access Point must be associated with exactly one bucket in each of those Regions\. You specify the name of each bucket in the request to create the Multi\-Region Access Point\. Each bucket that supports the Multi\-Region Access Point must be owned by the same AWS account that owns the Multi\-Region Access Point\. 

 A single bucket can be used by multiple Multi\-Region Access Points\. 

**Important**  
 You can specify the buckets that are associated with a Multi\-Region Access Point only at the time that you create it\. After it is created, you can’t add, modify, or remove buckets from the Multi\-Region Access Point configuration\. To change the buckets, you must delete the entire Multi\-Region Access Point and create a new one\. 
You can't delete a bucket that is part of a Multi\-Region Access Point\. If you want to delete a bucket attached to a Multi\-Region Access Point, delete the Multi\-Region Access Point first\. 
 The AWS account that owns the Multi\-Region Access Point must also own the associated buckets\. For more information about using permissions with Multi\-Region Access Points, see [Multi\-Region Access Point permissions](MultiRegionAccessPointPermissions.md)\. 
 Not all Regions support Multi\-Region Access Points\. To see the list of supported Regions, see [Multi\-Region Access Point restrictions and limitations](MultiRegionAccessPointRestrictions.md)\. 

You can create replication rules to synchronize data between buckets\. These rules enable you to automatically copy data from source buckets to destination buckets\. Having buckets connected to a Multi\-Region Access Point does not affect how replication works\. Configuring replication with Multi\-Region Access Points is described in a later section\.

It is important to realize that when you make a request to a Multi\-Region Access Point, the Multi\-Region Access Point does not make any considerations about which bucket can fulfill the request\. This is why replication is recommended\. Otherwise, one of the buckets in the Multi\-Region Access Point might have the necessary data, but there's no way to guarantee it will receive the request\. For more information, see [Configuring bucket replication for use with Multi\-Region Access Points](MultiRegionAccessPointBucketReplication.md)\.

## Blocking public access with Amazon S3 Multi\-Region Access Points<a name="multi-region-access-point-block-public-access"></a>

Each Multi\-Region Access Point has distinct settings for Amazon S3 Block Public Access\. These settings operate in conjunction with the Block Public Access settings for the buckets that underly the Multi\-Region Access Point and for the AWS account that owns both the Multi\-Region Access Point and the underlying buckets\. 

When Amazon S3 authorizes a request, it applies the most restrictive combination of these settings\. If the Block Public Access settings for any of these resources \(the Multi\-Region Access Point, the underlying bucket, or the owner account\) block access for the requested action or resource, Amazon S3 rejects the request\. 

We recommend that you enable all Block Public Access settings unless you have a specific need to disable any of them\. By default, all Block Public Access settings are enabled for a Multi\-Region Access Point\. Be ware that if Block Public Access is enabled, the Multi\-Region Access Point will not be able to accept internet\-based requests\.

**Important**  
 Amazon S3 doesn’t currently support changing the Block Public Access settings for a Multi\-Region Access Point after it has been created\. 

 For more information about Amazon S3 Block Public Access, see [Blocking public access to your Amazon S3 storage](access-control-block-public-access.md)\. 

## Creating Amazon S3 Multi\-Region Access Points<a name="multi-region-access-point-create-examples"></a>

The following example demonstrates how to create a Multi\-Region Access Point using the AWS Management Console\. 

### Using the S3 console<a name="multi-region-access-point-create-console"></a>

**To create a Multi\-Region Access Point**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the navigation pane, choose **Multi\-Region Access Points**\.

1. In the **Multi\-Region Access Point name** field, supply a name for the Multi\-Region Access Point\.

1. To select the buckets that will be associated with this Multi\-Region Access Point, choose **Add buckets** \. 

   To create a new bucket, choose **Create bucket**\. After creating the bucket, choose **Add buckets** to add the bucket to the Multi\-Region Access Point\.

   For more information about creating buckets, see [Creating a bucket](create-bucket-overview.md)\.

1. Under **Block Public Access settings for this Multi\-Region Access Point**, select the Block Public Access settings that you want to apply to the Multi\-Region Access Point\. By default, all Block Public Access settings are enabled for new Multi\-Region Access Points\. We recommend that you leave all settings enabled unless you know that you have a specific need to disable any of them\. 
**Note**  
 Amazon S3 currently doesn't support changing a Multi\-Region Access Point's Block Public Access settings after the Multi\-Region Access Point has been created\. 

1. Choose **Create Multi\-Region Access Point**\.

### Using the AWS CLI<a name="multi-region-access-point-create-cli"></a>

 You can use the AWS CLI to create a Multi\-Region Access Point\. Remember that when you create the Multi\-Region Access Point, you need to provide all the buckets it will support\. There is no option to add buckets to the Multi\-Region Access Point after it has been created\. 

 The following example creates a Multi\-Region Access Point with two buckets using the AWS CLI\. 

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

**Topics**
+ [Rules for naming Amazon S3 Multi\-Region Access Points](#multi-region-access-point-naming)
+ [Rules for choosing buckets for Amazon S3 Multi\-Region Access Points](#multi-region-access-point-buckets)
+ [Blocking public access with Amazon S3 Multi\-Region Access Points](#multi-region-access-point-block-public-access)
+ [Creating Amazon S3 Multi\-Region Access Points](#multi-region-access-point-create-examples)
+ [Configuring a Multi\-Region Access Point for use with AWS PrivateLink](MultiRegionAccessPointsPrivateLink.md)