# Categorizing your storage using tags<a name="object-tagging"></a>

Use object tagging to categorize storage\. Each tag is a key\-value pair\.

You can add tags to new objects when you upload them, or you can add them to existing objects\. 
+ You can associate up to 10 tags with an object\. Tags that are associated with an object must have unique tag keys\.
+ A tag key can be up to 128 Unicode characters in length, and tag values can be up to 256 Unicode characters in length\.
+ The key and values are case sensitive\.
+ For more information about tag restrictions, see [User\-Defined Tag Restrictions](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/allocation-tag-restrictions.html)\.

**Examples**  
Consider the following tagging examples:

**Example PHI information**  
Suppose that an object contains protected health information \(PHI\) data\. You might tag the object using the following key\-value pair\.  

```
PHI=True
```
or  

```
Classification=PHI
```

**Example Project files**  
Suppose that you store project files in your S3 bucket\. You might tag these objects with a key named `Project` and a value, as shown following\.  

```
Project=Blue
```

**Example Multiple tags**  
You can add multiple tags to an object, as shown following\.  

```
Project=x
Classification=confidential
```

**Key name prefixes and tags**  
Object key name prefixes also enable you to categorize storage\. However, prefix\-based categorization is one\-dimensional\. Consider the following object key names:

```
photos/photo1.jpg
project/projectx/document.pdf
project/projecty/document2.pdf
```

These key names have the prefixes `photos/`, `project/projectx/`, and `project/projecty/`\. These prefixes enable one\-dimensional categorization\. That is, everything under a prefix is one category\. For example, the prefix `project/projectx` identifies all documents related to project x\.

With tagging, you now have another dimension\. If you want photo1 in project x category, you can tag the object accordingly\.

**Additional benefits**  
In addition to data classification, tagging offers benefits such as the following:
+ Object tags enable fine\-grained access control of permissions\. For example, you could grant a user permissions to read\-only objects with specific tags\.
+ Object tags enable fine\-grained object lifecycle management in which you can specify a tag\-based filter, in addition to a key name prefix, in a lifecycle rule\.
+ When using Amazon S3 analytics, you can configure filters to group objects together for analysis by object tags, by key name prefix, or by both prefix and tags\.
+ You can also customize Amazon CloudWatch metrics to display information by specific tag filters\. The following sections provide details\.

**Important**  
It is acceptable to use tags to label objects containing confidential data, such as personally identifiable information \(PII\) or protected health information \(PHI\)\. However, the tags themselves shouldn't contain any confidential information\. 

**Adding object tag sets to multiple Amazon S3 object with a single request**  
To add object tag sets to more than one Amazon S3 object with a single request, you can use S3 Batch Operations\. You provide S3 Batch Operations with a list of objects to operate on\. S3 Batch Operations calls the respective API operation to perform the specified operation\. A single Batch Operations job can perform the specified operation on billions of objects containing exabytes of data\. 

The S3 Batch Operations feature tracks progress, sends notifications, and stores a detailed completion report of all actions, providing a fully managed, auditable, serverless experience\. You can use S3 Batch Operations through the Amazon S3 console, AWS CLI, AWS SDKs, or REST API\. For more information, see [S3 Batch Operations basics](batch-ops.md#batch-ops-basics)\.

For more information about object tags, see [Managing object tags](tagging-managing.md)\.

## API operations related to object tagging<a name="tagging-apis"></a>

Amazon S3 supports the following API operations that are specifically for object tagging:

**Object API operations**
+  [PUT Object tagging](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectPUTtagging.html) – Replaces tags on an object\. You specify tags in the request body\.  There are two distinct scenarios of object tag management using this API\.
  + Object has no tags – Using this API you can add a set of tags to an object \(the object has no prior tags\)\.
  + Object has a set of existing tags – To modify the existing tag set, you must first retrieve the existing tag set, modify it on the client side, and then use this API to replace the tag set\.
**Note**  
 If you send this request with an empty tag set, Amazon S3 deletes the existing tag set on the object\. If you use this method, you will be charged for a Tier 1 Request \(PUT\)\. For more information, see [Amazon S3 Pricing](https://d0.awsstatic.com/whitepapers/aws_pricing_overview.pdf)\.  
The [DELETE Object tagging](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectDELETEtagging.html) request is preferred because it achieves the same result without incurring charges\. 
+  [GET Object tagging](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectGETtagging.html) – Returns the tag set associated with an object\. Amazon S3 returns object tags in the response body\.
+ [DELETE Object tagging](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectDELETEtagging.html) – Deletes the tag set associated with an object\. 

**Other API operations that support tagging**
+  [PUT Object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectPUT.html) and [Initiate Multipart Upload](https://docs.aws.amazon.com/AmazonS3/latest/API/mpUploadInitiate.html)– You can specify tags when you create objects\. You specify tags using the `x-amz-tagging` request header\. 
+  [GET Object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectGET.html) – Instead of returning the tag set, Amazon S3 returns the object tag count in the `x-amz-tag-count` header \(only if the requester has permissions to read tags\) because the header response size is limited to 8 K bytes\. If you want to view the tags, you make another request for the [GET Object tagging](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectGETtagging.html) API operation\.
+ [POST Object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectPOST.html) – You can specify tags in your POST request\. 

  As long as the tags in your request don't exceed the 8 K byte HTTP request header size limit, you can use the `PUT Object `API to create objects with tags\. If the tags you specify exceed the header size limit, you can use this POST method in which you include the tags in the body\. 

   [PUT Object \- Copy](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectCOPY.html) – You can specify the `x-amz-tagging-directive` in your request to direct Amazon S3 to either copy \(default behavior\) the tags or replace tags by a new set of tags provided in the request\. 

Note the following:
+ S3 Object Tagging is strongly consistent\. For more information, see [Amazon S3 data consistency model](Welcome.md#ConsistencyModel)\. 

## Additional configurations<a name="tagging-other-configs"></a>

This section explains how object tagging relates to other configurations\.

### Object tagging and lifecycle management<a name="tagging-and-lifecycle"></a>

In bucket lifecycle configuration, you can specify a filter to select a subset of objects to which the rule applies\. You can specify a filter based on the key name prefixes, object tags, or both\. 

Suppose that you store photos \(raw and the finished format\) in your Amazon S3 bucket\. You might tag these objects as shown following\. 

```
phototype=raw
or
phototype=finished
```

You might consider archiving the raw photos to S3 Glacier sometime after they are created\. You can configure a lifecycle rule with a filter that identifies the subset of objects with the key name prefix \(`photos/`\) that have a specific tag \(`phototype=raw`\)\. 

For more information, see [Managing your storage lifecycle](object-lifecycle-mgmt.md)\. 

### Object tagging and replication<a name="tagging-and-replication"></a>

If you configured Replication on your bucket, Amazon S3 replicates tags, provided you grant Amazon S3 permission to read the tags\. For more information, see [Setting up replication](replication-how-setup.md)\.

### Object tagging event notifications<a name="tagging-and-event-notifications"></a>

You can set up an Amazon S3 event notification to receive notice when an object tag is added or deleted from an object\. The `s3:ObjectTagging:Put` event type notifies you when a tag is PUT on an object or when an existing tag is updated\. The `s3:ObjectTagging:Delete` event type notifies you when a tag is removed from an object\. For more information, see [ Enabling event notifications](https://docs.aws.amazon.com/AmazonS3/latest/userguide/how-to-enable-disable-notification-intro.html)\.

For more information about object tagging, see the following topics:

**Topics**
+ [API operations related to object tagging](#tagging-apis)
+ [Additional configurations](#tagging-other-configs)
+ [Tagging and access control policies](tagging-and-policies.md)
+ [Managing object tags](tagging-managing.md)