# Using access points<a name="using-access-points"></a>

You can access the objects in an Amazon S3 bucket with an *access point* using the AWS Management Console, AWS CLI, AWS SDKs, or the S3 REST APIs\.

Access points have Amazon Resource Names \(ARNs\)\. Access point ARNs are similar to bucket ARNs, but they are explicitly typed and encode the access point's Region and the AWS account ID of the access point's owner\. For more information about ARNs, see [Amazon Resource Names \(ARNs\)](https://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html) in the *AWS General Reference*\.

Access point ARNs use the format `arn:aws:s3:region:account-id:accesspoint/resource`\. For example:
+ **arn:aws:s3:us\-west\-2:123456789012:accesspoint/test** represents the access point named `test`, owned by account `123456789012` in Region `us-west-2`\.
+ **arn:aws:s3:us\-west\-2:123456789012:accesspoint/\*** represents all access points under account `123456789012` in Region `us-west-2`\.

ARNs for objects accessed through an access point use the format `arn:aws:s3:region:account-id:accesspoint/access-point-name/object/resource`\. For example:
+ **arn:aws:s3:us\-west\-2:123456789012:accesspoint/test/object/unit\-01** represents the object `unit-01`, accessed through the access point named `test`, owned by account `123456789012` in Region `us-west-2`\.
+ **arn:aws:s3:us\-west\-2:123456789012:accesspoint/test/object/\*** represents all objects for access point `test`, in account `123456789012` in Region `us-west-2`\.
+ **arn:aws:s3:us\-west\-2:123456789012:accesspoint/test/object/unit\-01/finance/\*** represents all objects under prefix `unit-01/finance/` for access point `test`, in account `123456789012` in Region `us-west-2`\.

**Topics**
+ [Monitoring and logging access points](access-points-monitoring-logging.md)
+ [Using Amazon S3 access points with the Amazon S3 console](access-points-manage.md)
+ [Using a bucket\-style alias for your S3 bucket access point](access-points-alias.md)
+ [Using access points with compatible Amazon S3 operations](access-points-usage-examples.md)

If you have a Virtual Private Cloud \(VPC\), see [Managing Amazon S3 access with VPC endpoints and S3 Access Points](https://aws.amazon.com/blogs/storage/managing-amazon-s3-access-with-vpc-endpoints-and-s3-access-points/)\.