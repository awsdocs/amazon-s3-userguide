# Working with S3 on Outposts objects<a name="S3OutpostsWorkingObjects"></a>

With Amazon S3 on Outposts, you can create S3 buckets on your AWS Outposts and easily store and retrieve objects on premises for applications that require local data access, local data processing, and data residency\. S3 on Outposts provides a new storage class, S3 Outposts \(`OUTPOSTS`\), which uses the S3 APIs, and is designed to store data durably and redundantly across multiple devices and servers on your AWS Outposts\. You communicate with your Outpost bucket by using an access point and endpoint connection over a virtual private cloud \(VPC\)\. You can use the same APIs and features on Outpost buckets as you do on Amazon S3 buckets, including access policies, encryption, and tagging\. You can use S3 on Outposts through the AWS Management Console, AWS CLI, AWS SDKs, or REST API\. 

Objects are the fundamental entities stored in S3 on Outposts\. Every object is contained in a bucket\. You must use access points to access any object in an Outpost bucket\. When you specify the bucket for object operations, you use the access point Amazon Resource Name \(ARN\), which includes the AWS Region code for the Region the Outpost is homed to, AWS account ID, Outpost ID, and access point name\. The following example shows the ARN format for S3 on Outposts access points in object operations:

```
arn:aws:s3-outposts:region:account-id:outpost/outpost-id/accesspoint/accesspoint-name
```

Object ARNs use the following format, which includes the AWS Region, AWS account ID, Outpost ID, bucket name, and object key:

```
arn:aws:s3-outposts:us-west-2:123456789012:​outpost/op-01ac5d28a6a232904/bucket/DOC-EXAMPLE-BUCKET1/object/myobject
```

With S3 on Outposts, object data is always stored on the Outpost\. When AWS installs an Outpost rack, your data stays local to your Outpost to meet data residency requirements\. Your objects never leave your Outpost and are not in an AWS Region\. Because the AWS Management Console is hosted in\-Region, you can't use the console to upload or manage objects in your Outpost\. However, you can use the REST API, AWS CLI, and AWS SDKs to upload and manage your objects through your access points\.

To upload an object, see [Step four: Upload an object to an S3 on Outposts bucket](S3OutpostsGSCLIJava.md#S3OutpostsUploadObjects)\. For other object actions, see the following topics\.

**Topics**
+ [Getting an object from an Amazon S3 on Outposts bucket](S3OutpostsGetObject.md)
+ [Listing the objects in an Amazon S3 on Outposts bucket](S3OutpostsListObjects.md)
+ [Deleting objects in Amazon S3 on Outposts buckets](S3OutpostsDeleteObject.md)
+ [Using HeadBucket to determine if an S3 on Outposts bucket exists and you have access permissions](S3OutpostsHeadBucket.md)
+ [Performing and managing a multipart upload with the AWS SDK for Java](S3OutpostsJavaExamples.md)