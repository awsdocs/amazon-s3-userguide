--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Uploading objects<a name="upload-objects"></a>

When you upload a file to Amazon S3, it is stored as an S3 object\. Objects consist of the file data and metadata that describes the object\. You can have an unlimited number of objects in a bucket\. Before you can upload files to an Amazon S3 bucket, you need write permissions for the bucket\. For more information about access permissions, see [Identity and access management in Amazon S3](s3-access-control.md)\. 

You can upload any file type—images, backups, data, movies, etc\.—into an S3 bucket\. The maximum size of a file that you can upload by using the Amazon S3 console is 160 GB\. To upload a file larger than 160 GB, use the AWS CLI, AWS SDK, or Amazon S3 REST API\.

If you upload an object with a key name that already exists in a versioning\-enabled bucket, Amazon S3 creates another version of the object instead of replacing the existing object\. For more information about versioning, see [Using the S3 console](manage-versioning-examples.md#enable-versioning)\.

 Depending on the size of the data you are uploading, Amazon S3 offers the following options: 
+ **Upload an object in a single operation using the AWS SDKs, REST API, or AWS CLI—**With a single PUT operation, you can upload a single object up to 5 GB in size\.
+ **Upload a single object using the Amazon S3 Console—**With the Amazon S3 Console, you can upload a single object up to 160 GB in size\. 
+ **Upload an object in parts using the AWS SDKs, REST API, or AWS CLI—**Using the multipart upload API, you can upload a single large object, up to 5 TB in size\.

  The multipart upload API is designed to improve the upload experience for larger objects\. You can upload an object in parts\. These object parts can be uploaded independently, in any order, and in parallel\. You can use a multipart upload for objects from 5 MB to 5 TB in size\. For more information, see [Uploading and copying objects using multipart upload](mpuoverview.md)\.

When uploading an object, you can optionally request that Amazon S3 encrypt it before saving it to disk, and decrypt it when you download it\. For more information, see [Protecting data using encryption](UsingEncryption.md)\. 

## Using the S3 console to upload a single object<a name="upload-objects-by-file-selection"></a>

This procedure explains how to upload a single object to an S3 bucket using the console\. 

When you upload an object, the object key name is the file name and any optional prefixes\. In the Amazon S3 console, you can create folders to organize your objects\. In Amazon S3, folders are represented as prefixes that appear in the object key name\. If you upload an individual object to a folder in the Amazon S3 console, the folder name is included in the object key name\. 

For example, if you upload an object named `sample1.jpg` to a folder named `backup`, the key name is `backup/sample1.jpg`\. However, the object is displayed in the console as `sample1.jpg` in the `backup` folder\. For more information about key names, see [Working with object metadata](UsingMetadata.md)\.

**Note**  
If you rename an object or change any of the properties in the S3 console, for example **Storage Class**, ** Encryption**, **Metadata**, a new object is created to replace the old one\. If S3 Versioning is enabled, a new version of the object is created, and the existing object becomes an older version\. The role that changes the property also becomes the owner of the new object or \(object version\)\.

**Topics**

**To upload a file to an S3 bucket**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the name of the bucket that you want to upload your files to\.

1. Choose **Upload**\.

1. On the **Upload** page, choose **Add files** or **Add folder**\.

1. Choose one or more files to upload, and then choose **Open**\.

1. After you see the files that you chose listed in the **Upload** dialog box, continue with **Step 5** of [Using the S3 console to upload folders or multiple objects](#upload-objects-by-drag-and-drop)\.

## Using the S3 console to upload folders or multiple objects<a name="upload-objects-by-drag-and-drop"></a>

If you are using the Chrome or Firefox browsers, you can choose folders and files to upload and then drag and drop them into the destination bucket\. Dragging and dropping is the *only* way that you can upload folders\. For information about which Chrome and Firefox browser versions are supported, see [ What browsers are supported for use with the AWS Management Console?](https://aws.amazon.com/premiumsupport/knowledge-center/browsers-management-console/) 

When you upload a folder, Amazon S3 uploads all of the files and subfolders from the specified folder to your bucket\. It then assigns an object key name that is a combination of the uploaded file name and the folder name\. For example, if you upload a folder named `/images` that contains two files, `sample1.jpg` and `sample2.jpg`, Amazon S3 uploads the files and then assigns the corresponding key names, `images/sample1.jpg` and `images/sample2.jpg`\. The key names include the folder name as a prefix\. The Amazon S3 console displays only the part of the key name that follows the last “/”\. For example, within an images folder the `images/sample1.jpg` and `images/sample2.jpg` objects are displayed as `sample1.jpg` and a `sample2.jpg`\.

**To upload folders and files to an S3 bucket using a drag\-and\-drop process**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Bucket name** list, choose the name of the bucket that you want to upload your folders or files to\.

1. In a window other than the console window, select the files and folders that you want to upload\. Then drag and drop your selections into the console window that lists the objects in the destination bucket\. 

   The files you chose are listed in the **Upload** dialog box\.

1. In the **Upload** dialog box, do one of the following: 

   1. Drag and drop more files and folders to the console window that displays the **Upload** dialog box\. To add more files, you can also choose **Add more files**\. This option works *only* for files, not folders\.

   1. To immediately upload the listed files and folders without granting or removing permissions for specific users or setting public permissions for all of the files that you're uploading, choose **Upload**\. For information about object access permissions, see [Configuring ACLs](managing-acls.md)\. 

   1. To set permissions or properties for the files that you are uploading, choose **Next**\.

1. On the **Set Permissions** page, under **Manage users** you can change the permissions for the AWS account owner\. The *owner* refers to the AWS account root user, and not an AWS Identity and Access Management \(IAM\) user\. For more information about the root user, see [AWS account root user](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_root-user.html) in the *IAM User Guide*\. 

   Choose **Add account** to grant access to another AWS account\. For more information about granting permissions to another AWS account, see [Configuring ACLs](managing-acls.md)\.

   Under **Manage public permissions**, you can grant read access to your objects to the general public \(everyone in the world\), for all of the files that you're uploading\. Granting public read access is applicable to a small subset of use cases, such as when buckets are used for websites\. We recommend that you do not change the default setting of **Do not grant public read access to this object\(s\)**\. You can always make changes to object permissions after you upload the object\. For information about object access permissions, see [Configuring ACLs](managing-acls.md)\. 

   When you're done configuring permissions, choose **Next**\. 

1. <a name="UsingServerSideEncryption.title"></a>On the **Set Properties** page, choose the storage class and encryption method to use for the files that you are uploading\. You can also add or modify metadata\. 

   1. Choose a storage class for the files you're uploading\. For more information about storage classes, see [Using Amazon S3 storage classes](storage-class-intro.md)\.

   1. Choose the type of encryption for the files that you're uploading\. If you don't want to encrypt them, choose **None**\. 

      1. To encrypt the uploaded files using keys that are managed by Amazon S3, choose **Amazon S3 master\-key**\. For more information, see [Protecting data using server\-side encryption with Amazon S3\-managed encryption keys \(SSE\-S3\)](UsingServerSideEncryption.md)\.

      1. To encrypt the uploaded files using the AWS Key Management Service \(AWS KMS\), choose **AWS KMS master\-key**\. Then choose a customer master key \(CMK\) from the list of AWS KMS CMKs\.
**Note**  
To encrypt objects in a bucket, you can use only CMKs that are available in the same AWS Region as the bucket\. 

         You can give an external account the ability to use an object that is protected by an AWS KMS CMK\. To do this, select **Custom KMS ARN** from the list and enter the Amazon Resource Name \(ARN\) for the external account\. Administrators of an external account that have usage permissions to an object protected by your AWS KMS CMK can further restrict access by creating a resource\-level IAM policy\. 

         For more information about creating an AWS KMS CMK, see [Creating keys](https://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html) in the *AWS Key Management Service Developer Guide*\. For more information about protecting data with AWS KMS, see [Protecting Data Using Server\-Side Encryption with CMKs Stored in AWS Key Management Service \(SSE\-KMS\)](UsingKMSEncryption.md)\.

   1. Metadata for Amazon S3 objects is represented by a name\-value \(key\-value\) pair\. There are two kinds of metadata: system\-defined metadata and user\-defined metadata\.

      If you want to add Amazon S3 system\-defined metadata to all of the objects you are uploading, for **Header**, select a header\. You can select common HTTP headers, such as **Content\-Type** and **Content\-Disposition**\. Type a value for the header, and then choose **Save**\. For a list of system\-defined metadata and information about whether you can add the value, see [System\-defined object metadata](UsingMetadata.md#SysMetadata)\.

   1. Any metadata starting with prefix `x-amz-meta-` is treated as user\-defined metadata\. User\-defined metadata is stored with the object, and is returned when you download the object\. 

      To add user\-defined metadata to all of the objects that you are uploading, type `x-amz-meta-` plus a custom metadata name in the **Header** field\. Type a value for the header, and then choose **Save**\. Both the keys and their values must conform to US\-ASCII standards\. User\-defined metadata can be as large as 2 KB\. For more information about user\-defined metadata, see [User\-defined object metadata](UsingMetadata.md#UserMetadata)\.

   1. Object tagging gives you a way to categorize storage\. Each tag is a key\-value pair\. Key and tag values are case sensitive\. You can have up to 10 tags per object\. 

      To add tags to all of the objects that you are uploading, type a tag name in the **Key** field\. Type a value for the tag, and then choose **Save**\. A tag key can be up to 128 Unicode characters in length and tag values can be up to 255 Unicode characters in length\. For more information about object tags, see [ Categorizing your storage using tagsUsing object tags  Use object tagging to categorize storage\. Each tag is a key\-value pair\. You can add tags to new objects when you upload them, or you can add them to existing objects\.    You can associate up to 10 tags with an object\. Tags that are associated with an object must have unique tag keys\.   A tag key can be up to 128 Unicode characters in length, and tag values can be up to 256 Unicode characters in length\.   The key and values are case sensitive\.   For more information about tag restrictions, see [User\-Defined Tag Restrictions](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/allocation-tag-restrictions.html)\.    Examples Consider the following tagging examples:  

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
```  Key name prefixes and tags Object key name prefixes also enable you to categorize storage\. However, prefix\-based categorization is one\-dimensional\. Consider the following object key names:  

```
photos/photo1.jpg
project/projectx/document.pdf
project/projecty/document2.pdf
``` These key names have the prefixes `photos/`, `project/projectx/`, and `project/projecty/`\. These prefixes enable one\-dimensional categorization\. That is, everything under a prefix is one category\. For example, the prefix `project/projectx` identifies all documents related to project x\. With tagging, you now have another dimension\. If you want photo1 in project x category, you can tag the object accordingly\.  Additional benefits In addition to data classification, tagging offers benefits such as the following:    Object tags enable fine\-grained access control of permissions\. For example, you could grant an IAM user permissions to read\-only objects with specific tags\.   Object tags enable fine\-grained object lifecycle management in which you can specify a tag\-based filter, in addition to a key name prefix, in a lifecycle rule\.   When using Amazon S3 analytics, you can configure filters to group objects together for analysis by object tags, by key name prefix, or by both prefix and tags\.   You can also customize Amazon CloudWatch metrics to display information by specific tag filters\. The following sections provide details\.    It is acceptable to use tags to label objects containing confidential data, such as personally identifiable information \(PII\) or protected health information \(PHI\)\. However, the tags themselves shouldn't contain any confidential information\.     Adding object tag sets to multiple Amazon S3 object with a single request To add object tag sets to more than one Amazon S3 object with a single request, you can use S3 Batch Operations\. You provide S3 Batch Operations with a list of objects to operate on\. S3 Batch Operations calls the respective API to perform the specified operation\. A single Batch Operations job can perform the specified operation on billions of objects containing exabytes of data\.   The S3 Batch Operations feature tracks progress, sends notifications, and stores a detailed completion report of all actions, providing a fully managed, auditable, serverless experience\. You can use S3 Batch Operations through the AWS Management Console, AWS CLI, AWS SDKs, or REST API\. For more information, see [S3 Batch Operations basics](batch-ops-basics.md)\. For more information about object tabs, see [Managing object tags](tagging-managing.md)\. API operations related to object tagging Amazon S3 supports the following API operations that are specifically for object tagging: **Object API operations**    [PUT Object tagging](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectPUTtagging.html) – Replaces tags on an object\. You specify tags in the request body\.  There are two distinct scenarios of object tag management using this API\.   Object has no tags – Using this API you can add a set of tags to an object \(the object has no prior tags\)\.   Object has a set of existing tags – To modify the existing tag set, you must first retrieve the existing tag set, modify it on the client side, and then use this API to replace the tag set\.  If you send this request with an empty tag set, Amazon S3 deletes the existing tag set on the object\. If you use this method, you will be charged for a Tier 1 Request \(PUT\)\. For more information, see [Amazon S3 Pricing](https://d0.awsstatic.com/whitepapers/aws_pricing_overview.pdf)\. The [DELETE Object tagging](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectDELETEtagging.html) request is preferred because it achieves the same result without incurring charges\.       [GET Object tagging](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectGETtagging.html) – Returns the tag set associated with an object\. Amazon S3 returns object tags in the response body\.   [DELETE Object tagging](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectDELETEtagging.html) – Deletes the tag set associated with an object\.     **Other API operations that support tagging**    [PUT Object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectPUT.html) and [Initiate Multipart Upload](https://docs.aws.amazon.com/AmazonS3/latest/API/mpUploadInitiate.html)– You can specify tags when you create objects\. You specify tags using the `x-amz-tagging` request header\.     [GET Object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectGET.html) – Instead of returning the tag set, Amazon S3 returns the object tag count in the `x-amz-tag-count` header \(only if the requester has permissions to read tags\) because the header response size is limited to 8 K bytes\. If you want to view the tags, you make another request for the [GET Object tagging](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectGETtagging.html) API operation\.   [POST Object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectPOST.html) – You can specify tags in your POST request\.  As long as the tags in your request don't exceed the 8 K byte HTTP request header size limit, you can use the `PUT Object `API to create objects with tags\. If the tags you specify exceed the header size limit, you can use this POST method in which you include the tags in the body\.   [PUT Object \- Copy](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectCOPY.html) – You can specify the `x-amz-tagging-directive` in your request to direct Amazon S3 to either copy \(default behavior\) the tags or replace tags by a new set of tags provided in the request\.    Note the following:   S3 Object Tagging is strongly consistent\. For more information, see [Amazon S3 data consistency model](Welcome.md#ConsistencyModel)\.       Additional configurations  Explains how object tagging relates to other configurations\.   This section explains how object tagging relates to other configurations\.   Object tagging and lifecycle management  In bucket lifecycle configuration, you can specify a filter to select a subset of objects to which the rule applies\. You can specify a filter based on the key name prefixes, object tags, or both\.  Suppose that you store photos \(raw and the finished format\) in your Amazon S3 bucket\. You might tag these objects as shown following\.  

```
phototype=raw
or
phototype=finished
``` You might consider archiving the raw photos to S3 Glacier sometime after they are created\. You can configure a lifecycle rule with a filter that identifies the subset of objects with the key name prefix \(`photos/`\) that have a specific tag \(`phototype=raw`\)\.  For more information, see [Managing your storage lifecycle](object-lifecycle-mgmt.md)\.    Object tagging and replication  If you configured Replication on your bucket, Amazon S3 replicates tags, provided you grant Amazon S3 permission to read the tags\. For more information, see [Setting up replication](replication-how-setup.md)\.    Access control policies  Explains how to use permissions policies to manage object tagging related permissions\.   You can also use permissions policies \(bucket and user policies\) to manage permissions related to object tagging\. For policy actions see the following topics:     [Example — Object operations](using-with-s3-actions.md#using-with-s3-actions-related-to-objects)     [Example — Bucket operations](using-with-s3-actions.md#using-with-s3-actions-related-to-buckets)   Object tags enable fine\-grained access control for managing permissions\. You can grant conditional permissions based on object tags\. Amazon S3 supports the following condition keys that you can use to grant conditional permissions based on object tags:   `s3:ExistingObjectTag/<tag-key>` – Use this condition key to verify that an existing object tag has the specific tag key and value\.   When granting permissions for the `PUT Object` and `DELETE Object` operations, this condition key is not supported\. That is, you cannot create a policy to grant or deny a user permissions to delete or overwrite an object based on its existing tags\.     `s3:RequestObjectTagKeys` – Use this condition key to restrict the tag keys that you want to allow on objects\. This is useful when adding tags to objects using the PutObjectTagging and PutObject, and POST object requests\.   `s3:RequestObjectTag/<tag-key>` – Use this condition key to restrict the tag keys and values that you want to allow on objects\. This is useful when adding tags to objects using the PutObjectTagging and PutObject, and POST Bucket requests\.   For a complete list of Amazon S3 service\-specific condition keys, see [Amazon S3 condition keys](amazon-s3-policy-keys.md)\. The following permissions policies illustrate how object tagging enables fine grained access permissions management\. 

**Example 1: Allow a user to read only the objects that have a specific tag**  
The following permissions policy grants a user permission to read objects, but the condition limits the read permission to only objects that have the following specific tag key and value\.  

```
security : public
```
Note that the policy uses the Amazon S3 condition key, `s3:ExistingObjectTag/<tag-key>` to specify the key and value\.  

```
 1. {
 2.   "Version": "2012-10-17",
 3.   "Statement": [
 4.     {
 5.       "Effect":     "Allow",
 6.       "Action":     "s3:GetObject",
 7.       "Resource":    "arn:aws:s3:::awsexamplebucket1/*",
 8.       "Principal":   "*",
 9.       "Condition": {  "StringEquals": {"s3:ExistingObjectTag/security": "public" } }
10.     }
11.   ]
12. }
``` 

**Example 2: Allow a user to add object tags with restrictions on the allowed tag keys**  
The following permissions policy grants a user permissions to perform the `s3:PutObjectTagging` action, which allows user to add tags to an existing object\. The condition limits the tag keys that the user is allowed to use\. The condition uses the `s3:RequestObjectTagKeys` condition key to specify the set of tag keys\.  

```
 1. {
 2.   "Version": "2012-10-17",
 3.   "Statement": [
 4.     {
 5.       "Effect": "Allow",
 6.       "Action": [
 7.         "s3:PutObjectTagging"
 8.       ],
 9.       "Resource": [
10.         "arn:aws:s3:::awsexamplebucket1/*"
11.       ],
12.       "Principal":{
13.         "CanonicalUser":[
14.             "64-digit-alphanumeric-value"
15.          ]
16.        },
17.       "Condition": {
18.         "ForAllValues:StringLike": {
19.           "s3:RequestObjectTagKeys": [
20.             "Owner",
21.             "CreationDate"
22.           ]
23.         }
24.       }
25.     }
26.   ]
27. }
```
The policy ensures that the tag set, if specified in the request, has the specified keys\. A user might send an empty tag set in `PutObjectTagging`, which is allowed by this policy \(an empty tag set in the request removes any existing tags on the object\)\. If you want to prevent a user from removing the tag set, you can add another condition to ensure that the user provides at least one value\. The `ForAnyValue` in the condition ensures at least one of the specified values must be present in the request\.  

```
 1. {
 2. 
 3.   "Version": "2012-10-17",
 4.   "Statement": [
 5.     {
 6.       "Effect": "Allow",
 7.       "Action": [
 8.         "s3:PutObjectTagging"
 9.       ],
10.       "Resource": [
11.         "arn:aws:s3:::awsexamplebucket1/*"
12.       ],
13.       "Principal":{
14.         "AWS":[
15.             "arn:aws:iam::account-number-without-hyphens:user/username"
16.          ]
17.        },
18.       "Condition": {
19.         "ForAllValues:StringLike": {
20.           "s3:RequestObjectTagKeys": [
21.             "Owner",
22.             "CreationDate"
23.           ]
24.         },
25.         "ForAnyValue:StringLike": {
26.           "s3:RequestObjectTagKeys": [
27.             "Owner",
28.             "CreationDate"
29.           ]
30.         }
31.       }
32.     }
33.   ]
34. }
```
For more information, see [Creating a Condition That Tests Multiple Key Values \(Set Operations\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_multi-value-conditions.html) in the *IAM User Guide*\. 

**Example 3: Allow a user to add object tags that include a specific tag key and value**  
The following user policy grants a user permissions to perform the `s3:PutObjectTagging` action, which allows user to add tags on an existing object\. The condition requires the user to include a specific tag \(`Project`\) with value set to `X`\.   

```
 1. {
 2.   "Version": "2012-10-17",
 3.   "Statement": [
 4.     {
 5.       "Effect": "Allow",
 6.       "Action": [
 7.         "s3:PutObjectTagging"
 8.       ],
 9.       "Resource": [
10.         "arn:aws:s3:::awsexamplebucket1/*"
11.       ],
12.       "Principal":{
13.         "AWS":[
14.             "arn:aws:iam::account-number-without-hyphens:user/username"
15.          ]
16.        },
17.       "Condition": {
18.         "StringEquals": {
19.           "s3:RequestObjectTag/Project": "X"
20.         }
21.       }
22.     }
23.   ]
24. }
```
    Managing object tags  For information about managing object tags, see [Managing object tags](tagging-managing.md)\.  Managing object tags This section explains how you can manage object tags using the AWS SDKs for Java and \.NET or the Amazon S3 console\. Object tagging gives you a way to categorize storage\. Each tag is a key\-value pair that adheres to the following rules:   You can associate up to 10 tags with an object\. Tags associated with an object must have unique tag keys\.   A tag key can be up to 128 Unicode characters in length and tag values can be up to 256 Unicode characters in length\.    Key and tag values are case sensitive\.    For more information about object tags, see [Categorizing your storage using tags](object-tagging.md)\. For more information about tag restrictions, see [User\-Defined Tag Restrictions](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/allocation-tag-restrictions.html) in the *AWS Billing and Cost Management User Guide*\.   Using the S3 consoleEditing object tags   How to add or edit tags on an Amazon S3 object or object\.   To add tags to an object  Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.   In the **Buckets** list, choose the name of the bucket that contains the objects that you want to add tags to\. You can also optionally navigate to a folder\.   In the **Objects** list, select the checkbox next to the names of the objects that you want to add tags to\.   In the **Actions** menu, choose **Edit tags**\.   Review the objects listed, and choose **Add tags**\.    Each object tag is a key\-value pair\. Enter a **Key** and a **Value**\. To add another tag, choose **Add Tag**\.  You can enter up to 10 tags for an object\.   Choose **Save changes**\. Amazon S3 adds the tags to the specified objects\.   For more information, see also [Viewing object properties in the Amazon S3 console](view-object-properties.md) and [Uploading objects](upload-objects.md) in this guide\.    Using the AWS SDKs    Java  The following example shows how to use the AWS SDK for Java to set tags for a new object and retrieve or replace tags for an existing object\. For more information about object tagging, see [Categorizing your storage using tags](object-tagging.md)\. For instructions on creating and testing a working sample, see [Testing the Amazon S3 Java Code Examples](UsingTheMPJavaAPI.md#TestingJavaSamples)\.  

```
import com.amazonaws.AmazonServiceException;
import com.amazonaws.SdkClientException;
import com.amazonaws.auth.profile.ProfileCredentialsProvider;
import com.amazonaws.regions.Regions;
import com.amazonaws.services.s3.AmazonS3;
import com.amazonaws.services.s3.AmazonS3ClientBuilder;
import com.amazonaws.services.s3.model.*;

import java.io.File;
import java.util.ArrayList;
import java.util.List;

public class ManagingObjectTags {

    public static void main(String[] args) {
        Regions clientRegion = Regions.DEFAULT_REGION;
        String bucketName = "*** Bucket name ***";
        String keyName = "*** Object key ***";
        String filePath = "*** File path ***";

        try {
            AmazonS3 s3Client = AmazonS3ClientBuilder.standard()
                    .withCredentials(new ProfileCredentialsProvider())
                    .withRegion(clientRegion)
                    .build();

            // Create an object, add two new tags, and upload the object to Amazon S3.
            PutObjectRequest putRequest = new PutObjectRequest(bucketName, keyName, new File(filePath));
            List<Tag> tags = new ArrayList<Tag>();
            tags.add(new Tag("Tag 1", "This is tag 1"));
            tags.add(new Tag("Tag 2", "This is tag 2"));
            putRequest.setTagging(new ObjectTagging(tags));
            PutObjectResult putResult = s3Client.putObject(putRequest);

            // Retrieve the object's tags.
            GetObjectTaggingRequest getTaggingRequest = new GetObjectTaggingRequest(bucketName, keyName);
            GetObjectTaggingResult getTagsResult = s3Client.getObjectTagging(getTaggingRequest);

            // Replace the object's tags with two new tags.
            List<Tag> newTags = new ArrayList<Tag>();
            newTags.add(new Tag("Tag 3", "This is tag 3"));
            newTags.add(new Tag("Tag 4", "This is tag 4"));
            s3Client.setObjectTagging(new SetObjectTaggingRequest(bucketName, keyName, new ObjectTagging(newTags)));
        } catch (AmazonServiceException e) {
            // The call was transmitted successfully, but Amazon S3 couldn't process 
            // it, so it returned an error response.
            e.printStackTrace();
        } catch (SdkClientException e) {
            // Amazon S3 couldn't be contacted for a response, or the client
            // couldn't parse the response from Amazon S3.
            e.printStackTrace();
        }
    }
}
```    \.NET  The following example shows how to use the AWS SDK for \.NET to set the tags for a new object and retrieve or replace the tags for an existing object\. For more information about object tagging, see [Categorizing your storage using tags](object-tagging.md)\.  For instructions on how to create and test a working sample, see [Running the Amazon S3 \.NET Code Examples](UsingTheMPDotNetAPI.md#TestingDotNetApiSamples)\. 

```
using Amazon;
using Amazon.S3;
using Amazon.S3.Model;
using System;
using System.Collections.Generic;
using System.Threading.Tasks;

namespace Amazon.DocSamples.S3
{
    public class ObjectTagsTest
    {
        private const string bucketName = "*** bucket name ***";
        private const string keyName = "*** key name for the new object ***";
        private const string filePath = @"*** file path ***";
        // Specify your bucket region (an example region is shown).
        private static readonly RegionEndpoint bucketRegion = RegionEndpoint.USWest2;
        private static IAmazonS3 client;

        public static void Main()
        {
            client = new AmazonS3Client(bucketRegion);
            PutObjectWithTagsTestAsync().Wait();
        }

        static async Task PutObjectWithTagsTestAsync()
        {
            try
            {
                // 1. Put an object with tags.
                var putRequest = new PutObjectRequest
                {
                    BucketName = bucketName,
                    Key = keyName,
                    FilePath = filePath,
                    TagSet = new List<Tag>{
                        new Tag { Key = "Keyx1", Value = "Value1"},
                        new Tag { Key = "Keyx2", Value = "Value2" }
                    }
                };

                PutObjectResponse response = await client.PutObjectAsync(putRequest);
                // 2. Retrieve the object's tags.
                GetObjectTaggingRequest getTagsRequest = new GetObjectTaggingRequest
                {
                    BucketName = bucketName,
                    Key = keyName
                };

                GetObjectTaggingResponse objectTags = await client.GetObjectTaggingAsync(getTagsRequest);
                for (int i = 0; i < objectTags.Tagging.Count; i++)
                    Console.WriteLine("Key: {0}, Value: {1}", objectTags.Tagging[i].Key, objectTags.Tagging[i].Value);


                // 3. Replace the tagset.

                Tagging newTagSet = new Tagging();
                newTagSet.TagSet = new List<Tag>{
                    new Tag { Key = "Key3", Value = "Value3"},
                    new Tag { Key = "Key4", Value = "Value4" }
                };


                PutObjectTaggingRequest putObjTagsRequest = new PutObjectTaggingRequest()
                {
                    BucketName = bucketName,
                    Key = keyName,
                    Tagging = newTagSet
                };
                PutObjectTaggingResponse response2 = await client.PutObjectTaggingAsync(putObjTagsRequest);

                // 4. Retrieve the object's tags.
                GetObjectTaggingRequest getTagsRequest2 = new GetObjectTaggingRequest();
                getTagsRequest2.BucketName = bucketName;
                getTagsRequest2.Key = keyName;
                GetObjectTaggingResponse objectTags2 = await client.GetObjectTaggingAsync(getTagsRequest2);
                for (int i = 0; i < objectTags2.Tagging.Count; i++)
                    Console.WriteLine("Key: {0}, Value: {1}", objectTags2.Tagging[i].Key, objectTags2.Tagging[i].Value);

            }
            catch (AmazonS3Exception e)
            {
                Console.WriteLine(
                        "Error encountered ***. Message:'{0}' when writing an object"
                        , e.Message);
            }
            catch (Exception e)
            {
                Console.WriteLine(
                    "Encountered an error. Message:'{0}' when writing an object"
                    , e.Message);
            }
        }
    }
}
```      ](object-tagging.md)\.

1. Choose **Next**\.

1. On the **Upload** review page, verify that your settings are correct, and then choose **Upload**\. To make changes, choose **Previous**\.

1. To see the progress of the upload, choose **In progress** at the bottom of the browser window\.

   To see a history of your uploads and other operations, choose **Success**\.

## Using the AWS SDKs<a name="UploadInSingleOp"></a>

You can use the AWS SDK to upload objects in Amazon S3\. The SDK provides wrapper libraries for you to upload data easily\. However, if your application requires it, you can use the REST API directly in your application\. You can also use the AWS Command Line Interface \(AWS CLI\)\.

------
#### [ Java ]

The following example creates two objects\. The first object has a text string as data, and the second object is a file\. The example creates the first object by specifying the bucket name, object key, and text data directly in a call to `AmazonS3Client.putObject()`\. The example creates the second object by using a `PutObjectRequest` that specifies the bucket name, object key, and file path\. The `PutObjectRequest` also specifies the `ContentType` header and title metadata\.

 For instructions on creating and testing a working sample, see [Testing the Amazon S3 Java Code Examples](UsingTheMPJavaAPI.md#TestingJavaSamples)\. 

```
import com.amazonaws.AmazonServiceException;
import com.amazonaws.SdkClientException;
import com.amazonaws.regions.Regions;
import com.amazonaws.services.s3.AmazonS3;
import com.amazonaws.services.s3.AmazonS3ClientBuilder;
import com.amazonaws.services.s3.model.ObjectMetadata;
import com.amazonaws.services.s3.model.PutObjectRequest;

import java.io.File;
import java.io.IOException;

public class UploadObject {

    public static void main(String[] args) throws IOException {
        Regions clientRegion = Regions.DEFAULT_REGION;
        String bucketName = "*** Bucket name ***";
        String stringObjKeyName = "*** String object key name ***";
        String fileObjKeyName = "*** File object key name ***";
        String fileName = "*** Path to file to upload ***";

        try {
            //This code expects that you have AWS credentials set up per:
            // https://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/setup-credentials.html
            AmazonS3 s3Client = AmazonS3ClientBuilder.standard()
                    .withRegion(clientRegion)
                    .build();

            // Upload a text string as a new object.
            s3Client.putObject(bucketName, stringObjKeyName, "Uploaded String Object");

            // Upload a file as a new object with ContentType and title specified.
            PutObjectRequest request = new PutObjectRequest(bucketName, fileObjKeyName, new File(fileName));
            ObjectMetadata metadata = new ObjectMetadata();
            metadata.setContentType("plain/text");
            metadata.addUserMetadata("title", "someTitle");
            request.setMetadata(metadata);
            s3Client.putObject(request);
        } catch (AmazonServiceException e) {
            // The call was transmitted successfully, but Amazon S3 couldn't process 
            // it, so it returned an error response.
            e.printStackTrace();
        } catch (SdkClientException e) {
            // Amazon S3 couldn't be contacted for a response, or the client
            // couldn't parse the response from Amazon S3.
            e.printStackTrace();
        }
    }
}
```

------
#### [ \.NET ]

The following C\# code example creates two objects with two `PutObjectRequest` requests:
+ The first `PutObjectRequest` request saves a text string as sample object data\. It also specifies the bucket and object key names\. 
+ The second `PutObjectRequest` request uploads a file by specifying the file name\. This request also specifies the `ContentType` header and optional object metadata \(a title\)\. 

For instructions on how to create and test a working sample, see [Running the Amazon S3 \.NET Code Examples](UsingTheMPDotNetAPI.md#TestingDotNetApiSamples)\.

```
using Amazon;
using Amazon.S3;
using Amazon.S3.Model;
using System;
using System.Threading.Tasks;

namespace Amazon.DocSamples.S3
{
    class UploadObjectTest
    {
        private const string bucketName = "*** bucket name ***";
        // For simplicity the example creates two objects from the same file.
        // You specify key names for these objects.
        private const string keyName1 = "*** key name for first object created ***";
        private const string keyName2 = "*** key name for second object created ***";
        private const string filePath = @"*** file path ***";
        private static readonly RegionEndpoint bucketRegion = RegionEndpoint.EUWest1; 

        private static IAmazonS3 client;

        public static void Main()
        {
            client = new AmazonS3Client(bucketRegion);
            WritingAnObjectAsync().Wait();
        }

        static async Task WritingAnObjectAsync()
        {
            try
            {
                // 1. Put object-specify only key name for the new object.
                var putRequest1 = new PutObjectRequest
                {
                    BucketName = bucketName,
                    Key = keyName1,
                    ContentBody = "sample text"
                };

                PutObjectResponse response1 = await client.PutObjectAsync(putRequest1);

                // 2. Put the object-set ContentType and add metadata.
                var putRequest2 = new PutObjectRequest
                {
                    BucketName = bucketName,
                    Key = keyName2,
                    FilePath = filePath,
                    ContentType = "text/plain"
                };
                
                putRequest2.Metadata.Add("x-amz-meta-title", "someTitle");
                PutObjectResponse response2 = await client.PutObjectAsync(putRequest2);
            }
            catch (AmazonS3Exception e)
            {
                Console.WriteLine(
                        "Error encountered ***. Message:'{0}' when writing an object"
                        , e.Message);
            }
            catch (Exception e)
            {
                Console.WriteLine(
                    "Unknown encountered on server. Message:'{0}' when writing an object"
                    , e.Message);
            }
        }
    }
}
```

------
#### [ PHP ]

 This topic guides you through using classes from the AWS SDK for PHP to upload an object of up to 5 GB in size\. For larger files, you must use multipart upload API\. For more information, see [Uploading and copying objects using multipart upload](mpuoverview.md)\.

 This topic assumes that you are already following the instructions for [Using the AWS SDK for PHP and Running PHP Examples](UsingTheMPphpAPI.md) and have the AWS SDK for PHP properly installed\.

**Example — Creating an object in an Amazon S3 bucket by uploading data**  
The following PHP example creates an object in a specified bucket by uploading data using the `putObject()` method\. For information about running the PHP examples in this guide, see [Running PHP Examples](UsingTheMPphpAPI.md#running-php-samples)\.   

```
 require 'vendor/autoload.php';

use Aws\S3\S3Client;
use Aws\S3\Exception\S3Exception;

$bucket = '*** Your Bucket Name ***';
$keyname = '*** Your Object Key ***';
                        
$s3 = new S3Client([
    'version' => 'latest',
    'region'  => 'us-east-1'
]);

try {
    // Upload data.
    $result = $s3->putObject([
        'Bucket' => $bucket,
        'Key'    => $keyname,
        'Body'   => 'Hello, world!',
        'ACL'    => 'public-read'
    ]);

    // Print the URL to the object.
    echo $result['ObjectURL'] . PHP_EOL;
} catch (S3Exception $e) {
    echo $e->getMessage() . PHP_EOL;
}
```

------
#### [ Ruby ]

The AWS SDK for Ruby \- Version 3 has two ways of uploading an object to Amazon S3\. The first uses a managed file uploader, which makes it easy to upload files of any size from disk\. To use the managed file uploader method:

1. Create an instance of the `Aws::S3::Resource` class\.

1. Reference the target object by bucket name and key\. Objects live in a bucket and have unique keys that identify each object\.

1. Call`#upload_file` on the object\.

**Example**  

```
require 'aws-sdk-s3'

# Uploads an object to a bucket in Amazon Simple Storage Service (Amazon S3).
#
# Prerequisites:
#
# - An S3 bucket.
# - An object to upload to the bucket.
#
# @param s3_client [Aws::S3::Resource] An initialized S3 resource.
# @param bucket_name [String] The name of the bucket.
# @param object_key [String] The name of the object.
# @param file_path [String] The path and file name of the object to upload.
# @return [Boolean] true if the object was uploaded; otherwise, false.
# @example
#   exit 1 unless object_uploaded?(
#     Aws::S3::Resource.new(region: 'us-east-1'),
#     'doc-example-bucket',
#     'my-file.txt',
#     './my-file.txt'
#   )
def object_uploaded?(s3_resource, bucket_name, object_key, file_path)
  object = s3_resource.bucket(bucket_name).object(object_key)
  object.upload_file(file_path)
  return true
rescue StandardError => e
  puts "Error uploading object: #{e.message}"
  return false
end
```

The second way that AWS SDK for Ruby \- Version 3 can upload an object uses the `#put` method of `Aws::S3::Object`\. This is useful if the object is a string or an I/O object that is not a file on disk\. To use this method:

1. Create an instance of the `Aws::S3::Resource` class\.

1. Reference the target object by bucket name and key\.

1. Call`#put`, passing in the string or I/O object\.

**Example**  

```
require 'aws-sdk-s3'

# Uploads an object to a bucket in Amazon Simple Storage Service (Amazon S3).
#
# Prerequisites:
#
# - An S3 bucket.
# - An object to upload to the bucket.
#
# @param s3_client [Aws::S3::Resource] An initialized S3 resource.
# @param bucket_name [String] The name of the bucket.
# @param object_key [String] The name of the object.
# @param file_path [String] The path and file name of the object to upload.
# @return [Boolean] true if the object was uploaded; otherwise, false.
# @example
#   exit 1 unless object_uploaded?(
#     Aws::S3::Resource.new(region: 'us-east-1'),
#     'doc-example-bucket',
#     'my-file.txt',
#     './my-file.txt'
#   )
def object_uploaded?(s3_resource, bucket_name, object_key, file_path)
  object = s3_resource.bucket(bucket_name).object(object_key)
  File.open(file_path, 'rb') do |file|
    object.put(body: file)
  end
  return true
rescue StandardError => e
  puts "Error uploading object: #{e.message}"
  return false
end
```

------

### Uploading an object using the REST API<a name="UploadObjSingleOpREST"></a>

You can use AWS SDK to upload an object\. However, if your application requires it, you can send REST requests directly\. You can send a PUT request to upload data in a single operation\. For more information, see [PUT Object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectPUT.html)\.

### Uploading an object using the AWS CLI<a name="UploadObjSingleOpCLI"></a>

You can use AWS SDK to upload an object\. However, if your application requires it, you can send AWS Command Line Interface \(AWS CLI\) requests directly\. You can send a PUT request to upload data in a single operation\. For more information, see [PutObject](https://docs.aws.amazon.com/cli/latest/reference/s3api/put-object.html) in the *AWS CLI Command Reference*\.