# Bucket restrictions and limitations<a name="BucketRestrictions"></a>

An Amazon S3 bucket is owned by the AWS account that created it\. Bucket ownership is not transferable to another account\.

When you create a bucket, you choose its name and the AWS Region to create it in\. After you create a bucket, you can't change its name or Region\.

When naming a bucket, choose a name that is relevant to you or your business\. Avoid using names associated with others\. For example, you should avoid using `AWS` or `Amazon` in your bucket name\.

By default, you can create up to 100 buckets in each of your AWS accounts\. If you need additional buckets, you can increase your account bucket limit to a maximum of 1,000 buckets by submitting a service limit increase\. There is no difference in performance whether you use many buckets or just a few\. 

For information about how to increase your bucket limit, see [AWS service quotas](https://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html) in the *AWS General Reference*\. 

**Reusing bucket names**  
If a bucket is empty, you can delete it\. After a bucket is deleted, the name becomes available for reuse\. However, after you delete the bucket, you might not be able to reuse the name for various reasons\.

For example, when you delete the bucket and the name becomes available for reuse, another AWS account might create a bucket with that name\. In addition, some time might pass before you can reuse the name of a deleted bucket\. If you want to use the same bucket name, we recommend that you don't delete the bucket\.

For more information about bucket names, see [Bucket naming rules](bucketnamingrules.md)\.

**Objects and bucket limitations**  
There is no max bucket size or limit to the number of objects that you can store in a bucket\. You can store all of your objects in a single bucket, or you can organize them across several buckets\. However, you can't create a bucket from within another bucket\.

**Bucket operations**  
The high availability engineering of Amazon S3 is focused on *get*, *put*, *list*, and *delete* operations\. Because bucket operations work against a centralized, global resource space, it is not recommended to create, delete, or configure buckets on the high availability code path of your application\. It's better to create, delete, or configure buckets in a separate initialization or setup routine that you run less often\. 

**Bucket naming and automatically created buckets**  
If your application automatically creates buckets, choose a bucket naming scheme that is unlikely to cause naming conflicts\. Ensure that your application logic will choose a different bucket name if a bucket name is already taken\.

For more information about bucket naming, see [Bucket naming rules](bucketnamingrules.md)\.