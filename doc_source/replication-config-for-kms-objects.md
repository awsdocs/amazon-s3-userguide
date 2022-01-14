# Replicating objects created with server\-side encryption \(SSE\) using KMS keys<a name="replication-config-for-kms-objects"></a>

By default, Amazon S3 doesn't replicate objects that are stored at rest using server\-side encryption with customer managed keys stored in AWS KMS\. This section explains additional configuration that you add to direct Amazon S3 to replicate these objects\. 

**Note**  
You can use multi\-Region AWS KMS keys in Amazon S3\. However, Amazon S3 currently treats multi\-Region keys as though they were single\-Region keys, and does not use the multi\-Region features of the key\. For more information, see [ Using multi\-Region keys](https://docs.aws.amazon.com/kms/latest/developerguide/multi-region-keys-overview.html) in *AWS Key Management Service Developer Guide*\.

For an example with step\-by\-step instructions, see [Replicating encrypted objects](replication-walkthrough-4.md)\. For information about creating a replication configuration, see [Replicating objects](replication.md)\. 

**Important**  
Replication of encrypted data is a server\-side process that occurs entirely within Amazon S3\. Objects created with server\-side encryption using customer\-provided \(SSE\-C\) encryption keys are not replicated\.

**Topics**
+ [Specifying additional information in the replication configuration](#replication-kms-extra-config)
+ [Granting additional permissions for the IAM role](#replication-kms-extra-permissions)
+ [Granting additional permissions for cross\-account scenarios](#replication-kms-cross-acct-scenario)
+ [AWS KMS transaction limit considerations](#replication-kms-considerations)

## Specifying additional information in the replication configuration<a name="replication-kms-extra-config"></a>

In the replication configuration, you do the following:
+ In the `Destination` configuration, add the symmetric AWS KMS customer managed key that you want Amazon S3 to use to encrypt object replicas\. 
+ Explicitly opt in by enabling replication of objects encrypted using KMS keys by adding the `SourceSelectionCriteria` element\.

 

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
             <ReplicaKmsKeyID>AWS KMS key ID for the AWS Region of the destination bucket.</ReplicaKmsKeyID>
          </EncryptionConfiguration>
       </Destination>
      ...
   </Rule>
</ReplicationConfiguration>
```

**Important**  
The KMS key must have been created in the same AWS Region as the destination buckets\.   
The KMS key *must* be valid\. The `PUT` Bucket replication API doesn't check the validity of KMS keys\. If you use an invalid KMS key, you will receive the 200 OK status code in response, but replication fails\.

The following example shows a replication configuration, which includes optional configuration elements\.

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
         <Bucket>arn:aws:s3:::destination-bucket</Bucket>
         <EncryptionConfiguration>
           <ReplicaKmsKeyID>The AWS KMS key ID for the AWS Region of the destination buckets (S3 uses it to encrypt object replicas).</ReplicaKmsKeyID>
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

This replication configuration has one rule\. The rule applies to objects with the `Tax` key prefix\. Amazon S3 uses the AWS KMS key ID to encrypt these object replicas\.

## Granting additional permissions for the IAM role<a name="replication-kms-extra-permissions"></a>

To replicate objects that are encrypted at rest under AWS Key Management Service \(AWS KMS\), grant the following additional permissions to the IAM role you specify in the replication configuration\. You grant these permissions by updating the permission policy associated with the IAM role\. Objects created with server\-side encryption using customer\-provided \(SSE\-C\) encryption keys are not replicated\.
+ **`s3:GetObjectVersionForReplication` action for source objects** – Allows Amazon S3 to replicate both unencrypted objects and objects created with server\-side encryption using Amazon S3 managed encryption \(SSE\-S3\) keys or KMS keys \(SSE\-KMS\)\.
**Note**  
We recommend that you use the `s3:GetObjectVersionForReplication` action instead of the `s3:GetObjectVersion` action because it provides Amazon S3 with only the minimum permissions necessary for replication\. In addition, permission for the `s3:GetObjectVersion` action allows replication of unencrypted and SSE\-S3\-encrypted objects, but not of objects created using a KMS key\. 
+ **`kms:Decrypt` and `kms:Encrypt` AWS KMS actions**:
  + `kms:Decrypt` permissions for the KMS key used to decrypt the source object
  + `kms:Encrypt` permissions for the KMS key used to encrypt the object replica
**Note**  
The key policy must allow the use of IAM policies to control access to a KMS key\. If the key policy doesn't allow it, IAM policies that attempt to control access to a KMS key are ineffective\. For information, see [Default key policy](https://docs.aws.amazon.com/kms/latest/developerguide/key-policy-default.html) in the AWS Key Management Service Developer Guide\.

We recommend that you restrict these permissions only to the destination buckets and objects using AWS KMS condition keys\. The AWS account that owns the IAM role must have permissions for these AWS KMS actions \(`kms:Encrypt` and `kms:Decrypt`\) for KMS keys listed in the policy\. If the KMS keys are owned by another AWS account, the KMS key owner must grant these permissions to the AWS account that owns the IAM role\. For more information about managing access to these KMS keys, see [Using IAM Policies with AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/iam-policies.html) in the* AWS Key Management Service Developer Guide*\.

### Amazon S3 Bucket Keys and replication<a name="bk-replication"></a>

When an S3 Bucket Key is enabled for the source and destination bucket, the encryption context will be the bucket Amazon Resource Name \(ARN\) and not the object ARN, for example, `arn:aws:s3:::bucket_ARN`\. You need to update your IAM policies to use the bucket ARN for the encryption context\. However, if an S3 Bucket Key is only enabled on the destination bucket and not the source bucket, you don't need to update your IAM policies to use the bucket ARN for the encryption context\.

The example below shows the encryption context with the bucket ARN\.

```
"kms:EncryptionContext:aws:s3:arn": [
"arn:aws:s3:::bucket_ARN"
]
```

For more information, see [Encryption context](UsingKMSEncryption.md#encryption-context) and [Changes to note before enabling an S3 Bucket Key](bucket-key.md#bucket-key-changes)\.

### Example policies \- Using AWS KMS server\-side\-encryption \(SSE\-KMS\) with replication<a name="kms-replication-examples"></a>

The following example IAM policies show statements for using AWS KMS server\-side encryption with replication\.

In this example, the encryption context is the object ARN\. If you use SSE\-KMS with an S3 Bucket Key *enabled*, you must use the bucket ARN as the encryption context\. For more information, see [Encryption context](UsingKMSEncryption.md#encryption-context)\.

**Example Using AWS KMS server\-side\-encryption \(SSE\-KMS\) – separate destination buckets**  
The following example policy shows statements for using AWS KMS with separate destination buckets\.  

```
{
	"Version": "2012-10-17",
	"Statement": [{
			"Action": ["kms:Decrypt"],
			"Effect": "Allow",
			"Resource": "List of AWS KMS key ARNs used to encrypt source objects.",
			"Condition": {
				"StringLike": {
					"kms:ViaService": "s3.source-bucket-region.amazonaws.com",
					"kms:EncryptionContext:aws:s3:arn": "arn:aws:s3:::source-bucket-name/key-prefix1/*"
				}
			}
		},

		{
			"Action": ["kms:Encrypt"],
			"Effect": "Allow",
			"Resource": "AWS KMS key ARNs (for the AWS Region of the destination bucket 1). Used to encrypt object replicas created in destination bucket 1.",
			"Condition": {
				"StringLike": {
					"kms:ViaService": "s3.destination-bucket-1-region.amazonaws.com",
					"kms:EncryptionContext:aws:s3:arn": "arn:aws:s3:::destination-bucket-name-1/key-prefix1/*"
				}
			}
		},
		{
			"Action": ["kms:Encrypt"],
			"Effect": "Allow",
			"Resource": "AWS KMS key ARNs (for the AWS Region of destination bucket 2). Used to encrypt object replicas created in destination bucket 2.",
			"Condition": {
				"StringLike": {
					"kms:ViaService": "s3.destination-bucket-2-region.amazonaws.com",
					"kms:EncryptionContext:aws:s3:arn": "arn:aws:s3:::destination-bucket-2-name/key-prefix1*"
				}
			}
		}
	]
}
```

**Example Replicating objects created with server\-side encryption using Amazon S3 managed encryption keys and KMS keys**  
The following is a complete IAM policy that grants the necessary permissions to replicate unencrypted objects, objects created with server\-side encryption using Amazon S3 managed encryption keys and KMS keys\.  
Objects created with server\-side encryption using customer\-provided \(SSE\-C\) encryption keys are not replicated\.

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
            "arn:aws:s3:::source-bucket"
         ]
      },
      {
         "Effect":"Allow",
         "Action":[
            "s3:GetObjectVersionForReplication",
            "s3:GetObjectVersionAcl"
         ],
         "Resource":[
            "arn:aws:s3:::source-bucket/key-prefix1*"
         ]
      },
      {
         "Effect":"Allow",
         "Action":[
            "s3:ReplicateObject",
            "s3:ReplicateDelete"
         ],
         "Resource":"arn:aws:s3:::destination-bucket/key-prefix1*"
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
                  "arn:aws:s3:::source-bucket-name/key-prefix1*"
               ]
            }
         },
         "Resource":[
           "List of AWS KMS key ARNs used to encrypt source objects."
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
                  "arn:aws:s3:::destination-bucket-name/prefix1*"
               ]
            }
         },
         "Resource":[
            "AWS KMS key ARNs (for the AWS Region of the destination buckets) to use for encrypting object replicas"
         ]
      }
   ]
}
```

## Granting additional permissions for cross\-account scenarios<a name="replication-kms-cross-acct-scenario"></a>

In a cross\-account scenario, where *source* and *destination* buckets are owned by different AWS accounts, you can use a customer managed key to encrypt object replicas\. However, the KMS key owner must grant the source bucket owner permission to use the KMS key\. <a name="cross-acct-kms-key-permission"></a>

**To grant the source bucket owner permission to use the KMS key \(IAM console\)**

1. Sign in to the AWS Management Console and open the AWS Key Management Service \(AWS KMS\) console at [https://console\.aws\.amazon\.com/kms](https://console.aws.amazon.com/kms)\.

1. To change the AWS Region, use the Region selector in the upper\-right corner of the page\.

1.  To view the keys in your account that you create and manage, in the navigation pane choose **Customer managed keys**\.

1. Choose the KMS key\.

1. Under **General configuration**, choose the **Key policy** tab\.

1. Choose **Other AWS accounts**\.

1. Choose **Add another AWS account**\.

1. In **arn:aws:iam::**, enter the source bucket account ID\.

1. Choose **Save Changes**\.

**To grant the source bucket owner permission to use the KMS key \(AWS CLI\)**
+ For information, see [put\-key\-policy](http://docs.aws.amazon.com/cli/latest/reference/kms/put-key-policy.html) in the* AWS CLI Command Reference*\. For information about the underlying API, see [PutKeyPolicy](http://docs.aws.amazon.com/kms/latest/APIReference/API_PutKeyPolicy.html) in the *[AWS Key Management Service API Reference](https://docs.aws.amazon.com/kms/latest/APIReference/)\.*

## AWS KMS transaction limit considerations<a name="replication-kms-considerations"></a>

When you add many new objects with AWS KMS encryption after enabling cross\-region replication \(CRR\), you might experience throttling \(HTTP 503 Slow Down errors\)\. Throttling occurs when the number of AWS KMS transactions per second exceeds the current limit\. For more information, see [Limits]( http://docs.aws.amazon.com/kms/latest/developerguide/limits.html) in the *AWS Key Management Service Developer Guide*\.

To request a limit increase, use Service Quotas\. For more information, see [Amazon Web Services Limits](https://docs.aws.amazon.com/kms/latest/developerguide/limits.html)\. If Service Quotas isn't supported in your Region, [open an AWS Support case](https://console.aws.amazon.com/support/home#/)\. 