# Working with delete markers<a name="DeleteMarker"></a>

A *delete marker* in Amazon S3 is a placeholder \(or marker\) for a versioned object that was named in a simple `DELETE` request\. Because the object is in a versioning\-enabled bucket, the object is not deleted\. But the delete marker makes Amazon S3 behave as if it is deleted\. 

A delete marker has a key name \(or key\) and version ID like any other object\. However, a delete marker differs from other objects in the following ways:
+ It does not have data associated with it\.
+ It is not associated with an access control list \(ACL\) value\.
+ It does not retrieve anything from a `GET` request because it has no data; you get a 404 error\.
+ The only operation that you can use on a delete marker is an Amazon S3 API `DELETE` call\. To do this, you must make the `DELETE` request using an AWS Identity and Access Management \(IAM\) user or role with the appropriate permissions\.

Delete markers accrue a minimal charge for storage in Amazon S3\. The storage size of a delete marker is equal to the size of the key name of the delete marker\. A key name is a sequence of Unicode characters\. The UTF\-8 encoding adds 1‐4 bytes of storage to your bucket for each character in the name\. Delete markers are stored in the S3 Standard storage class\. If you want to find out how many delete markers you have and what storage class they are stored in, you can use Amazon S3 Storage Lens\. For more information, see [Assessing your storage activity and usage with Amazon S3 Storage Lens](storage_lens.md) and [Amazon S3 Storage Lens metrics glossary](storage_lens_metrics_glossary.md)\.

For more information about key names, see [Creating object key names](object-keys.md)\. For information about deleting a delete marker, see [Managing delete markers](ManagingDelMarkers.md)\.  

Only Amazon S3 can create a delete marker, and it does so whenever you send a `DELETE Object` request on an object in a versioning\-enabled or suspended bucket\. The object named in the `DELETE` request is not actually deleted\. Instead, the delete marker becomes the current version of the object\. The object's key name \(or key\) becomes the key of the delete marker\. If you try to get an object and its current version is a delete marker, Amazon S3 responds with the following:
+ A 404 \(Object not found\) error
+ A response header, `x-amz-delete-marker: true`

If you make an API call on an object whose current version is a delete marker and the request is for an action that can't be performed on a delete marker, Amazon S3 responds with the following:
+ A 405 \(Method Not Allowed\) error
+ A response header, `x-amz-delete-marker: true`

The response header tells you that the object accessed was a delete marker\. This response header never returns `false`\. If the value is `false`, Amazon S3 does not include this response header in the response\.

The following figure shows how a simple `GET` on an object whose current version is a delete marker, returns a 404 No Object Found error\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/versioning_DELETE_NoObjectFound.png)

The only way to list delete markers \(and other versions of an object\) is by using the `versions` subresource in a `GET Bucket versions` request\. A simple `GET` does not retrieve delete marker objects\. The following figure shows that a `GET Bucket` request does not return objects whose current version is a delete marker\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/versioning_GETBucketwithDeleteMarkers.png)