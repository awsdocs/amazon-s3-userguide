# Deleting Amazon S3 objects<a name="DeletingObjects"></a>

You can delete one or more objects directly from Amazon S3 using the Amazon S3 console, AWS SDKs, AWS Command Line Interface \(AWS CLI\), or REST API\. Because all objects in your S3 bucket incur storage costs, you should delete objects that you no longer need\. For example, if you're collecting log files, it's a good idea to delete them when they're no longer needed\. You can set up a lifecycle rule to automatically delete objects such as log files\. For more information, see [Setting lifecycle configuration on a bucket](how-to-set-lifecycle-configuration-intro.md)\.

For information about Amazon S3 features and pricing, see [Amazon S3 pricing](http://aws.amazon.com/s3/pricing)\.

You have the following API options when deleting an object: 
+ **Delete a single object** – Amazon S3 provides the `DELETE` \(`DeleteObject`\) API operation that you can use to delete one object in a single HTTP request\. 
+ **Delete multiple objects** – Amazon S3 provides the Multi\-Object Delete \(`DeleteObjects`\) API operation that you can use to delete up to 1,000 objects in a single HTTP request\. 

When deleting objects from a bucket that is not version\-enabled, you provide only the object key name\. However, when deleting objects from a version\-enabled bucket, you can optionally provide the version ID of the object to delete a specific version of the object\. 

## Programmatically deleting objects from a version\-enabled bucket<a name="DeletingObjectsfromaVersion-EnabledBucket"></a>

If your bucket is version\-enabled, multiple versions of the same object can exist in the bucket\. When working with version\-enabled buckets, the delete API operations enable the following options:
+ **Specify a non\-versioned delete request** – Specify only the object's key, and not the version ID\. In this case, Amazon S3 creates a delete marker and returns its version ID in the response\. This makes your object disappear from the bucket\. For information about object versioning and the delete marker concept, see [Using versioning in S3 buckets](Versioning.md)\.
+ **Specify a versioned delete request** – Specify both the key and also a version ID\. In this case the following two outcomes are possible:
  + If the version ID maps to a specific object version, Amazon S3 deletes the specific version of the object\.
  + If the version ID maps to the delete marker of that object, Amazon S3 deletes the delete marker\. This makes the object reappear in your bucket\. 

## Deleting objects from an MFA\-enabled bucket<a name="DeletingObjectsfromanMFA-EnabledBucket"></a>

When deleting objects from a multi\-factor authentication \(MFA\)\-enabled bucket, note the following:
+ If you provide an MFA token that isn't valid, the request always fails\.
+ If you have an MFA\-enabled bucket and you make a versioned delete request \(you provide an object key and version ID\), the request fails if you don't provide a valid MFA token\. In addition, when using the Multi\-Object Delete API operation on an MFA\-enabled bucket, if any of the deletes are a versioned delete request \(that is, you specify an object key and version ID\), the entire request fails if you don't provide an MFA token\. 

However, in the following cases, the request succeeds:
+ If you have an MFA\-enabled bucket and you make a non\-versioned delete request \(you are not deleting a versioned object\),  and you don't provide an MFA token, the delete succeeds\. 
+ If you have a Multi\-Object Delete request that specifies only non\-versioned objects to delete from an MFA\-enabled bucket  and you don't provide an MFA token, the deletions succeed\.

For information about MFA delete, see [Configuring MFA delete](MultiFactorAuthenticationDelete.md)\.

**Topics**
+ [Programmatically deleting objects from a version\-enabled bucket](#DeletingObjectsfromaVersion-EnabledBucket)
+ [Deleting objects from an MFA\-enabled bucket](#DeletingObjectsfromanMFA-EnabledBucket)
+ [Deleting a single object](delete-objects.md)
+ [Deleting multiple objects](delete-multiple-objects.md)