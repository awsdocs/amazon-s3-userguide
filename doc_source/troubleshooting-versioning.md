# Troubleshoot versioning<a name="troubleshooting-versioning"></a>

The following topics can help you troubleshoot some common Amazon S3 versioning issues\.

**Topics**
+ [I want to recover objects that were accidentally deleted in a versioning\-enabled bucket](#recover-objects)
+ [I want to permanently delete versioned objects](#delete-objects)
+ [I'm experiencing performance degradation after enabling bucket versioning](#performance-degradation)

## I want to recover objects that were accidentally deleted in a versioning\-enabled bucket<a name="recover-objects"></a>

In general, when object versions are deleted from S3 buckets, there is no way for Amazon S3 to recover them\. However, if you have enabled S3 Versioning on your S3 bucket, a `DELETE` request that doesn't specify a version ID cannot permanently delete an object\. Instead, a delete marker is added as a placeholder\. This delete marker becomes the current version of the object\. 

To verify whether your deleted objects are permanently deleted or temporarily deleted \(with a delete marker in their place\), do the following: 

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Buckets**\.

1. In the **Buckets** list, choose the name of the bucket that contains the object\.

1. In the **Objects** list, Turn on the **Show versions** toggle to the right of the search bar, and then search for the deleted object in the search bar\. This toggle is available only if versioning was previously enabled on the bucket\.

   You can also use [S3 Inventory to search for deleted objects](storage-inventory.md#storage-inventory-contents)\.

1. If you can't find the object after toggling **Show versions** or creating an inventory report, and you also cannot find a [delete marker](DeleteMarker.md) of the object, the deletion is permanent and the object cannot be recovered\.



You can also verify a deleted object's status by using the `HeadObject` API operation from the AWS Command Line Interface \(AWS CLI\)\. To do so, use the following `head-object` command and replace the `user input placeholders` with your own information: 

`aws s3api head-object --bucket DOC-EXAMPLE-BUCKET --key index.html`

If you run the `head-object` command on a versioned object whose current version is a delete marker, you will receive a 404 Not Found error\. For example: 

An error occurred \(404\) when calling the HeadObject operation: Not Found

If you run the `head-object` command on a versioned object and provide the object's version ID, Amazon S3 retrieves the object's metadata, confirming that the object still exists and is not permanently deleted\.

`aws s3api head-object --bucket DOC-EXAMPLE-BUCKET --key index.html --version-id versionID`

```
{
"AcceptRanges": "bytes",
"ContentType": "text/html",
"LastModified": "Thu, 16 Apr 2015 18:19:14 GMT",
"ContentLength": 77,
"VersionId": "Zg5HyL7m.eZU9iM7AVlJkrqAiE.0UG4q",
"ETag": "\"30a6ec7e1a9ad79c203d05a589c8b400\"",
"Metadata": {}
}
```

If the object is found and the newest version is a delete marker, the previous version of the object still exists\. Because the delete marker is the current version of the object, you can recover the object by deleting the delete marker\. 

After you permanently remove the delete marker, the second newest version of the object becomes the current version of the object, making your object available once again\. For a visual depiction of how objects are recovered, see [Removing delete markers](ManagingDelMarkers.md#RemDelMarker)\.

To remove a specific version of an object, you must be the bucket owner\. To delete a delete marker permanently, you must include its version ID in a `DeleteObject` request\. To delete the delete marker, use the following command, and replace the `user input placeholders` with your own information: 

****  
`aws s3api delete-object --bucket DOC-EXAMPLE-BUCKET --key index.html --version-id versionID`

For more information about the `delete-object` command, see [https://docs.aws.amazon.com/cli/latest/reference/s3api/delete-object.html](https://docs.aws.amazon.com/cli/latest/reference/s3api/delete-object.html) in the *AWS CLI Command Reference*\. For more information about permanently deleting delete markers, see [Managing delete markers](ManagingDelMarkers.md)\.

## I want to permanently delete versioned objects<a name="delete-objects"></a>

In a versioning\-enabled bucket, a `DELETE` request without a version ID cannot permanently delete an object\. Instead, such a request inserts a delete marker\.

To permanently delete versioned objects, you can choose from the following methods:
+ Create an S3 Lifecycle rule to permanently delete noncurrent versions\. To permanently delete noncurrent versions, select **Permanently delete noncurrent versions of objects**, and then enter a number under **Days after objects become noncurrent**\. You can optionally specify the number of newer versions to retain by entering a value under **Number of newer versions to retain**\. For more information about creating this rule, see [Setting an S3 Lifecycle configuration](how-to-set-lifecycle-configuration-intro.md)\.
+ Delete a specified version by including the version ID in the `DELETE` request\. For more information, see [How to delete versioned objects permanently](DeletingObjectVersions.md#delete-request-use-cases)\.
+ Create a lifecycle rule to expire current versions\. To expire current versions of objects, select **Expire current versions of objects**, and then add a number under **Days after object creation**\. For more information about creating this lifecycle rule, see [Setting an S3 Lifecycle configuration](how-to-set-lifecycle-configuration-intro.md)\.
+ To permanently delete all versioned objects and delete markers, create two lifecycle rules: one to expire current versions and permanently delete noncurrent versions of objects, and the other to delete expired object delete markers\.

In a versioning\-enabled bucket, a `DELETE` request that doesn't specify a version ID can remove only objects with a `NULL` version ID\. If the object was uploaded when versioning was enabled, a `DELETE` request that doesn't specify a version ID creates a delete marker of that object\.

**Note**  
For S3 Object Lock\-enabled buckets, a `DELETE` object request with a protected object version ID causes a 403 Access Denied error\. A `DELETE` object request without a version ID adds a delete marker as the newest version of the object with a 200 OK response\. Objects protected by Object Lock cannot be permanently deleted until their retention periods and legal holds are removed\. For more information, see [How S3 Object Lock works](object-lock-overview.md)\.

## I'm experiencing performance degradation after enabling bucket versioning<a name="performance-degradation"></a>

Performance degradation can occur on versioning\-enabled buckets if there are too many delete markers or versioned objects, and if best practices aren't followed\.

**Too many delete markers**  
After you enable versioning on a bucket, a `DELETE` request without a version ID made to an object creates a delete marker with a unique version ID\. Lifecycle configurations with an **Expire current versions of objects** rule add a delete marker with a unique version ID to every object\. Excessive delete markers can reduce performance in the bucket\.

When versioning is suspended on a bucket, Amazon S3 marks the version ID as `NULL` on newly created objects\. An expiration action in a versioning\-suspended bucket causes Amazon S3 to create a delete marker with `NULL` as the version ID\. In a versioning\-suspended bucket, a `NULL` delete marker is created for any delete request\. These `NULL` delete markers are also called expired object delete markers when all object versions are deleted and only a single delete marker remains\. If too many `NULL` delete markers accumulate, performance degradation in the bucket occurs\.

**Too many versioned objects**  
If a versioning\-enabled bucket contains objects with millions of versions, an increase in 503 Service Unavailable errors can occur\. If you notice a significant increase in the number of HTTP 503 Service Unavailable responses received for `PUT` or `DELETE` object requests to a versioning\-enabled bucket, you might have one or more objects in the bucket with millions of versions\. When you have objects with millions of versions, Amazon S3 automatically throttles requests to the bucket\. Throttling requests protects your bucket from an excessive amount of request traffic, which could potentially impede other requests made to the same bucket\. 

To determine which objects have millions of versions, use S3 Inventory\. S3 Inventory generates a report that provides a flat file list of the objects in a bucket\. For more information, see [Amazon S3 Inventory](storage-inventory.md)\.

To verify if there are high number of versioned objects in the bucket, use S3 Storage Lens metrics to view the **Current version object count**, **Noncurrent version object count**, and **Delete marker object count**\. For more information about Storage Lens metrics, see [Amazon S3 Storage Lens metrics glossary](storage_lens_metrics_glossary.md)\.

The Amazon S3 team encourages customers to investigate applications that repeatedly overwrite the same object, potentially creating millions of versions for that object, to determine whether the application is working as intended\. If you have a use case that requires millions of versions for one or more Amazon S3 objects, contact the AWS Support team for assistance with determining a better solution\.

**Best practices**  
To prevent versioning\-related performance degradation issues, we recommend that you employ the following best practices:
+ Enable a lifecycle rule to expire the previous versions of objects\. For example, you can create a lifecycle rule to expire noncurrent versions after 30 days of the object being noncurrent\. You can also retain multiple noncurrent versions if you don't want to delete all of them\. For more information, see [Setting an S3 Lifecycle configuration](how-to-set-lifecycle-configuration-intro.md)\.
+ Enable a lifecycle rule to delete expired object delete markers that don't have associated data objects in the bucket\. For more information, see [Removing expired object delete markers](lifecycle-configuration-examples.md#lifecycle-config-conceptual-ex7)\.

For additional Amazon S3 performance\-optimization best practices, see [Best practices design patterns](optimizing-performance.md)\.