# Creating access points restricted to a virtual private cloud<a name="access-points-vpc"></a>

When you create an access point, you can choose to make the access point accessible from the internet, or you can specify that all requests made through that access point must originate from a specific virtual private cloud \(VPC\)\. An access point that's accessible from the internet is said to have a network origin of `Internet`\. It can be used from anywhere on the internet, subject to any other access restrictions in place for the access point, underlying bucket, and related resources, such as the requested objects\. An access point that's only accessible from a specified VPC has a network origin of `VPC`, and Amazon S3 rejects any request made to the access point that doesn't originate from that VPC\.

**Important**  
You can only specify an access point's network origin when you create the access point\. After you create the access point, you can't change its network origin\.

To restrict an access point to VPC\-only access, you include the `VpcConfiguration` parameter with the request to create the access point\. In the `VpcConfiguration` parameter, you specify the VPC ID that you want to be able to use the access point\. Amazon S3 then rejects requests made through the access point unless they originate from that VPC\.

You can retrieve an access point's network origin using the AWS CLI, AWS SDKs, or REST APIs\. If an access point has a VPC configuration specified, its network origin is `VPC`\. Otherwise, the access point's network origin is `Internet`\.

**Example**  
***Example: Create an access point Restricted to VPC Access***  
The following example creates an access point named `example-vpc-ap` for bucket `example-bucket` in account `123456789012` that allows access only from VPC `vpc-1a2b3c`\. The example then verifies that the new access point has a network origin of `VPC`\.  

```
aws s3control create-access-point --name example-vpc-ap --account-id 123456789012 --bucket example-bucket --vpc-configuration VpcId=vpc-1a2b3c
```

```
aws s3control get-access-point --name example-vpc-ap --account-id 123456789012

{
    "Name": "example-vpc-ap",
    "Bucket": "example-bucket",
    "NetworkOrigin": "VPC",
    "VpcConfiguration": {
        "VpcId": "vpc-1a2b3c"
    },
    "PublicAccessBlockConfiguration": {
        "BlockPublicAcls": true,
        "IgnorePublicAcls": true,
        "BlockPublicPolicy": true,
        "RestrictPublicBuckets": true
    },
    "CreationDate": "2019-11-27T00:00:00Z"
}
```

To use an access point with a VPC, you must modify the access policy for your VPC endpoint\. VPC endpoints allow traffic to flow from your VPC to Amazon S3\. They have access\-control policies that control how resources within the VPC are allowed to interact with S3\. Requests from your VPC to S3 only succeed through an access point if the VPC endpoint policy grants access to both the access point and the underlying bucket\.

The following example policy statement configures a VPC endpoint to allow calls to `GetObject` for a bucket named `awsexamplebucket1` and an access point named `example-vpc-ap`\.

```
{
    "Version": "2012-10-17",
    "Statement": [
    {
        "Principal": "*",
        "Action": [
            "s3:GetObject"
        ],
        "Effect": "Allow",
        "Resource": [
            "arn:aws:s3:::awsexamplebucket1/*",
            "arn:aws:s3:us-west-2:123456789012:accesspoint/example-vpc-ap/object/*"
        ]
    }]
}
```

**Note**  
The `"Resource"` declaration in this example uses an Amazon Resource Name \(ARN\) to specify the access point\. For more information about access point ARNs, see [Using access points](using-access-points.md)\. 

For more information about VPC endpoint policies, see [Using Endpoint Policies for Amazon S3](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints-s3.html#vpc-endpoints-policies-s3) in the *virtual private cloud \(VPC\) User Guide*\.