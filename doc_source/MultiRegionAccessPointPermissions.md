# Multi\-Region Access Point permissions<a name="MultiRegionAccessPointPermissions"></a>

Amazon S3 Multi\-Region Access Points can simplify data access for Amazon S3 buckets in multiple AWS Regions\. Multi\-Region Access Points are named global endpoints that you can use to perform Amazon S3 data\-access object operations, such as `GetObject` and `PutObject`\. Each Multi\-Region Access Point can have distinct permissions and network controls for any request that is made through the global endpoint\.

Each Multi\-Region Access Point can also enforce a customized access policy that works in conjunction with the bucket policy that is attached to the underlying bucket\. For a request to succeed, all of the following must permit the operation:
+ The Multi\-Region Access Point policy
+ The underlying AWS Identity and Access Management \(IAM\) policy
+ The underlying bucket policy \(where the request is routed to\)

You can configure any Multi\-Region Access Point policy to accept requests only from specific IAM users or groups\. For an example of how to do this, see Example 2 in [Multi\-Region Access Point policy examples](#MultiRegionAccessPointPolicyExamples)\. To restrict Amazon S3 data access to a private network, you can configure the Multi\-Region Access Point policy to accept requests only from a virtual private cloud \(VPC\)\.

For example, suppose that you make a `GetObject` request through a Multi\-Region Access Point by using a user called `AppDataReader` in your AWS account\. To help ensure that the request won't be denied, the `AppDataReader` user must be granted the `s3:GetObject` permission by the Multi\-Region Access Point and by each bucket underlying the Multi\-Region Access Point\. `AppDataReader` won't be able to retrieve data from any bucket that doesn't grant this permission\.

In most cases, underlying buckets that have individual S3 Block Public Access settings, policies, and access control lists \(ACLs, including object ACLs\) will still remain in effect\.

## Managing public access to a Multi\-Region Access Point<a name="MultiRegionAccessPointPublicAccess"></a>

Multi\-Region Access Points support independent Block Public Access settings for each Multi\-Region Access Point\. When you create a Multi\-Region Access Point, you can specify the Block Public Access settings that apply to that Multi\-Region Access Point\. 

For any request that is made through a Multi\-Region Access Point, Amazon S3 evaluates the Block Public Access settings for:
+ The Multi\-Region Access Point
+ The underlying buckets
+ The account that owns both the Multi\-Region Access Point and the underlying buckets

If any of these settings indicate that the request should be blocked, Amazon S3 rejects the request\. For more information about the Amazon S3 Block Public Access feature, see [Blocking public access to your Amazon S3 storage](access-control-block-public-access.md)\. 

**Important**  
By default, all Block Public Access settings are enabled for Multi\-Region Access Points\. You must explicitly turn off any settings that you don't want to apply to a Multi\-Region Access Point\.   
Amazon S3 doesn't currently support changing the Block Public Access settings for a Multi\-Region Access Point after it has been created\. 

## Viewing Block Public Access settings for a Multi\-Region Access Point<a name="viewing-bpa-mrap-settings"></a>

**To view the Block Public Access settings for a Multi\-Region Access Point**

1. Sign in to the AWS Management Console\.

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Multi\-Region Access Points**\.

1. Choose the Multi\-Region Access Point that you want to review\.

1. Choose the **Permissions** tab\.

1. Under **Block Public Access settings for this Multi\-Region Access Point**, review the Block Public Access settings for your Multi\-Region Access Point\.
**Note**  
You can't edit the Block Public Access settings after the Multi\-Region Access Point is created\. Therefore, if you're going to block public access, make sure that your applications work correctly without public access before you create a Multi\-Region Access Point\. 

## Using a Multi\-Region Access Point policy<a name="use-mrap-policy"></a>

The following example Multi\-Region Access Point policy grants an AWS Identity and Access Management \(IAM\) user access to list and download files from your Multi\-Region Access Point\. To use this example policy, replace the `user input placeholders` with your own information\.

```
 {
   "Version":"2012-10-17",
   "Statement":[
      {
         "Effect":"Allow",
         "Principal":{
            "AWS":"arn:aws:iam::111122223333:JohnDoe"
         },
         "Action":[
            "s3:ListBucket",
            "s3:GetObject"
         ],
         "Resource":[
            "arn:aws:s3::111122223333:accesspoint/MultiRegionAccessPoint_alias",
            "arn:aws:s3::111122223333:accesspoint/MultiRegionAccessPoint_alias/object/*"
         ]
      }
   ]
}
```

To associate your Multi\-Region Access Point policy with the specified Multi\-Region Access Point, use the following syntax\. Each Multi\-Region Access Point can have only one policy, so a request made to this action replaces any existing policy that is associated with the specified Multi\-Region Access Point\.

------
#### [ AWS CLI ]

```
aws s3control put-multi-region-access-point-policy
--account-id 111122223333
--details { "Name": "DOC-EXAMPLE-BUCKET-MultiRegionAccessPoint", "Policy": "{ \"Version\": \"2012-10-17\", \"Statement\": { \"Effect\": \"Allow\", \"Principal\": { \"AWS\": \"arn:aws:iam::111122223333:root\" }, \"Action\": [\"s3:ListBucket\", \"s3:GetObject\"], \"Resource\": [ \"arn:aws:s3::111122223333:accesspoint/MultiRegionAccessPoint_alias", \"arn:aws:s3::111122223333:accesspoint/MultiRegionAccessPoint_alias/object/*\" ] } }" }
```

------

To query your results for the previous operation, use the following syntax:

------
#### [ AWS CLI ]

```
aws s3control describe-multi-region-access-point-operation
--account-id 111122223333
--request-token-arn requestArn
```

------

To retrieve your Multi\-Region Access Point policy, use the following syntax:

------
#### [ AWS CLI ]

```
aws s3control get-multi-region-access-point-policy
--account-id 111122223333
--name=DOC-EXAMPLE-BUCKET-MultiRegionAccessPoint
```

------

## Editing the Multi\-Region Access Point policy<a name="editing-mrap-policy"></a>

The Multi\-Region Access Point policy \(written in JSON\) provides storage access to the Amazon S3 buckets that are used with this Multi\-Region Access Point\. You can allow or deny specific principals to perform various actions on your Multi\-Region Access Point\. When a request is routed to a bucket through the Multi\-Region Access Point, both the access policies for the Multi\-Region Access Point and the bucket apply\. The more restrictive access policy always takes precedence\. 

**Note**  
Multi\-Region Access Point policies can't be applied to objects that are owned by other AWS accounts\.

After you apply a Multi\-Region Access Point policy, the policy cannot be deleted\. You can either edit the policy or create a new policy that overwrites the existing one\.

**To edit the Multi\-Region Access Point policy**



1. Sign in to the AWS Management Console\.

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Multi\-Region Access Points**\.

1. Choose the Multi\-Region Access Point that you want to edit the policy for\.

1. Choose the **Permissions** tab\.

1. Scroll down to the **Multi\-Region Access Point policy** section\. Choose **Edit** to update the policy \(in JSON\)\.

1. The **Edit Multi\-Region Access Point policy** page appears\. You can either enter the policy directly into the text field, or you can choose **Add statement** to select policy elements from a dropdown list\.
**Note**  
The console automatically displays the Multi\-Region Access Point Amazon Resource Name \(ARN\), which you can use in the policy\. For example Multi\-Region Access Point policies, see [Multi\-Region Access Point policy examples](https://docs.aws.amazon.com/AmazonS3/latest/userguide/MultiRegionAccessPointPermissions.html#MultiRegionAccessPointPolicyExamples)\.

## Multi\-Region Access Point policy examples<a name="MultiRegionAccessPointPolicyExamples"></a>

Amazon S3 Multi\-Region Access Points support AWS Identity and Access Management \(IAM\) resource policies, allowing you to control the use of the Multi\-Region Access Point by resource, user, or other conditions\. For an application or user to be able to access objects through a Multi\-Region Access Point, both the Multi\-Region Access Point and the underlying bucket must allow the same access\.

To allow the same access to both the Multi\-Region Access Point and the underlying bucket, do one of the following:
+ **\(Recommended\)** To simplify access controls when using an Amazon S3 Multi\-Region Access Point, delegate access control for the Amazon S3 bucket to the Multi\-Region Access Point\. For an example of how to do this, see Example 1 in this section\. 
+ Add the same permissions contained in the Multi\-Region Access Point policy to the underlying bucket policy\.

**Important**  
Delegating access control for a bucket to a Multi\-Region Access Point policy doesn't change the bucket's behavior when the bucket is accessed directly through its bucket name or Amazon Resource Name \(ARN\)\. All operations made directly against the bucket will continue to work as before\. Restrictions that you include in a Multi\-Region Access Point policy apply only to requests made through that Multi\-Region Access Point\.

**Example 1 – Delegating access to specific Multi\-Region Access Points in your bucket policy**  
The following example bucket policy allows full access to specific Multi\-Region Access Points\. This means that all access to this bucket is controlled by the policies that are attached to the Multi\-Region Access Points\. We recommend configuring your buckets this way for all use cases that don't require direct access to the bucket\.  

```
{
    "Version": "2012-10-17",
    "Statement" : [
    {
        "Effect": "Allow",
        "Principal" : { "AWS": "*" },
        "Action" : "*",
        "Resource" : [ "Bucket ARN", "Bucket ARN/*"],
        "Condition": {
            "StringEquals" : { "s3:DataAccessPointArn" : "MultiRegionAccessPoint_ARN" }
        }
    }]
}
```
If there are multiple Multi\-Region Access Points in the bucket owner's account that you're granting access to, make sure to list each Multi\-Region Access Point\.

**Example 2 – Granting access to a Multi\-Region Access Point in your Multi\-Region Access Point policy**  
The following Multi\-Region Access Point policy allows account `123456789012` permission to view the objects contained in the Multi\-Region Access Point *`021345abcdef6.mrap`*\.  

```
{
   "Version":"2012-10-17",
   "Statement": [
      {
       "Effect": "Allow",
       "Principal": {
        "AWS": "arn:aws:iam::123456789012:user/JohnDoe"
        },
        "Action": "s3:ListBucket",
        "Resource": "arn:aws:s3::123456789012:accesspoint/zzzzzzzzzzzzz.mrap"
       }
    ]
}
```