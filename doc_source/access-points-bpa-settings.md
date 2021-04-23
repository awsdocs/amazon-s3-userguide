# Managing public access to access points<a name="access-points-bpa-settings"></a>

Amazon S3 access points support independent *block public access* settings for each access point\. When you create an access point, you can specify block public access settings that apply to that access point\. For any request made through an access point, Amazon S3 evaluates the block public access settings for that access point, the underlying bucket, and the bucket owner's account\. If any of these settings indicate that the request should be blocked, Amazon S3 rejects the request\.

For more information about the S3 Block Public Access feature, see [Blocking public access to your Amazon S3 storage](access-control-block-public-access.md)\.

**Important**  
All block public access settings are enabled by default for access points\. You must explicitly disable any settings that you don't want to apply to an access point\.
Amazon S3 currently doesn't support changing an access point's block public access settings after the access point has been created\.

**Example**  
***Example: Create an access point with Custom Block Public Access Settings***  
This example creates an access point named `example-ap` for bucket `example-bucket` in account `123456789012` with non\-default Block Public Access settings\. The example then retrieves the new access point's configuration to verify its Block Public Access settings\.  

```
aws s3control create-access-point --name example-ap --account-id 123456789012 --bucket example-bucket --public-access-block-configuration BlockPublicAcls=false,IgnorePublicAcls=false,BlockPublicPolicy=true,RestrictPublicBuckets=true
```

```
aws s3control get-access-point --name example-ap --account-id 123456789012

{
    "Name": "example-ap",
    "Bucket": "example-bucket",
    "NetworkOrigin": "Internet",
    "PublicAccessBlockConfiguration": {
        "BlockPublicAcls": false,
        "IgnorePublicAcls": false,
        "BlockPublicPolicy": true,
        "RestrictPublicBuckets": true
    },
    "CreationDate": "2019-11-27T00:00:00Z"
}
```