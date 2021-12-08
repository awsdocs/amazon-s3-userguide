# Troubleshooting<a name="object-ownership-error-responses"></a>

When you apply the bucket owner enforced setting for S3 Object Ownership, access control lists \(ACLs\) are disabled and you, as the bucket owner, automatically own all objects in your bucket\. ACLs no longer affect permissions for the objects in your bucket\. You can use policies to grant permissions\. All S3 PUT requests must specify bucket owner full control ACLs or not specify an ACL, or they fail\. For more information, see [Controlling ownership of objects and disabling ACLs for your bucket](about-object-ownership.md)\.

If an invalid ACL is specified or bucket ACL permissions grant access outside of your AWS account, you might see the following error responses\.

## AccessControlListNotSupported<a name="object-ownership-error-responses-acl-not-supported"></a>

After you apply the bucket owner enforced setting for Object Ownership, ACLs are disabled\. Requests to set ACLs or update ACLs fail with a `400` error and return the AccessControlListNotSupported error code\. Requests to read ACLs are still supported\. Requests to read ACLs always return a response that shows full control for the bucket owner\. In your PUT operations, you must either specify bucket owner full control ACLs or not specify an ACL, or your PUT operations fail\. 

The following example `put-object` operation using the AWS CLI includes the `public-read` canned ACL\. 

```
aws s3api put-object --bucket DOC-EXAMPLE-BUCKET --key object-key-name --body doc-example-body --acl public-read
```

If the bucket uses the bucket owner enforced setting to disable ACLs, this operation fails, and the uploader receives the following error message:

An error occurred \(AccessControlListNotSupported\) when calling the PutObject operation: The bucket does not allow ACLs

## InvalidBucketAclWithObjectOwnership<a name="object-ownership-error-responses-invalid-acl"></a>

If you want to apply the bucket owner enforced setting to disable ACLs, your bucket ACL must give full control only to the bucket owner\. Your bucket ACL cannot give access to an external AWS account or any other group\. For example, if your `CreateBucket` request sets bucket owner enforced and specifies a bucket ACL that provides access to an external AWS account, your request fails with a `400` error and returns the InvalidBucketAclWithObjectOwnership error code\. Similarly, if your `PutBucketOwnershipControls` request sets bucket owner enforced on a bucket that has a bucket ACL that grants permissions to others, the request fails\.

**Example : Existing bucket ACL grants public read access**  
For example, if an existing bucket ACL grants public read access, you cannot apply the bucket owner enforced setting for Object Ownership until you migrate these ACL permissions to a bucket policy and reset your bucket ACL to the default private ACL\. For more information, see [Prerequisites for disabling ACLs](object-ownership-migrating-acls-prerequisites.md)\.  
This example bucket ACL grants public read access:  

```
{
    "Owner": {
        "ID": "852b113e7a2f25102679df27bb0ae12b3f85be6BucketOwnerCanonicalUserID"
    },
    "Grants": [
        {
            "Grantee": {
                "ID": "852b113e7a2f25102679df27bb0ae12b3f85be6BucketOwnerCanonicalUserID",
                "Type": "CanonicalUser"
            },
            "Permission": "FULL_CONTROL"
        },
        {
            "Grantee": {
                "Type": "Group",
                "URI": "http://acs.amazonaws.com/groups/global/AllUsers"
            },
            "Permission": "READ"
        }
    ]
}
```
This example `put-bucket-ownership-controls` AWS CLI operation applies the bucket owner enforced setting for Object Ownership:  

```
aws s3api put-bucket-ownership-controls --bucket DOC-EXAMPLE-BUCKET --ownership-controls Rules=[{ObjectOwnership=BucketOwnerEnforced}]
```
Because the bucket ACL grants public read access, the request fails and returns the following error code:  
An error occurred \(InvalidBucketAclWithObjectOwnership\) when calling the PutBucketOwnershipControls operation: Bucket cannot have ACLs set with ObjectOwnership's BucketOwnerEnforced setting 