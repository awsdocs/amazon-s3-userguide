# Bucket naming rules<a name="bucketnamingrules"></a>

The following rules apply for naming buckets in Amazon S3:
+ Bucket names must be between 3 \(min\) and 63 \(max\) characters long\.
+ Bucket names can consist only of lowercase letters, numbers, dots \(\.\), and hyphens \(\-\)\.
+ Bucket names must begin and end with a letter or number\.
+ Bucket names must not contain two adjacent periods\.
+ Bucket names must not be formatted as an IP address \(for example, 192\.168\.5\.4\)\.
+ Bucket names must not start with the prefix `xn--`\.
+ Bucket names must not end with the suffix `-s3alias`\. This suffix is reserved for access point alias names\. For more information, see [Using a bucket\-style alias for your S3 bucket access point](access-points-alias.md)\.
+ Bucket names must be unique across all AWS accounts in all the AWS Regions within a partition\. A partition is a grouping of Regions\. AWS currently has three partitions: `aws` \(Standard Regions\), `aws-cn` \(China Regions\), and `aws-us-gov` \(AWS GovCloud \(US\)\)\. 
+ A bucket name cannot be used by another AWS account in the same partition until the bucket is deleted\.
+ Buckets used with Amazon S3 Transfer Acceleration can't have dots \(\.\) in their names\. For more information about Transfer Acceleration, see [Configuring fast, secure file transfers using Amazon S3 Transfer Acceleration](transfer-acceleration.md)\.

For best compatibility, we recommend that you avoid using dots \(\.\) in bucket names, except for buckets that are used only for static website hosting\. If you include dots in a bucket's name, you can't use virtual\-host\-style addressing over HTTPS, unless you perform your own certificate validation\. This is because the security certificates used for virtual hosting of buckets don't work for buckets with dots in their names\. 

This limitation doesn't affect buckets used for static website hosting, because static website hosting is only available over HTTP\. For more information about virtual\-host\-style addressing, see [Virtual hosting of buckets](VirtualHosting.md)\. For more information about static website hosting, see [Hosting a static website using Amazon S3](WebsiteHosting.md)\.

**Note**  
Before March 1, 2018, buckets created in the US East \(N\. Virginia\) Region could have names that were up to 255 characters long and included uppercase letters and underscores\. Beginning March 1, 2018, new buckets in US East \(N\. Virginia\) must conform to the same rules applied in all other Regions\.

 For information on object key names, see [Creating object key names](https://docs.aws.amazon.com/en_us/AmazonS3/latest/userguide/object-keys.html)\.

## Example bucket names<a name="bucket-names"></a>

The following example bucket names are valid and follow the recommended naming guidelines:
+ `docexamplebucket1`
+ `log-delivery-march-2020`
+ `my-hosted-content`

The following example bucket names are valid but not recommended for uses other than static website hosting:
+ `docexamplewebsite.com`
+ `www.docexamplewebsite.com`
+ `my.example.s3.bucket`

The following example bucket names are *not* valid:
+ `doc_example_bucket` \(contains underscores\)
+ `DocExampleBucket` \(contains uppercase letters\)
+ `doc-example-bucket-` \(ends with a hyphen\)