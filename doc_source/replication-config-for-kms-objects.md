# Replicating objects created with server\-side encryption \(SSE\-C, SSE\-S3, SSE\-KMS\)<a name="replication-config-for-kms-objects"></a>

**Important**  
Amazon S3 now applies server\-side encryption with Amazon S3 managed keys \(SSE\-S3\) as the base level of encryption for every bucket in Amazon S3\. Starting January 5, 2023, all new object uploads to Amazon S3 are automatically encrypted at no additional cost and with no impact on performance\. The automatic encryption status for S3 bucket default encryption configuration and for new object uploads is available in AWS CloudTrail logs, S3 Inventory, S3 Storage Lens, the Amazon S3 console, and as an additional Amazon S3 API response header in the AWS Command Line Interface and AWS SDKs\. For more information, see [Default encryption FAQ](https://docs.aws.amazon.com/AmazonS3/latest/userguide/default-encryption-faq.html)\.

There are some special considerations when you're replicating objects that have been encrypted by using server\-side encryption\. Amazon S3 supports the following types of server\-side encryption:
+ Server\-side encryption with AWS Key Management Service \(AWS KMS\) keys \(SSE\-KMS\)
+ Server\-side encryption with Amazon S3 managed keys \(SSE\-S3\)
+ Server\-side encryption with customer\-provided keys \(SSE\-C\)

For more information about server\-side encryption, see [Protecting data using server\-side encryption](serv-side-encryption.md)\.

This topic explains the permissions that you need to direct Amazon S3 to replicate objects that have been encrypted by using server\-side encryption\. This topic also provides additional configuration elements that you can add and example AWS Identity and Access Management \(IAM\) policies that grant the necessary permissions for replicating encrypted objects\. 

For an example with step\-by\-step instructions, see [Replicating encrypted objects](replication-walkthrough-4.md)\. For information about creating a replication configuration, see [Replicating objects](replication.md)\. 

**Note**  
You can use multi\-Region AWS KMS keys in Amazon S3\. However, Amazon S3 currently treats multi\-Region keys as though they were single\-Region keys, and does not use the multi\-Region features of the key\. For more information, see [ Using multi\-Region keys](https://docs.aws.amazon.com/kms/latest/developerguide/multi-region-keys-overview.html) in *AWS Key Management Service Developer Guide*\.

**Topics**
+ [How default bucket encryption affects replication](#replication-default-encryption)
+ [Replicating objects encrypted with SSE\-C](#replicationSSEC)
+ [Replicating objects encrypted with SSE\-S3 or SSE\-KMS](#replications)

## How default bucket encryption affects replication<a name="replication-default-encryption"></a>

When you enable default encryption for a replication destination bucket, the following encryption behavior applies:
+ If objects in the source bucket are not encrypted, the replica objects in the destination bucket are encrypted by using the default encryption settings of the destination bucket\. As a result, the entity tags \(ETags\) of the source objects differ from the ETags of the replica objects\. If you have applications that use ETags, you must update those applications to account for this difference\.
+ If objects in the source bucket are encrypted by using server\-side encryption with Amazon S3 managed keys \(SSE\-S3\) or server\-side encryption with AWS Key Management Service \(AWS KMS\) keys \(SSE\-KMS\), the replica objects in the destination bucket use the same type of encryption as the source objects\. The default encryption settings of the destination bucket are not used\.

## Replicating objects encrypted with SSE\-C<a name="replicationSSEC"></a>

By using server\-side encryption with customer\-provided keys \(SSE\-C\), you can manage proprietary encryption keys\. With SSE\-C, you manage the keys while Amazon S3 manages the encryption and decryption process\. You must provide an encryption key as part of your request, but you don't need to write any code to perform object encryption or decryption\. When you upload an object, Amazon S3 encrypts the object by using the key that you provided\. Amazon S3 then purges that key from memory\. When you retrieve an object, you must provide the same encryption key as part of your request\. For more information, see [Using server\-side encryption with customer\-provided keys \(SSE\-C\)](ServerSideEncryptionCustomerKeys.md)\.

S3 Replication supports objects that are encrypted with SSE\-C\. You can configure SSE\-C object replication in the Amazon S3 console or with the AWS SDKs, the same way that you configure replication for unencrypted objects\. There aren't additional SSE\-C permissions beyond what are currently required for replication\. 

S3 Replication automatically replicates newly uploaded SSE\-C encrypted objects if they are eligible, as per your S3 Replication configuration\. To replicate existing objects in your buckets, use S3 Batch Replication\. For more information about replicating objects, see [Setting up replication](replication-how-setup.md) and [Replicating existing objects with S3 Batch Replication](s3-batch-replication-batch.md)\.

There are no additional charges for replicating SSE\-C objects\. For details about replication pricing, see the [Amazon S3 pricing page](http://aws.amazon.com/s3/pricing/)\. 

## Replicating objects encrypted with SSE\-S3 or SSE\-KMS<a name="replications"></a>

By default, Amazon S3 doesn't replicate objects that are stored at rest by using server\-side encryption with customer managed keys that are stored in AWS Key Management Service \(AWS KMS\) \(SSE\-KMS\)\. This section explains the additional configuration elements that you can add to direct Amazon S3 to replicate these objects\. 

For an example with step\-by\-step instructions, see [Replicating encrypted objects](replication-walkthrough-4.md)\. For information about creating a replication configuration, see [Replicating objects](replication.md)\. 

### Specifying additional information in the replication configuration for SSE\-KMS<a name="replication-kms-extra-config"></a>

In the replication configuration, you do the following:
+ In the `Destination` element in your replication configuration, add the ID of the symmetric AWS KMS customer managed key that you want Amazon S3 to use to encrypt object replicas, as shown in the following example replication configuration\. 
+ Explicitly opt in by enabling replication of objects encrypted by using KMS keys\. To opt in, add the `SourceSelectionCriteria` element, as shown in the following example replication configuration\.

 

```
<ReplicationConfiguration>
   <Rule>
      ...
      <SourceSelectionCriteria>
         <SseKmsEncryptedObjects>
           <Status>Enabled</Status>
         </SseKmsEncryptedObjects>
      </SourceSelectionCriteria>

      <Destination>
          ...
          <EncryptionConfiguration>
             <ReplicaKmsKeyID>AWS KMS key ID that's in the same as the destination bucket.</ReplicaKmsKeyID>
          </EncryptionConfiguration>
       </Destination>
      ...
   </Rule>
</ReplicationConfiguration>
```

**Important**  
The KMS key must have been created in the same AWS Region as the destination buckets\.   
The KMS key *must* be valid\. The `PutBucketReplication` API operation doesn't check the validity of KMS keys\. If you use a KMS key that isn't valid, you will receive the HTTP `200 OK` status code in response, but replication fails\.

The following example shows a replication configuration that includes optional configuration elements\.

```
<?xml version="1.0" encoding="UTF-8"?>
<ReplicationConfiguration>
   <Role>arn:aws:iam::account-id:role/role-name</Role>
   <Rule>
      <ID>Rule-1</ID>
      <Priority>1</Priority>
      <Status>Enabled</Status>
      <DeleteMarkerReplication>
         <Status>Disabled</Status>
      </DeleteMarkerReplication>
      <Filter>
         <Prefix>Tax</Prefix>
      </Filter>
      <Destination>
         <Bucket>arn:aws:s3:::DOC-EXAMPLE-DESTINATION-BUCKET</Bucket>
         <EncryptionConfiguration>
            <ReplicaKmsKeyID>AWS KMS key ID that's in the same AWS Region as the destination buckets. (S3 uses this key to encrypt object replicas.)</ReplicaKmsKeyID>
         </EncryptionConfiguration>
      </Destination>
      <SourceSelectionCriteria>
         <SseKmsEncryptedObjects>
            <Status>Enabled</Status>
         </SseKmsEncryptedObjects>
      </SourceSelectionCriteria>
   </Rule>
</ReplicationConfiguration>
```

This replication configuration has one rule\. The rule applies to objects with the `Tax` key prefix\. Amazon S3 uses the specified AWS KMS key ID to encrypt these object replicas\.

### Granting additional permissions for the IAM role<a name="replication-kms-permissions"></a>

To replicate objects that are encrypted at rest by using SSE\-S3 or SSE\-KMS, grant the following additional permissions to the AWS Identity and Access Management \(IAM\) role that you specify in the replication configuration\. You grant these permissions by updating the permissions policy that's associated with the IAM role\. 
+ **`s3:GetObjectVersionForReplication` action for source objects** – This action allows Amazon S3 to replicate both unencrypted objects and objects created with server\-side encryption by using SSE\-S3 or SSE\-KMS\.
**Note**  
We recommend that you use the `s3:GetObjectVersionForReplication` action instead of the `s3:GetObjectVersion` action because `s3:GetObjectVersionForReplication` provides Amazon S3 with only the minimum permissions necessary for replication\. In addition, the `s3:GetObjectVersion` action allows replication of unencrypted and SSE\-S3\-encrypted objects, but not replication of objects encrypted by using SSE\-KMS\. 
+ **`kms:Decrypt` and `kms:Encrypt` AWS KMS actions for your KMS keys**
  + You must grant the `kms:Decrypt` permission for the AWS KMS key that's used to decrypt the source object\.
  + You must grant the `kms:Encrypt` permission for the AWS KMS key that's used to encrypt the object replica\.
+ **`kms:GenerateDataKey` action for replicating plaintext objects** – If you're replicating plaintext objects to a bucket with SSE\-KMS encryption enabled by default, you must include the `kms:GenerateDataKey` permission for the destination encryption context and the KMS key in the IAM policy\.

We recommend that you restrict these permissions only to the destination buckets and objects by using AWS KMS condition keys\. The AWS account that owns the IAM role must have permissions for the `kms:Encrypt` and `kms:Decrypt` actions for the KMS keys that are listed in the policy\. If the KMS keys are owned by another AWS account, the owner of the KMS keys must grant these permissions to the AWS account that owns the IAM role\. For more information about managing access to these KMS keys, see [Using IAM Policies with AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/iam-policies.html) in the* AWS Key Management Service Developer Guide*\.

### S3 Bucket Keys and replication<a name="bk-replication"></a>

To use replication with an S3 Bucket Key, the AWS KMS key policy for the KMS key that's used to encrypt the object replica must include the `kms:Decrypt` permission for the calling principal\. The call to `kms:Decrypt` verifies the integrity of the S3 Bucket Key before using it\. For more information, see [Using an S3 Bucket Key with replication](bucket-key.md#bucket-key-replication)\.

When an S3 Bucket Key is enabled for the source or destination bucket, the encryption context will be the bucket's Amazon Resource Name \(ARN\), not the object's ARN \(for example, `arn:aws:s3:::bucket_ARN`\)\. You must update your IAM policies to use the bucket ARN for the encryption context:

```
"kms:EncryptionContext:aws:s3:arn": [
"arn:aws:s3:::bucket_ARN"
]
```

For more information, see [Encryption context \(x\-amz\-server\-side\-encryption\-context\)](specifying-kms-encryption.md#s3-kms-encryption-context) \(in the "Using the REST API" section\) and [Changes to note before enabling an S3 Bucket Key](bucket-key.md#bucket-key-changes)\.

### Example policies – Using SSE\-S3 and SSE\-KMS with replication<a name="kms-replication-examples"></a>

The following example IAM policies show statements for using SSE\-S3 and SSE\-KMS with replication\.

**Example – Using SSE\-KMS with separate destination buckets**  
The following example policy shows statements for using SSE\-KMS with separate destination buckets\.   

```
{
    "Action": ["kms:Decrypt"],
    "Effect": "Allow",
    "Condition": {
        "StringLike": {
            "kms:ViaService": "s3.source-bucket-region.amazonaws.com",
            "kms:EncryptionContext:aws:s3:arn": [
                "arn:aws:s3:::DOC-EXAMPLE-SOURCE-BUCKET/key-prefix1*"
            ]
        }
    },
    "Resource": [
        "List of AWS KMS key ARNs that are used to encrypt source objects." 
    ]
},
{
    "Action": ["kms:Encrypt"],
    "Effect": "Allow",
    "Condition": {
        "StringLike": {
            "kms:ViaService": "s3.destination-bucket-1-region.amazonaws.com",
            "kms:EncryptionContext:aws:s3:arn": [
                "arn:aws:s3:::DOC-EXAMPLE-DESTINATION-BUCKET1/key-prefix1*"
            ]
        }
    },
    "Resource": [
         "AWS KMS key ARNs (in the same AWS Region as destination bucket 1). Used to encrypt object replicas created in destination bucket 1." 
    ]
},
{
    "Action": ["kms:Encrypt"],
    "Effect": "Allow",
    "Condition": {
        "StringLike": {
            "kms:ViaService": "s3.destination-bucket-2-region.amazonaws.com",
            "kms:EncryptionContext:aws:s3:arn": [
                "arn:aws:s3:::DOC-EXAMPLE-DESTINATION-BUCKET2/key-prefix1*"
            ]
        }
    },
    "Resource": [
         "AWS KMS key ARNs (in the same AWS Region as destination bucket 2). Used to encrypt object replicas created in destination bucket 2."
    ]
}
```

**Example – Replicating objects created with SSE\-S3 and SSE\-KMS**  
The following is a complete IAM policy that grants the necessary permissions to replicate unencrypted objects, objects created with SSE\-S3, and objects created with SSE\-KMS\.  

```
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Effect":"Allow",
         "Action":[
            "s3:GetReplicationConfiguration",
            "s3:ListBucket"
         ],
         "Resource":[
            "arn:aws:s3:::DOC-EXAMPLE-SOURCE-BUCKET"
         ]
      },
      {
         "Effect":"Allow",
         "Action":[
            "s3:GetObjectVersionForReplication",
            "s3:GetObjectVersionAcl"
         ],
         "Resource":[
            "arn:aws:s3:::DOC-EXAMPLE-SOURCE-BUCKET/key-prefix1*"
         ]
      },
      {
         "Effect":"Allow",
         "Action":[
            "s3:ReplicateObject",
            "s3:ReplicateDelete"
         ],
         "Resource":"arn:aws:s3:::DOC-EXAMPLE-DESTINATION-BUCKET/key-prefix1*"
      },
      {
         "Action":[
            "kms:Decrypt"
         ],
         "Effect":"Allow",
         "Condition":{
            "StringLike":{
               "kms:ViaService":"s3.source-bucket-region.amazonaws.com",
               "kms:EncryptionContext:aws:s3:arn":[
                  "arn:aws:s3:::DOC-EXAMPLE-SOURCE-BUCKET/key-prefix1*"
               ]
            }
         },
         "Resource":[
           "List of the AWS KMS key ARNs that are used to encrypt source objects."
         ]
      },
      {
         "Action":[
            "kms:Encrypt"
         ],
         "Effect":"Allow",
         "Condition":{
            "StringLike":{
               "kms:ViaService":"s3.destination-bucket-region.amazonaws.com",
               "kms:EncryptionContext:aws:s3:arn":[
                  "arn:aws:s3:::DOC-EXAMPLE-DESTINATION-BUCKET/prefix1*"
               ]
            }
         },
         "Resource":[
            "AWS KMS key ARNs (in the same AWS Region as the destination bucket) to use for encrypting object replicas"
         ]
      }
   ]
}
```

**Example – Replicating objects with S3 Bucket Keys**  
The following is a complete IAM policy that grants the necessary permissions to replicate objects with S3 Bucket Keys\.  

```
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Effect":"Allow",
         "Action":[
            "s3:GetReplicationConfiguration",
            "s3:ListBucket"
         ],
         "Resource":[
            "arn:aws:s3:::DOC-EXAMPLE-SOURCE-BUCKET"
         ]
      },
      {
         "Effect":"Allow",
         "Action":[
            "s3:GetObjectVersionForReplication",
            "s3:GetObjectVersionAcl"
         ],
         "Resource":[
            "arn:aws:s3:::DOC-EXAMPLE-SOURCE-BUCKET/key-prefix1*"
         ]
      },
      {
         "Effect":"Allow",
         "Action":[
            "s3:ReplicateObject",
            "s3:ReplicateDelete"
         ],
         "Resource":"arn:aws:s3:::DOC-EXAMPLE-DESTINATION-BUCKET/key-prefix1*"
      },
      {
         "Action":[
            "kms:Decrypt"
         ],
         "Effect":"Allow",
         "Condition":{
            "StringLike":{
               "kms:ViaService":"s3.source-bucket-region.amazonaws.com",
               "kms:EncryptionContext:aws:s3:arn":[
                  "arn:aws:s3:::DOC-EXAMPLE-SOURCE-BUCKET"
               ]
            }
         },
         "Resource":[
           "List of the AWS KMS key ARNs that are used to encrypt source objects."
         ]
      },
      {
         "Action":[
            "kms:Encrypt"
         ],
         "Effect":"Allow",
         "Condition":{
            "StringLike":{
               "kms:ViaService":"s3.destination-bucket-region.amazonaws.com",
               "kms:EncryptionContext:aws:s3:arn":[
                  "arn:aws:s3:::DOC-EXAMPLE-DESTINATION-BUCKET"
               ]
            }
         },
         "Resource":[
            "AWS KMS key ARNs (in the same AWS Region as the destination bucket) to use for encrypting object replicas"
         ]
      }
   ]
}
```

### Granting additional permissions for cross\-account scenarios<a name="replication-kms-cross-acct-scenario"></a>

In a cross\-account scenario, where the source and destination buckets are owned by different AWS accounts, you can use a KMS key to encrypt object replicas\. However, the KMS key owner must grant the source bucket owner permission to use the KMS key\. 

**Note**  
Objects encrypted through [AWS managed keys](http://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#aws-managed-cmk) cannot be shared cross\-account because you cannot modify the key policies\. If you need to replicate SSE\-KMS data cross\-account, you must use a [customer managed key](http://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#customer-cmk) from AWS KMS\. <a name="cross-acct-kms-key-permission"></a>

**To grant the source bucket owner permission to use the KMS key \(AWS KMS console\)**

1. Sign in to the AWS Management Console and open the AWS KMS console at [https://console\.aws\.amazon\.com/kms](https://console.aws.amazon.com/kms)\.

1. To change the AWS Region, use the Region selector in the upper\-right corner of the page\.

1. To view the keys in your account that you create and manage, in the navigation pane choose **Customer managed keys**\.

1. Choose the KMS key\.

1. Under the **General configuration** section, choose the **Key policy** tab\.

1. Scroll down to **Other AWS accounts**\.

1. Choose **Add other AWS accounts**\. 

   The **Other AWS accounts **dialog box appears\. 

1. In the dialog box, choose **Add another AWS account**\. For **arn:aws:iam::**, enter the source bucket account ID\.

1. Choose **Save changes**\.

**To grant the source bucket owner permission to use the KMS key \(AWS CLI\)**
+ For information about the `put-key-policy` AWS Command Line Interface \(AWS CLI\) command, see [http://docs.aws.amazon.com/cli/latest/reference/kms/put-key-policy.html](http://docs.aws.amazon.com/cli/latest/reference/kms/put-key-policy.html) in the* AWS CLI Command Reference*\. For information about the underlying `PutKeyPolicy` API operation, see [http://docs.aws.amazon.com/kms/latest/APIReference/API_PutKeyPolicy.html](http://docs.aws.amazon.com/kms/latest/APIReference/API_PutKeyPolicy.html) in the [AWS Key Management Service API Reference](https://docs.aws.amazon.com/kms/latest/APIReference/)\.

### AWS KMS transaction quota considerations<a name="crr-kms-considerations"></a>

When you add many new objects with AWS KMS encryption after enabling Cross\-Region Replication \(CRR\), you might experience throttling \(HTTP `503 Service Unavailable` errors\)\. Throttling occurs when the number of AWS KMS transactions per second exceeds the current quota\. For more information, see [Quotas]( http://docs.aws.amazon.com/kms/latest/developerguide/limits.html) in the *AWS Key Management Service Developer Guide*\.

To request a quota increase, use Service Quotas\. For more information, see [Amazon Web Services Quotas](https://docs.aws.amazon.com/kms/latest/developerguide/limits.html)\. If Service Quotas isn't supported in your Region, [open an AWS Support case](https://console.aws.amazon.com/support/home#/)\. 