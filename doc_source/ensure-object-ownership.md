# Disabling ACLs for all new buckets and enforcing Object Ownership<a name="ensure-object-ownership"></a>

We recommend that you disable ACLs on your Amazon S3 buckets\. You can do this by applying the Bucket owner enforced setting for S3 Object Ownership\. When you apply this setting, ACLs are disabled and you automatically own and have full control over all objects in your bucket\. To require that all new buckets are created with ACLs disabled, use AWS Identity and Access Management \(IAM\) policies or AWS Organizations service control policies \(SCPs\), as described in the next section\.

To enforce object ownership for new objects without disabling ACLs, you can apply the Bucket owner preferred setting\. When you apply this setting, we strongly recommend that you update your bucket policy to require the `bucket-owner-full-control` canned ACL for all `PUT` requests to your bucket\. Make sure you also update your clients to send the `bucket-owner-full-control` canned ACL to your bucket from other accounts\.

**Topics**
+ [Disabling ACLs for all new buckets \(bucket owner enforced\)](#object-ownership-requiring-bucket-owner-enforced)
+ [Requiring the bucket\-owner\-full\-control canned ACL for Amazon S3 `PUT` operations \(bucket owner preferred\)](#ensure-object-ownership-bucket-policy)

## Disabling ACLs for all new buckets \(bucket owner enforced\)<a name="object-ownership-requiring-bucket-owner-enforced"></a>

The following example IAM policy denies the `s3:CreateBucket` permission for a specific IAM user or role unless the Bucket owner enforced setting is applied for Object Ownership\. The key\-value pair in the `Condition` block specifies `s3:x-amz-object-ownership` as its key and the `BucketOwnerEnforced` setting as its value\. In other words, the IAM user can create buckets only if they set the Bucket owner enforced setting for Object Ownership and disable ACLs\. You can also use this policy as a boundary SCP for your AWS organization\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "RequireBucketOwnerFullControl",
            "Action": "s3:CreateBucket",
            "Effect": "Deny",
            "Resource": "*",
            "Condition": {
                "StringNotEquals": {
                    "s3:x-amz-object-ownership": "BucketOwnerEnforced"
                }
            }
        }
    ]
}
```

## Requiring the bucket\-owner\-full\-control canned ACL for Amazon S3 `PUT` operations \(bucket owner preferred\)<a name="ensure-object-ownership-bucket-policy"></a>

With the Bucket owner preferred setting for Object Ownership, you, as the bucket owner, own and have full control over new objects that other accounts write to your bucket with the `bucket-owner-full-control` canned ACL\. However, if other accounts write objects to your bucket without the `bucket-owner-full-control` canned ACL, the object writer maintains full control access\. You, as the bucket owner, can implement a bucket policy that allows writes only if they specify the `bucket-owner-full-control` canned ACL\.

**Note**  
If you have ACLs disabled with the Bucket owner enforced setting, you, as the bucket owner, automatically own and have full control over all the objects in your bucket\. You don't need to use this section to update your bucket policy to enforce object ownership for the bucket owner\.

The following bucket policy specifies that account *`111122223333`* can upload objects to *`DOC-EXAMPLE-BUCKET`* only when the object's ACL is set to `bucket-owner-full-control`\. Be sure to replace *`111122223333`* with your account and *`DOC-EXAMPLE-BUCKET`* with the name of your bucket\.

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

The following is an example copy operation that includes the `bucket-owner-full-control` canned ACL by using the AWS Command Line Interface \(AWS CLI\)\.

```
aws s3 cp file.txt s3://DOC-EXAMPLE-BUCKET --acl bucket-owner-full-control
```

After the bucket policy is put into effect, if the client does not include the `bucket-owner-full-control` canned ACL, the operation fails, and the uploader receives the following error: 

An error occurred \(AccessDenied\) when calling the PutObject operation: Access Denied\.

**Note**  
If clients need access to objects after uploading, you must grant additional permissions to the uploading account\. For information about granting accounts access to your resources, see [Example walkthroughs: Managing access to your Amazon S3 resources](example-walkthroughs-managing-access.md)\.