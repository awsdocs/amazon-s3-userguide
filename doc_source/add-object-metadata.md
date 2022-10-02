# Editing object metadata in the Amazon S3 console<a name="add-object-metadata"></a>

You can use the Amazon S3 console to edit metadata of existing S3 objects\. Some metadata is set by Amazon S3 when you upload the object\. For example, `Content-Length` is the *key* \(name\) and the *value* is the size of the object in bytes\.

You can also set some metadata when you upload the object and later edit it as your needs change\. For example, you might have a set of objects that you initially store in the `STANDARD` storage class\. Over time, you might no longer need this data to be highly available\. So you change the storage class to `GLACIER` by editing the value of the `x-amz-storage-class` key from `STANDARD` to `GLACIER`\.

**Note**  
Consider the following issues when you are editing object metadata in Amazon S3:  
This action creates a *copy* of the object with updated settings and the last\-modified date\. If S3 Versioning is enabled, a new version of the object is created, and the existing object becomes an older version\. If S3 Versioning is not enabled, a new copy of the object replaces the original object\. The AWS account associated with the IAM role that changes the property also becomes the owner of the new object or \(object version\)\.
Editing metadata updates values for existing key names\.
Objects that are encrypted with customer\-provided encryption keys \(SSE\-C\) cannot be copied using the console\. You must use the AWS CLI, AWS SDK, or the Amazon S3 REST API\.

**Warning**  
When editing metadata of folders, wait for the `Edit metadata` operation to finish before adding new objects to the folder\. Otherwise, new objects might also be edited\.

The following topics describe how to edit metadata of an object using the Amazon S3 console\.

## Editing system\-defined metadata<a name="add-object-metadata-system"></a>

You can configure some, but not all, system metadata for an S3 object\. For a list of system\-defined metadata and whether you can modify their values, see [System\-defined object metadata](UsingMetadata.md#SysMetadata)\.

**To edit system\-defined metadata of an object**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. Navigate to your Amazon S3 bucket or folder, and select the check box to the left of the names of the objects with metadata you want to edit\.

1. On the **Actions** menu, choose **Edit actions**, and choose **Edit metadata**\.

1. Review the objects listed, and choose **Add metadata**\.

1. For metadata **Type**, select **System\-defined**\.

1. Specify a unique **Key** and the metadata **Value**\.

1. To edit additional metadata, choose **Add metadata**\. You can also choose **Remove** to remove a set of type\-key\-values\.

1. When you are done, choose **Edit metadata** and Amazon S3 edits the metadata of the specified objects\.

## Editing user\-defined metadata<a name="add-object-metadata-user-defined"></a>

You can edit user\-defined metadata of an object by combining the metadata prefix, `x-amz-meta-`, and a name you choose to create a custom key\. For example, if you add the custom name `alt-name`, the metadata key would be `x-amz-meta-alt-name`\. 

User\-defined metadata can be as large as 2 KB total\. To calculate the total size of user\-defined metadata, sum the number of bytes in the UTF\-8 encoding for each key and value\. Both keys and their values must conform to US\-ASCII standards\. For more information, see [User\-defined object metadata](UsingMetadata.md#UserMetadata)\.

**To edit user\-defined metadata of an object**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the name of the bucket that contains the objects that you want to add metadata to\.

   You can also optionally navigate to a folder\.

1. In the **Objects** list, select the check box next to the names of the objects that you want to add metadata to\.

1. On the **Actions** menu, choose **Edit metadata**\.

1. Review the objects listed, and choose **Add metadata**\.

1. For metadata **Type**, choose **User\-defined**\.

1. Enter a unique custom **Key** following `x-amz-meta-`\. Also enter a metadata **Value**\.

1. To add additional metadata, choose **Add metadata**\. You can also choose **Remove** to remove a set of type\-key\-values\. 

1. Choose **Edit metadata**\.

   Amazon S3 edits the metadata of the specified objects\.
