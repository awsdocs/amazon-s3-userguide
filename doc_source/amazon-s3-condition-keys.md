# Amazon S3 condition keys<a name="amazon-s3-condition-keys"></a>

The access policy language enables you to specify conditions when granting permissions\. To specify conditions for when a policy is in effect, you can use the optional `Condition` element, or `Condition` block, to specify conditions for when a policy is in effect\. You can use predefined AWS‐wide keys and Amazon S3‐specific keys to specify conditions in an Amazon S3 access policy\.

In the `Condition` element, you build expressions in which you use Boolean operators \(equal, less than, etc\.\) to match your condition against values in the request\. For example, when granting a user permission to upload an object, the bucket owner can require that the object be publicly readable by adding the `StringEquals` condition, as shown here\.

```
{ 
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "statement1",
      "Effect": "Allow",
      "Action": "s3:PutObject",
      "Resource": [
        "arn:aws:s3:::awsexamplebucket1/*"
      ],
      "Condition": {
        "StringEquals": {
          "s3:x-amz-acl": "public-read"
        }
      }
    }
  ]
}
```

In the example, the `Condition` block specifies the `StringEquals` condition that is applied to the specified key\-value pair, `"s3:x-amz-acl":["public-read"]`\. There is a set of predefined keys that you can use in expressing a condition\. The example uses the `s3:x-amz-acl` condition key\. This condition requires the user to include the `x-amz-acl` header with value `public-read` in every PUT object request\.

**Topics**
+ [AWS‐wide condition keys](#AvailableKeys-iamV2)
+ [Amazon S3‐specific condition keys](#s3-specific-keys)
+ [Amazon S3 condition key examples](amazon-s3-policy-keys.md)

## AWS‐wide condition keys<a name="AvailableKeys-iamV2"></a>

AWS provides a set of common keys that are supported by all AWS services that support policies\. These keys are called *AWS‐wide keys* and use the prefix `aws:`\. For a complete list of AWS‐wide condition keys, see [Available AWS Keys for Conditions](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html) in the *IAM User Guide*\. You can use AWS‐wide condition keys in Amazon S3\. The following example bucket policy allows authenticated users permission to use the `s3:GetObject` action if the request originates from a specific range of IP addresses \(192\.0\.2\.0\.\*\), unless the IP address is 192\.0\.2\.188\. In the condition block, the `IpAddress` and the `NotIpAddress` are conditions, and each condition is provided a key\-value pair for evaluation\. Both the key\-value pairs in this example use the `aws:SourceIp` AWS‐wide key\.

**Note**  
The `IPAddress` and `NotIpAddress` key values specified in the condition uses CIDR notation as described in RFC 4632\. For more information, see [http://www\.rfc\-editor\.org/rfc/rfc4632\.txt](http://www.rfc-editor.org/rfc/rfc4632.txt)\.

```
{
    "Version": "2012-10-17",
    "Id": "S3PolicyId1",
    "Statement": [
        {
            "Sid": "statement1",
            "Effect": "Allow",
            "Principal": "*",
            "Action":"s3:GetObject",
            "Resource": "arn:aws:s3:::awsexamplebucket1/*",
            "Condition" : {
                "IpAddress" : {
                    "aws:SourceIp": "192.0.2.0/24" 
                },
                "NotIpAddress" : {
                    "aws:SourceIp": "192.0.2.188/32" 
                } 
            } 
        } 
    ]
}
```

You can also use other AWS‐wide condition keys in Amazon S3 policies\. For example, you can specify the `aws:SourceVpce` and `aws:SourceVpc` condition keys in bucket policies for VPC endpoints\. For specific examples, see [Controlling access from VPC endpoints with bucket policies](example-bucket-policies-vpc-endpoint.md)\.

**Note**  
For some AWS global condition keys, only certain resource types are supported\. Therefore, check whether Amazon S3 supports the global condition key and resource type that you want to use, or if you'll need to use an Amazon S3\-specific condition key instead\. For a complete list of supported resource types and condition keys for Amazon S3, see [Actions, resources, and conditions](https://docs.aws.amazon.com/AmazonS3/latest/userguide/list_amazons3.html)\.

## Amazon S3‐specific condition keys<a name="s3-specific-keys"></a>

You can use Amazon S3 condition keys with specific Amazon S3 actions\. Each condition key maps to the same name request header allowed by the API on which the condition can be set\. Amazon S3‐specific condition keys dictate the behavior of the same name request headers\. For a complete list of Amazon S3‐specific condition keys, see [Actions, resources, and condition keys for Amazon S3](list_amazons3.md)\.

For example, the condition key `s3:x-amz-acl` that you can use to grant condition permission for the `s3:PutObject` permission defines behavior of the `x-amz-acl` request header that the PUT Object API supports\. The condition key `s3:VersionId` that you can use to grant conditional permission for the `s3:GetObjectVersion` permission defines behavior of the `versionId` query parameter that you set in a GET Object request\.

The following bucket policy grants the `s3:PutObject` permission for two AWS accounts if the request includes the `x-amz-acl` header making the object publicly readable\. The `Condition` block uses the `StringEquals` condition, and it is provided a key\-value pair, `"s3:x-amz-acl":["public-read"`, for evaluation\. In the key\-value pair, the `s3:x-amz-acl` is an Amazon S3–specific key, as indicated by the prefix `s3:`\. 

```
{
    "Version":"2012-10-17",
    "Statement": [ 
        {
	        "Sid":"AddCannedAcl",
            "Effect":"Allow",
	        "Principal": {
                "AWS": [
                "arn:aws:iam::Account1-ID:root",
				"arn:aws:iam::Account2-ID:root"
				]
            },
	        "Action":"s3:PutObject",
            "Resource": ["arn:aws:s3:::awsexamplebucket1/*"],
            "Condition": {
                "StringEquals": {
                    "s3:x-amz-acl":["public-read"]
                }
            }
        }
    ]
}
```

**Important**  
Not all conditions make sense for all actions\. For example, it makes sense to include an `s3:LocationConstraint` condition on a policy that grants the `s3:CreateBucket` Amazon S3 permission\. However, it does not make sense to include this condition on a policy that grants the `s3:GetObject` permission\. Amazon S3 can test for semantic errors of this type that involve Amazon S3–specific conditions\. However, if you are creating a policy for an IAM user or role and you include a semantically invalid Amazon S3 condition, no error is reported because IAM cannot validate Amazon S3 conditions\. 