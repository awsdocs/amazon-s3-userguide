# Uploading, downloading, and working with objects in Amazon S3<a name="uploading-downloading-objects"></a>

To store your data in Amazon S3, you work with resources known as buckets and objects\. A *bucket* is a container for objects\. An *object* is a file and any metadata that describes that file\.

To store an object in Amazon S3, you create a bucket and then upload the object to a bucket\. When the object is in the bucket, you can open it, download it, and copy it\. When you no longer need an object or a bucket, you can clean up these resources\.

**Important**  
 In the Amazon S3 console, when you choose **Open** or **Download As** for an object, these operations create presigned URLs\. For the duration of five minutes, your object will be accessible to anyone who has access to these presigned URLs\. For more information about presigned URLs, see [Using presigned URLS](https://docs.aws.amazon.com/AmazonS3/latest/userguide/using-presigned-url.html)\.

With Amazon S3, you pay only for what you use\. For more information about Amazon S3 features and pricing, see [Amazon S3](http://aws.amazon.com/s3)\. If you are a new Amazon S3 customer, you can get started with Amazon S3 for free\. For more information, see [AWS Free Tier](http://aws.amazon.com/free)\.

**Topics**
+ [Amazon S3 objects overview](UsingObjects.md)
+ [Creating object key names](object-keys.md)
+ [Working with object metadata](UsingMetadata.md)
+ [Uploading objects](upload-objects.md)
+ [Uploading and copying objects using multipart upload](mpuoverview.md)
+ [Copying objects](copy-object.md)
+ [Downloading an object](download-objects.md)
+ [Checking object integrity](checking-object-integrity.md)
+ [Deleting Amazon S3 objects](DeletingObjects.md)
+ [Organizing, listing, and working with your objects](organizing-objects.md)
+ [Using presigned URLs](using-presigned-url.md)
+ [Transforming objects with S3 Object Lambda](transforming-objects.md)