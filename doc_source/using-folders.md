# Organizing objects in the Amazon S3 console using folders<a name="using-folders"></a>

In Amazon S3, buckets and objects are the primary resources, and objects are stored in buckets\. Amazon S3 has a flat structure instead of a hierarchy like you would see in a file system\. However, for the sake of organizational simplicity, the Amazon S3 console supports the *folder* concept as a means of grouping objects\. It does this by using a shared name prefix for objects \(that is, objects have names that begin with a common string\)\. Object names are also referred to as *key names*\.

For example, you can create a folder on the console named `photos` and store an object named `myphoto.jpg` in it\. The object is then stored with the key name `photos/myphoto.jpg`, where `photos/` is the prefix\.

Here are two more examples: 
+ If you have three objects in your bucket—`logs/date1.txt`, `logs/date2.txt`, and `logs/date3.txt`—the console will show a folder named `logs`\. If you open the folder in the console, you will see three objects: `date1.txt`, `date2.txt`, and `date3.txt`\.
+ If you have an object named `photos/2017/example.jpg`, the console will show you a folder named `photos` containing the folder `2017`\. The folder `2017` will contain the object `example.jpg`\.

You can have folders within folders, but not buckets within buckets\. You can upload and copy objects directly into a folder\. Folders can be created, deleted, and made public, but they cannot be renamed\. Objects can be copied from one folder to another\. 

**Important**  
When you create a folder in Amazon S3, S3 creates a 0\-byte object with a key that's set to the folder name that you provided\. For example, if you create a folder named `photos` in your bucket, the Amazon S3 console creates a 0\-byte object with the key `photos/`\. The console creates this object to support the idea of folders\.   
The Amazon S3 console treats all objects that have a forward slash \(`/`\) character as the last \(trailing\) character in the key name as a folder \(for example, `examplekeyname/`\)\. You can't upload an object that has a key name with a trailing `/` character by using the Amazon S3 console\. However, you can upload objects that are named with a trailing `/` with the Amazon S3 API by using the AWS CLI, AWS SDKs, or REST API\.   
An object that is named with a trailing `/` appears as a folder in the Amazon S3 console\. The Amazon S3 console does not display the content and metadata for such an object\. When you use the console to copy an object named with a trailing `/`, a new folder is created in the destination location, but the object's data and metadata are not copied\. 

**Topics**
+ [Creating a folder](#create-folder)
+ [Making folders public](#public-folders)
+ [Calculating folder size](#calculate-folder)
+ [Deleting folders](#delete-folders)

## Creating a folder<a name="create-folder"></a>

This section describes how to use the Amazon S3 console to create a folder\.

**Important**  
If your bucket policy prevents uploading objects to this bucket without tags, metadata, or access control list \(ACL\) grantees, you will not be able to create a folder using this configuration\. Instead, upload an empty folder and specify these settings in the upload configuration\.

**To create a folder**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the name of the bucket that you want to create a folder in\.

1. If your bucket policy prevents uploading objects to this bucket without encryption, you must choose **Enable** under **Server\-side encryption**\.

1. Choose **Create folder**\.

1. Enter a name for the folder \(for example, **favorite\-pics**\)\. Then choose **Create folder**\.

## Making folders public<a name="public-folders"></a>

We recommend blocking all public access to your Amazon S3 folders and buckets unless you specifically require a public folder or bucket\. When you make a folder public, anyone on the internet can view all the objects that are grouped in that folder\. 

In the Amazon S3 console, you can make a folder public\. You can also make a folder public by creating a bucket policy that limits access by prefix\. For more information, see [Identity and access management in Amazon S3](s3-access-control.md)\. 

**Warning**  
After you make a folder public in the Amazon S3 console, you can't make it private again\. Instead, you must set permissions on each individual object in the public folder so that the objects have no public access\. For more information, see [Configuring ACLs](managing-acls.md)\.

**Topics**
+ [Creating a folder](#create-folder)
+ [Making folders public](#public-folders)
+ [Calculating folder size](#calculate-folder)
+ [Deleting folders](#delete-folders)

## Calculating folder size<a name="calculate-folder"></a>

This section describes how to use the Amazon S3 console to calculate a folder's size\.

**To calculate a folder's size**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Buckets**\.

1. In the **Buckets** list, choose the name of the bucket in which your folder is stored\.

1. In the **Objects** list, select the check box next to the name of the folder\.

1. Choose **Actions**, and then choose **Calculate total size**\.

**Note**  
 The folder information will no longer be available after you navigate away from the page\.

**Important**  
When you use the **Calculate total size** action on specified objects or folders within your bucket, Amazon S3 calculates the total number of objects and the total storage size\. However, incomplete or in\-progress multipart uploads and previous or noncurrent versions aren't calculated in the total number of objects or the total size\. This action calculates only the total number of objects and the total size for the current or newest version of each object that is stored in the bucket\.  
For example, if there are two versions of an object in your bucket, then the storage calculator in Amazon S3 counts them as only one object\. As a result, the total number of objects that is calculated in the Amazon S3 console can differ from the **Object Count** metric shown in S3 Storage Lens and from the number reported by the Amazon CloudWatch metric, `NumberOfObjects`\. Likewise, the total storage size can also differ from the **Total Storage** metric shown in S3 Storage Lens and from the `BucketSizeBytes` metric shown in CloudWatch\.

## Deleting folders<a name="delete-folders"></a>

This section explains how to use the Amazon S3 console to delete folders from an S3 bucket\. 

For information about Amazon S3 features and pricing, see [Amazon S3](https://aws.amazon.com/s3/)\.



**To delete folders from an S3 bucket**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the name of the bucket that you want to delete folders from\.

1. In the **Objects** list, select the check box next to the folders and objects that you want to delete\.

1. Choose **Delete**\.

1. On the **Delete objects** page, verify that the names of the folders you selected for deletion are listed\.

1. In the **Delete objects** box, enter **delete**, and choose **Delete objects**\.

**Warning**  
This action deletes all specified objects\. When deleting folders, wait for the delete action to finish before adding new objects to the folder\. Otherwise, new objects might be deleted as well\.