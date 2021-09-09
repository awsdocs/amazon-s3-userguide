# Multi\-Region Access Point permissions<a name="MultiRegionAccessPointPermissions"></a>

 When you make a request through a Multi\-Region Access Point, Amazon S3 authorizes the request against the Multi\-Region Access Point and against the underlying bucket that the request is routed to\. Thus, for a request to succeed, both the Multi\-Region Access Point and at least one underlying bucket must permit the operation\. 

For example, suppose that you make a `GetObject` request through a Multi\-Region Access Point using a user called `AppDataReader` in your AWS account\. To help ensure that the request won't be denied, user `AppDataReader` must be granted the `s3:GetObject` permission by the Multi\-Region Access Point and by each bucket underlying the Multi\-Region Access Point\. `AppDataReader` won’t be able to retrieve data from any bucket that doesn’t grant this permission\. 

 In general, underlying buckets still have individual S3 Block Public Access settings, policies, and access control lists \(ACLs, including object ACLs\) that remain in effect in all cases\. 

## Managing public access to a Multi\-Region Access Point<a name="MultiRegionAccessPointPublicAccess"></a>

 Multi\-Region Access Points support independent Block Public Access settings for each Multi\-Region Access Point\. When you create a Multi\-Region Access Point, you can specify the Block Public Access settings that apply to that Multi\-Region Access Point\. 

For any request that is made through a Multi\-Region Access Point, Amazon S3 evaluates the Block Public Access settings for that Multi\-Region Access Point, the underlying buckets, and the account that owns both the Multi\-Region Access Point and underlying buckets\. If any of these settings indicate that the request should be blocked, Amazon S3 rejects the request\. For more information about the Amazon S3 Block Public Access feature, see [Blocking public access to your Amazon S3 storage](access-control-block-public-access.md)\. 

**Important**  
 All Block Public Access settings are enabled by default for Multi\-Region Access Points\. You must explicitly turn off any settings that you don't want to apply to a Multi\-Region Access Point\. Amazon S3 doesn't currently support changing the Block Public Access settings for a Multi\-Region Access Point after it has been created\. 

## Delegating access control to Multi\-Region Access Point policies<a name="DelegatingMultiRegionAccessPointAccess"></a>

 You can delegate access control for a bucket to the Multi\-Region Access Point access policy\. The following example bucket policy allows full access to all access points owned by the bucket owner's account\. This means that all access to this bucket is controlled by the policies that are attached to its access points\. We recommend configuring your buckets this way for all use cases that don't require direct access to the bucket\. 

```
 1. {
 2.     "Version": "2012-10-17",
 3.     "Statement" : [
 4.     {
 5.         "Effect": "Allow",
 6.         "Principal" : { "AWS": "*" },
 7.         "Action" : "*",
 8.         "Resource" : [ "Bucket ARN", "Bucket ARN/*"],
 9.         "Condition": {
10.             "StringEquals" : { "s3:DataAccessPointAccount" : "Bucket owner's account ID" }
11.         }
12.     }]
13. }
```

 The following example bucket policy delegates access control to any of the bucket’s Multi\-Region Access Points\. If you want to delegate access to specific Multi\-Region Access Points, you can use the `s3:DataAccessPointArn` condition key instead\. 

```
 1. {
 2.     "Version": "2012-10-17",
 3.     "Statement" : [
 4.     {
 5.         "Effect": "Allow",
 6.         "Principal" : { "AWS": "*" },
 7.         "Action" : "*",
 8.         "Resource" : [ "Bucket ARN", "Bucket ARN/*"],
 9.         "Condition": {
10.             "StringEquals" : { "s3:DataAccessPointArn" : "MRAP_ARN" }
11.         }
12.     }]
13. }
```