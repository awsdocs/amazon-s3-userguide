--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Viewing object properties in the Amazon S3 console<a name="view-object-properties"></a>

You can use the Amazon S3 console to view the properties of an object, including storage class, encryption settings, tags, and metadata\.

**To view the properties of an object**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the name of the bucket that contains the object\.

1. In the **Objects** list, choose the name of the object you want to view properties for\.

   The **Object overview** for your object opens\. You can scroll down to view the object properties\.

1. On the **Object overview** page, you can configure the following properties for the object\.
**Note**  
If you change the **Storage Class**, ** Encryption**, or **Metadata** properties, a new object is created to replace the old one\. If S3 Versioning is enabled, a new version of the object is created, and the existing object becomes an older version\. The role that changes the property also becomes the owner of the new object or \(object version\)\.

   1. **Storage class** – Each object in Amazon S3 has a storage class associated with it\. The storage class that you choose to use depends on how frequently you access the object\. The default storage class for S3 objects is STANDARD\. You choose which storage class to use when you upload an object\. For more information about storage classes, see [Using Amazon S3 storage classes](storage-class-intro.md)\.

      To change the storage class after you upload an object, choose **Storage class**\. Choose the storage class that you want, and then choose **Save**\.

   1. **Server\-side encryption settings** – You can use server\-side encryption to encrypt your S3 objects\. For more information, see [Specifying server\-side encryption with AWS KMS \(SSE\-KMS\)](specifying-kms-encryption.md) or [Specifying Amazon S3 encryption](specifying-s3-encryption.md)\. 

   1. **Metadata** – Each object in Amazon S3 has a set of name\-value pairs that represents its metadata\. For information about adding metadata to an S3 object, see [Editing object metadata in the Amazon S3 console](UsingMetadata.md#add-object-metadata)\.

   1. **Tags** – You categorize storage by adding tags to an S3 object\. For more information, see [Categorizing your storage using tags](object-tagging.md)\.

   1. **Object lock legal hold and rentention** – You can prevent an object from being deleted\. For more information, see [Using S3 Object Lock](object-lock.md)\.