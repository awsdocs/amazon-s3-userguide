# Controlling ownership of objects and disabling ACLs for your bucket<a name="about-object-ownership"></a>

S3 Object Ownership is an Amazon S3 bucket\-level setting that you can use to disable [access control lists \(ACLs\)](acl-overview.md) and take ownership of every object in your bucket, simplifying access management for data stored in Amazon S3\. By default, when another AWS account uploads an object to your S3 bucket, that account \(the object writer\) owns the object, has access to it, and can grant other users access to it through ACLs\. You can use Object Ownership to change this default behavior\. With Object Ownership, ACLs are disabled, and you, as the bucket owner, automatically own every object in your bucket\. As a result, access control for your data is based on policies, such as IAM policies, S3 bucket policies, virtual private cloud \(VPC\) endpoint policies, and AWS Organizations service control policies \(SCPs\)\.

A majority of modern use cases in Amazon S3 no longer require the use of ACLs, and we recommend that you disable ACLs except in unusual circumstances where you need to control access for each object individually\. With Object Ownership, you can disable ACLs and rely on policies for access control\. When you disable ACLs, you can easily maintain a bucket with objects uploaded by different AWS accounts\. You, as the bucket owner, own all the objects in the bucket and can manage access to them using policies\.

Object Ownership has three settings that you can use to control ownership of objects uploaded to your bucket and to disable or enable ACLs:

**ACLs disabled**
+ **Bucket owner enforced \(recommended\)** – ACLs are disabled, and the bucket owner automatically owns and has full control over every object in the bucket\. ACLs no longer affect permissions to data in the S3 bucket\. The bucket uses policies to define access control\.

**ACLs enabled**
+ **Bucket owner preferred** – The bucket owner owns and has full control over new objects that other accounts write to the bucket with the `bucket-owner-full-control` canned ACL\. 
+ **Object writer \(default\)** – The AWS account that uploads an object owns the object, has full control over it, and can grant other users access to it through ACLs\.

For the majority of modern use cases in S3, we recommend that you disable ACLs by choosing the bucket owner enforced setting and use your bucket policy to share data with users outside of your account as needed\. This approach simplifies permissions management and auditing\. You can disable ACLs on both newly created and already existing buckets\. In the case of an existing bucket that already has objects in it, after you disable ACLs, the object and bucket ACLs are no longer part of an access evaluation, and access is granted or denied on the basis of policies\. For existing buckets, you can re\-enable ACLs at any time after you disable them, and your preexisting bucket and object ACLs are restored\.

Before you disable ACLs, we recommend that you review your bucket policy to ensure that it covers all the ways that you intend to grant access to your bucket outside of your account\. You must reset your bucket ACL to the default \(full control to the bucket owner\)\. After you disable ACLs, your bucket accepts only PUT requests that do not specify an ACL or PUT requests with bucket owner full control ACLs, such as the `bucket-owner-full-control` canned ACL or equivalent forms of this ACL expressed in XML\. Existing applications that support bucket owner full control ACLs see no impact\. PUT requests that contain other ACLs \(for example, custom grants to certain AWS accounts\) fail and return a `400` error with the error code `AccessControlListNotSupported`\. 

In contrast, a bucket with the bucket owner preferred setting continues to accept and honor bucket and object ACLs\. With this setting, new objects that are written with the `bucket-owner-full-control` canned ACL are automatically owned by the bucket owner rather than the object writer\. All other ACL behaviors remain in place\. To require all Amazon S3 PUT operations to include the `bucket-owner-full-control` canned ACL, you can [add a bucket policy](ensure-object-ownership.md#ensure-object-ownership-bucket-policy) that allows only object uploads using this ACL\.

## Object Ownership settings<a name="object-ownership-overview"></a>

This table shows the impact that each Object Ownership setting has on ACLs, objects, object ownership, and object uploads\. 


| Setting | Applies to | Effect on object ownership | Effect on ACLs | Uploads accepted | 
| --- | --- | --- | --- | --- | 
| Bucket owner enforced \(recommended\) | All new and existing objects | Bucket owner owns every object\. |  ACLs are disabled and no longer affect access permissions to your bucket\. Requests to set or update ACLs fail\. However, requests to read ACLs are supported\.  Bucket owner has full ownership and control\. Object writer no longer has full ownership and control\.  | Uploads with bucket owner full control ACLs or uploads that don't specify an ACL | 
| Bucket owner preferred | New objects | If an object upload includes the bucket\-owner\-full\-control canned ACL, the bucket owner owns the object\. Objects uploaded with other ACLs are owned by the writing account\. |  ACLs can be updated and can grant permissions\. If an object upload includes the `bucket-owner-full-control` canned ACL, the bucket owner has full control access, and the object writer no longer has full control access\.  | All uploads | 
| Object writer \(default\) | New objects | Object writer owns the object\. |  ACLs can be updated and can grant permissions\. Object writer has full control access\.  | All uploads | 

## Changes introduced by disabling ACLs<a name="object-ownership-changes"></a>

When you apply the bucket owner enforced setting for Object Ownership to disable ACLs, you automatically own and take full control over every object in the bucket without taking any additional actions\. After you apply this setting, you will see three changes: 
+ All bucket ACLs and object ACLs are disabled, which gives full access to you, as the bucket owner\. When you perform a read ACL request on your bucket or object, you will see that full access is given only to the bucket owner\.
+ You, as the bucket owner, automatically own and have full control over every object in your bucket\.
+ ACLs no longer affect access permissions to your bucket\. As a result, access control for your data is based on policies, such as IAM policies, S3 bucket policies, VPC endpoint policies, and Organizations SCPs\.

![\[Diagram showing what happens when you apply the bucket owner enforced setting to disable ACLs.\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/bucket-owner-enforced.png)

If you use S3 Versioning, the bucket owner owns and has full control over all object versions in your bucket\. Applying the bucket owner enforced setting does not add a new version of an object\.

New objects can be uploaded to your bucket only if they use bucket owner full control ACLs or don't specify an ACL\. Object uploads fail if they specify any other ACL\. For more information, see [Troubleshooting](object-ownership-error-responses.md)\.

Because the following example `PutObject` operation using the AWS Command Line Interface \(AWS CLI\) includes the `bucket-owner-full-control` canned ACL, the object can be uploaded to a bucket with disabled ACLs\.

```
aws s3api put-object --bucket DOC-EXAMPLE-BUCKET --key key-name --body path-to-file --acl bucket-owner-full-control
```

Because the following `PutObject` operation doesn't specify an ACL, it also succeeds for a bucket with disabled ACLs\.

```
aws s3api put-object --bucket DOC-EXAMPLE-BUCKET --key key-name --body path-to-file
```

**Note**  
If other AWS accounts need access to objects after uploading, you must grant additional permissions to those accounts through bucket policies\. For more information, see [Example walkthroughs: Managing access to your Amazon S3 resources](example-walkthroughs-managing-access.md)\.

**Re\-enabling ACLs**  
You can re\-enable ACLs by changing from the bucket owner enforced setting to another Object Ownership setting at any time\. If you used object ACLs for permissions management before you applied the bucket owner enforced setting and you didn't migrate these object ACL permissions to your bucket policy, after you re\-enable ACLs, these permissions are restored\. Additionally, objects written to the bucket while the bucket owner enforced setting was applied are still owned by the bucket owner\. 

For example, if you change from the bucket owner enforced setting back to object writer, you, as the bucket owner, no longer own and have full control over objects that were previously owned by other AWS accounts\. Instead, the uploading accounts again own these objects\. Objects owned by other accounts use ACLs for permissions, so you can't use policies to grant permissions to these objects\. However, you, as the bucket owner, still own any objects that were written to the bucket while the bucket owner enforced setting was applied\. These objects are not owned by the object writer, even if you re\-enable ACLs\.

For instructions on enabling and managing ACLs using the AWS Management Console, AWS Command Line Interface \(CLI\), REST API, or AWS SDKs, see [Configuring ACLs](managing-acls.md)\.

## Prerequisites for disabling ACLs<a name="object-ownership-considerations"></a>

Before you disable ACLs for an existing bucket, complete the following prerequisites\.

### Review bucket and object ACLs and migrate ACL permissions<a name="object-ownership-acl-permissions"></a>

When you disable ACLs, permissions granted by bucket and object ACLs no longer affect access\. Before you disable ACLs, review your bucket and object ACLs\. To identify Amazon S3 requests that required ACLs for authorization, you can use the `aclRequired` field in Amazon S3 server access logs or AWS CloudTrail\. For more information, see [Using Amazon S3 access logs to identify requests](using-s3-access-logs-to-identify-requests.md) and [Identifying Amazon S3 requests using CloudTrail](cloudtrail-request-identification.md)\. 

**Important**  
During the next few weeks, we are adding a new field, `aclRequired`, to Amazon S3 server access logs and AWS CloudTrail logs\. This field will indicate if your Amazon S3 requests required an access control list \(ACL\) for authorization\. You can use this information to migrate those ACL permissions to the appropriate bucket policies and disable ACLs\. This process is currently occurring across all AWS Regions, including the AWS GovCloud \(US\) Regions and the AWS China Regions\. If you don't see the `aclRequired` field, the rollout hasn't been completed in your Region\. 

If your bucket ACLs grant read or write permissions to others outside of your account, you must migrate these permissions to your bucket policy before you can apply the bucket owner enforced setting\. If you don't migrate bucket ACLs that grant read or write access outside of your account, your request to apply the bucket owner enforced setting fails and returns the [InvalidBucketAclWithObjectOwnership](object-ownership-error-responses.md#object-ownership-error-responses-invalid-acl) error code\. 

For example, if you want to disable ACLs for a bucket that receives server access logs, you must migrate the bucket ACL permissions for the S3 log delivery group to the logging service principal in a bucket policy\. For more information, see [Grant access to S3 log delivery group for server access logging](object-ownership-migrating-acls-prerequisites.md#object-ownership-server-access-logs)\.

If you want the object writer to maintain full control of the objects that they upload, object writer is the best Object Ownership setting for your use case\. If you want to control access at the individual object level, bucket owner preferred is the best choice\. These use cases are uncommon\.

To review ACLs and migrate ACL permissions to bucket policies, see [Prerequisites for disabling ACLs](object-ownership-migrating-acls-prerequisites.md)\.

### Review and update bucket policies that use ACL\-related condition keys<a name="object-ownership-bucket-policies"></a>

After you apply the bucket owner enforced setting to disable ACLs, new objects can be uploaded to your bucket only if the request uses bucket owner full control ACLs or doesn't specify an ACL\. Before disabling ACLs, review your bucket policy for ACL\-related condition keys\.

If your bucket policy uses an ACL\-related condition key to require the `bucket-owner-full-control` canned ACL \(for example, `s3:x-amz-acl`\), you don't need to update your bucket policy\. The following bucket policy uses the `s3:x-amz-acl` to require the `bucket-owner-full-control` canned ACL for S3 `PutObject` requests\. This policy *still* requires the object writer to specify the `bucket-owner-full-control` canned ACL\. However, buckets with ACLs disabled still accept this ACL, so requests continue to succeed with no client\-side changes required\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "Only allow writes to my bucket with bucket owner full control",
            "Effect": "Allow",
            "Principal": {
                "AWS": [
                    "arn:aws:iam::111122223333:user/ExampleUser"
                ]
            },
            "Action": [
                "s3:PutObject"
            ],
            "Resource": "arn:aws:s3:::DOC-EXAMPLE-BUCKET/*",
            "Condition": {
                "StringEquals": {
                    "s3:x-amz-acl": "bucket-owner-full-control"
                }
            }
        }
    ]
}
```

However, if your bucket policy uses an ACL\-related condition key that requires a different ACL, you must remove this condition key\. This example bucket policy requires the `public-read` ACL for S3 `PutObject` requests and therefore must be updated before disabling ACLs\. 

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "Only allow writes to my bucket with public read access",
            "Effect": "Allow",
            "Principal": {
                "AWS": [
                    "arn:aws:iam::111122223333:user/ExampleUser"                ]
            },
            "Action": [
                "s3:PutObject"
            ],
            "Resource": "arn:aws:s3:::DOC-EXAMPLE-BUCKET/*",
            "Condition": {
                "StringEquals": {
                    "s3:x-amz-acl": "public-read"
                }
            }
        }
    ]
}
```

## Object Ownership permissions<a name="object-ownership-permissions"></a>

To apply, update, or delete an Object Ownership setting for a bucket, you need the `s3:PutBucketOwnershipControls` permission\. To return the Object Ownership setting for a bucket, you need the `s3:GetBucketOwnershipControls` permission\. For more information, see [Setting Object Ownership when you create a bucket](object-ownership-new-bucket.md) and [Viewing the Object Ownership setting for an S3 bucket](object-ownership-retrieving.md)\.

## Disabling ACLs for all new buckets<a name="requiring-bucket-owner-enforced"></a>

You can require that all new buckets are created with ACLs disabled by using IAM or Organizations policies\. You can use the `s3:x-amz-object-ownership` condition key in an IAM or Organizations policy to require the bucket owner enforced setting for Object Ownership on all newly created buckets\. By requiring the bucket owner enforced setting, you ensure that ACLs are disabled for all new buckets in your account or organization\. For more information, see [Disabling ACLs for all new buckets \(bucket owner enforced\)](ensure-object-ownership.md#object-ownership-requiring-bucket-owner-enforced)\.

## Replication and Object Ownership<a name="object-ownership-replication"></a>

When you use S3 replication and the source and destination buckets are owned by different AWS accounts, you can disable ACLs \(with the bucket owner enforced setting for Object Ownership\) to change replica ownership to the AWS account that owns the destination bucket\. This setting mimics the existing owner override behavior without the need of the `s3:ObjectOwnerOverrideToBucketOwner` permission\. All objects that are replicated to the destination bucket with the bucket owner enforced setting are owned by the destination bucket owner\. For more information about the owner override option for replication configurations, see [Changing the replica owner](replication-change-owner.md)\. 

If you use the default object writer setting for Object Ownership or apply the bucket owner preferred setting for the destination bucket, you can use the S3 Replication owner override option to transfer ownership of replicated objects to the destination bucket owner\. 

## Setting Object Ownership<a name="object-ownership-setting"></a>

You can apply an Object Ownership setting using the S3 console, AWS CLI, AWS SDKs, Amazon S3 REST API, or AWS CloudFormation\. The following REST API and AWS CLI commands support Object Ownership:


| REST API | AWS CLI | Description | 
| --- | --- | --- | 
| [https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketOwnershipControls.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketOwnershipControls.html) | [https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3api/put-bucket-ownership-controls.html](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3api/put-bucket-ownership-controls.html)  | Creates or modifies the Object Ownership setting for an existing S3 bucket\. | 
| [https://docs.aws.amazon.com/AmazonS3/latest/API/API_CreateBucket.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_CreateBucket.html)  | [https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3api/create-bucket.html](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3api/create-bucket.html) | Creates a bucket using the x\-amz\-object\-ownership request header to specify the Object Ownership setting\.  | 
| [https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetBucketOwnershipControls.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetBucketOwnershipControls.html)  | [https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3api/get-bucket-ownership-controls.html](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3api/get-bucket-ownership-controls.html) | Retrieves the Object Ownership setting for an Amazon S3 bucket\.  | 
| [https://docs.aws.amazon.com/AmazonS3/latest/API/API_DeleteBucketOwnershipControls.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_DeleteBucketOwnershipControls.html) | [https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3api/delete-bucket-ownership-controls.html](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3api/delete-bucket-ownership-controls.html) | Deletes the Object Ownership setting for an Amazon S3 bucket\.  | 

For more information about applying and working with Object Ownership settings, see the following topics\.

**Topics**
+ [Object Ownership settings](#object-ownership-overview)
+ [Changes introduced by disabling ACLs](#object-ownership-changes)
+ [Prerequisites for disabling ACLs](#object-ownership-considerations)
+ [Object Ownership permissions](#object-ownership-permissions)
+ [Disabling ACLs for all new buckets](#requiring-bucket-owner-enforced)
+ [Replication and Object Ownership](#object-ownership-replication)
+ [Setting Object Ownership](#object-ownership-setting)
+ [Prerequisites for disabling ACLs](object-ownership-migrating-acls-prerequisites.md)
+ [Setting Object Ownership when you create a bucket](object-ownership-new-bucket.md)
+ [Setting Object Ownership on an existing bucket](object-ownership-existing-bucket.md)
+ [Viewing the Object Ownership setting for an S3 bucket](object-ownership-retrieving.md)
+ [Disabling ACLs for all new buckets and enforcing Object Ownership](ensure-object-ownership.md)
+ [Troubleshooting](object-ownership-error-responses.md)