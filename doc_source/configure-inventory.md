# Configuring Amazon S3 Inventory<a name="configure-inventory"></a>

Amazon S3 Inventory provides a flat file list of your objects and metadata, which is a scheduled alternative to the Amazon S3 synchronous `List` API operation\. Amazon S3 Inventory provides comma\-separated values \(CSV\) or [Apache optimized row columnar \(ORC\)](https://orc.apache.org/) or [Apache Parquet \(Parquet\)](https://parquet.apache.org/) output files that list your objects and their corresponding metadata on a daily or weekly basis for an S3 bucket or for objects that share a prefix \(objects that have names that begin with the same string\)\. For more information, see [ Amazon S3 Inventory](storage-inventory.md)\.

This section describes how to configure an inventory, including details about the inventory source and destination buckets\.

**Topics**
+ [Overview](#storage-inventory-setting-up)
+ [Creating a destination bucket policy](#configure-inventory-destination-bucket-policy)
+ [Granting Amazon S3 permission to use your AWS KMS key for encryption](#configure-inventory-kms-key-policy)
+ [Configuring inventory using the S3 console](#configure-inventory-console)

## Overview<a name="storage-inventory-setting-up"></a>

Amazon S3 Inventory helps you manage your storage by creating lists of the objects in an S3 bucket on a defined schedule\. You can configure multiple inventory lists for a bucket\. The inventory lists are published to CSV, ORC, or Parquet files in a destination bucket\. 

The easiest way to set up an inventory is by using the AWS Management Console, but you can also use the REST API, AWS CLI, or AWS SDKs\. The console performs the first step of the following procedure for you: adding a bucket policy to the destination bucket\.

**To set up Amazon S3 Inventory for an S3 bucket**

1. **Add a bucket policy for the destination bucket\.**

   You must create a bucket policy on the destination bucket to grant permissions to Amazon S3 to write objects to the bucket in the defined location\. For an example policy, see [Granting permissions for Amazon S3 Inventory and Amazon S3 analytics](example-bucket-policies.md#example-bucket-policies-use-case-9)\. 

1. **Configure an inventory to list the objects in a source bucket and publish the list to a destination bucket\.**

   When you configure an inventory list for a source bucket, you specify the destination bucket where you want the list to be stored, and whether you want to generate the list daily or weekly\. You can also configure what object metadata to include and whether to list all object versions or only current versions\. 

   You can specify that the inventory list file be encrypted by using an Amazon S3 managed key \(SSE\-S3\) or an AWS Key Management Service \(AWS KMS\) customer managed key\. For more information about SSE\-S3 and SSE\-KMS, see [Protecting data using server\-side encryption](serv-side-encryption.md)\. If you plan to use SSE\-KMS encryption, see Step 3\.
   + For information about how to use the console to configure an inventory list, see [Configuring Amazon S3 Inventory](#configure-inventory)\.
   + To use the Amazon S3 API to configure an inventory list, use the [PUT Bucket inventory configuration](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketPUTInventoryConfig.html) REST API or the equivalent from the AWS CLI or AWS SDKs\. 

1. **To encrypt the inventory list file with SSE\-KMS, grant Amazon S3 permission to use the AWS KMS key\.**

   You can configure encryption for the inventory list file by using the AWS Management Console, REST API, AWS CLI, or AWS SDKs\. Whichever way you choose, you must grant Amazon S3 permission to use the customer managed key to encrypt the inventory file\. You grant Amazon S3 permission by modifying the key policy for the customer managed key that you want to use to encrypt the inventory file\. For more information, see the next section, [Granting Amazon S3 permission to use your AWS KMS key for encryption](#configure-inventory-kms-key-policy)\.

## Creating a destination bucket policy<a name="configure-inventory-destination-bucket-policy"></a>

Amazon S3 creates a bucket policy on the destination bucket that grants Amazon S3 write permission\. This allows Amazon S3 to write data for the inventory reports to the bucket\. 

If an error occurs when you try to create the bucket policy, you are given instructions on how to fix it\. For example, if you choose a destination bucket in another AWS account and don't have permissions to read and write to the bucket policy, you see an error message\.

In this case, the destination bucket owner must add the displayed bucket policy to the destination bucket\. If the policy is not added to the destination bucket, you won't get an inventory report because Amazon S3 doesn’t have permission to write to the destination bucket\. If the source bucket is owned by a different account than that of the current user, the correct account ID of the source bucket must be substituted in the policy\.

## Granting Amazon S3 permission to use your AWS KMS key for encryption<a name="configure-inventory-kms-key-policy"></a>

To grant Amazon S3 permission to encrypt using a customer managed AWS Key Management Service \(AWS KMS\)key, you must use a key policy\. To update your key policy so that you can use customer managed key, follow the steps below\.

**To grant permissions to encrypt using your KMS key**

1. Using the AWS account that owns the customer managed key, sign into the AWS Management Console\.

1. Open the AWS KMS console at [https://console\.aws\.amazon\.com/kms](https://console.aws.amazon.com/kms)\.

1. To change the AWS Region, use the Region selector in the upper\-right corner of the page\.

1. In the left navigation pane, choose **Customer managed keys**\.

1. Under **Customer managed keys**, choose the customer managed key that you want to use to encrypt the inventory file\.

1. Under **Key policy**, choose **Switch to policy view**\.

1. To update the key policy, choose **Edit**\.

1. Under **Edit key policy**, add the following key policy to the existing key policy\.

   ```
   {
       "Sid": "Allow Amazon S3 use of the KMS key",
       "Effect": "Allow",
       "Principal": {
           "Service": "s3.amazonaws.com"
       },
       "Action": [
           "kms:GenerateDataKey"
       ],
       "Resource": "*",
       "Condition":{
         "StringEquals":{
            "aws:SourceAccount":"source-account-id"
        },
         "ArnLike":{
           "aws:SourceARN": "arn:aws:s3:::source-bucket-name"
        }
      }
   }
   ```

1. Choose **Save changes**\.

For more information about creating customer managed keys and using key policies, see the following links in the *AWS Key Management Service Developer Guide*:
+ [Getting Started](https://docs.aws.amazon.com/kms/latest/developerguide/getting-started.html)
+ [Using Key Policies in AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/key-policies.html)

## Configuring inventory using the S3 console<a name="configure-inventory-console"></a>

Use these instructions to configure inventory using the S3 console\.
**Note**  
It may take up to 48 hours to deliver the first report\.

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the name of the bucket for which you want to configure Amazon S3 Inventory\.

1. Choose **Management**\.

1. Under **Inventory configurations**, choose **Create inventory configuration**\.

1. In **Inventory configuration name**, enter a name\.

1. Set the **Inventory scope**:
   + Enter an optional prefix\.
   + Choose object versions: **Current versions only** or **Include all versions**\.

1. Under **Report details**, choose the location of the AWS account that you want to save the reports to: **This account** or **A different account**\.

1. Under **Destination**, choose the destination bucket where you want reports to be saved\.

   The destination bucket must be in the same AWS Region as the bucket for which you are setting up the inventory\. The destination bucket can be in a different AWS account\. Under the **Destination** bucket field, you see the **Destination bucket permission** that is added to the destination bucket policy to allow Amazon S3 to place data in that bucket\. For more information, see [Creating a destination bucket policy](#configure-inventory-destination-bucket-policy)\.

1. Under **Frequency**, choose how often the report will be generated: **Daily** or **Weekly**\.

1. Choose the **Output format** for the report:
   + **CSV**
   + **Apache ORC**
   + **Apache Parquet**

1. Under **Status**, choose **Enable** or **Disable**\.

1. To use server\-side encryption, under **Server\-side encryption**, follow these steps:

   1. Choose **Enable**\.

   1. Under **Encryption key type**, choose **Amazon S3 key \(SSE\-S3\)** or **AWS Key Management Service key \(SSE\-KMS\)**\.

      Amazon S3 server\-side encryption uses 256\-bit Advanced Encryption Standard \(AES\-256\)\. For more information, see [Protecting data using server\-side encryption with Amazon S3\-managed encryption keys \(SSE\-S3\)](UsingServerSideEncryption.md)\. For more information about SSE\-KMS, see [Protecting Data Using Server\-Side Encryption with AWS Key Management Service \(SSE\-KMS\)](UsingKMSEncryption.md)\.

   1. To use an AWS KMS key, choose one of the following:
      + **AWS managed key \(aws/s3\)**
      + **Choose from your AWS KMS keys**, and choose your **KMS key**\.
      + **Enter AWS KMS key ARN**, and enter your AWS KMS key ARN\.
**Note**  
To encrypt the inventory list file with SSE\-KMS, you must grant Amazon S3 permission to use the AWS KMS key\. For instructions, see [Grant Amazon S3 Permission to Encrypt Using Your KMS Keys](#configure-inventory-kms-key-policy)\.

1. For **Additional fields**, select one or more of the following to add to the inventory report:
   + **Size** – The object size in bytes\.
   + **Last modified date** – The object creation date or the last modified date, whichever is the latest\.
   + **Storage class** – The storage class used for storing the object\. 
   + **ETag** – The entity tag is a hash of the object\. The ETag reflects changes only to the contents of an object, and not its metadata\. The ETag may or may not be an MD5 digest of the object data\. Whether it is depends on how the object was created and how it is encrypted\.
   +  **Multipart upload** – Specifies that the object was uploaded as a multipart upload\. For more information, see [Uploading and copying objects using multipart upload](mpuoverview.md)\.
   + **Replication status** – The replication status of the object\. For more information, see [Using the S3 console](enable-replication.md)\.
   + **Encryption status** – The server\-side encryption used to encrypt the object\. For more information, see [Protecting data using server\-side encryption](serv-side-encryption.md)\.
   + **S3 Object Lock configurations** – The Object Lock status of the object, including the following settings: 
     + **Retention mode** – The level of protection applied to the object, either *Governance* or *Compliance*\.
     + **Retain until date** – The date until which the locked object cannot be deleted\.
     + **Legal hold status** – The legal hold status of the locked object\. 

     For information about S3 Object Lock, see [How S3 Object Lock works](object-lock-overview.md)\.
   + **Intelligent\-Tiering access tier** – Indicates the access tier \(frequent or infrequent\) of the object if it was stored in Intelligent\-Tiering\. For more information, see [Storage class for automatically optimizing data with changing or unknown access patterns](storage-class-intro.md#sc-dynamic-data-access)\.
   + **S3 Bucket Key status** – Indicates whether a bucket\-level key generated by AWS KMS applies to the object\. For more information, see [Reducing the cost of SSE\-KMS with Amazon S3 Bucket Keys](bucket-key.md)\.

   For more information about the contents of an inventory report, see [Amazon S3 Inventory list](storage-inventory.md#storage-inventory-contents)\.

1. Choose **Create**\.