# Setting up permissions<a name="setting-repl-config-perm-overview"></a>

When setting up replication, you must acquire the necessary permissions as follows:
+ Amazon S3 needs permissions to replicate objects on your behalf\. You grant these permissions by creating an IAM role and then specifying that role in your replication configuration\.
+ When the source and destination buckets aren't owned by the same accounts, the owner of the destination bucket must grant the source bucket owner permissions to store the replicas\.

**Topics**
+ [Creating an IAM role](#setting-repl-config-same-acctowner)
+ [Granting permissions when the source and destination buckets are owned by different AWS accounts](#setting-repl-config-crossacct)
+ [Changing replica ownership](#change-replica-ownership)
+ [Enable receiving replicated objects from a source bucket](#receiving-replicated-objects)

## Creating an IAM role<a name="setting-repl-config-same-acctowner"></a>



By default, all Amazon S3 resources—buckets, objects, and related subresources—are private, and only the resource owner can access the resource\. Amazon S3 needs permissions to read and replicate objects from the source bucket\. You grant these permissions by creating an IAM role and specifying the role in your replication configuration\. 

This section explains the trust policy and minimum required permissions policy\. The example walkthroughs provide step\-by\-step instructions to create an IAM role\. For more information, see [Walkthroughs: Examples for configuring replication](replication-example-walkthroughs.md)\.


+ The following example shows a *trust policy*, where you identify Amazon S3 as the service principal who can assume the role\.

  ```
  {
     "Version":"2012-10-17",
     "Statement":[
        {
           "Effect":"Allow",
           "Principal":{
              "Service":"s3.amazonaws.com"
           },
           "Action":"sts:AssumeRole"
        }
     ]
  }
  ```

  For more information about IAM roles, see [IAM Roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html) in the *IAM User Guide*\.
+ The following example shows an *access policy*, where you grant the role permissions to perform replication tasks on your behalf\. When Amazon S3 assumes the role, it has the permissions that you specify in this policy\. In this policy, `DOC-EXAMPLE-BUCKET1` is the source bucket, and `DOC-EXAMPLE-BUCKET2` is the destination bucket\.

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
              "arn:aws:s3:::DOC-EXAMPLE-BUCKET1"
           ]
        },
        {
           "Effect":"Allow",
           "Action":[
              "s3:GetObjectVersionForReplication",
              "s3:GetObjectVersionAcl",
              "s3:GetObjectVersionTagging"
           ],
           "Resource":[
              "arn:aws:s3:::DOC-EXAMPLE-BUCKET1/*"
           ]
        },
        {
           "Effect":"Allow",
           "Action":[
              "s3:ReplicateObject",
              "s3:ReplicateDelete",
              "s3:ReplicateTags"
           ],
           "Resource":"arn:aws:s3:::DOC-EXAMPLE-BUCKET2/*"
        }
     ]
  }
  ```

  The access policy grants permissions for the following actions:
  +  `s3:GetReplicationConfiguration` and `s3:ListBucket` – Permissions for these actions on the `DOC-EXAMPLE-BUCKET1` bucket \(the source bucket\) allow Amazon S3 to retrieve the replication configuration and list the bucket content\. \(The current permissions model requires the `s3:ListBucket` permission for accessing delete markers\.\)
  + `s3:GetObjectVersionForReplication` and `s3:GetObjectVersionAcl` – Permissions for these actions are granted on all objects to allow Amazon S3 to get a specific object version and access control list \(ACL\) associated with the objects\. 

    
  + `s3:ReplicateObject` and `s3:ReplicateDelete` – Permissions for these actions on all objects in the `DOC-EXAMPLE-BUCKET2` bucket \(the destination bucket\) allow Amazon S3 to replicate objects or delete markers to the destination bucket\. For information about delete markers, see [How delete operations affect replication](replication-what-is-isnot-replicated.md#replication-delete-op)\. 
**Note**  
Permissions for the `s3:ReplicateObject` action on the `DOC-EXAMPLE-BUCKET2` bucket \(the destination bucket\) also allow replication of object tags, so you don't need to explicitly grant permission for the `s3:ReplicateTags` action\.
  + `s3:GetObjectVersionTagging` – Permissions for this action on objects in the `DOC-EXAMPLE-BUCKET1` bucket \(the source bucket\) allow Amazon S3 to read object tags for replication\. For more information, see [Categorizing your storage using tags](object-tagging.md)\. If Amazon S3 doesn't have these permissions, it replicates the objects, but not the object tags\.

  For a list of Amazon S3 actions, see [Amazon S3 actions](using-with-s3-actions.md)\.
**Important**  
The AWS account that owns the IAM role must have permissions for the actions that it grants to the IAM role\.   
For example, suppose that the source bucket contains objects owned by another AWS account\. The owner of the objects must explicitly grant the AWS account that owns the IAM role the required permissions through the object ACL\. Otherwise, Amazon S3 can't access the objects, and replication of the objects fails\. For information about ACL permissions, see [Access control list \(ACL\) overview](acl-overview.md)\.  
  
The permissions described here are related to the minimum replication configuration\. If you choose to add optional replication configurations, you must grant additional permissions to Amazon S3\. For more information, see [Additional replication configurations](replication-additional-configs.md)\. 

## Granting permissions when the source and destination buckets are owned by different AWS accounts<a name="setting-repl-config-crossacct"></a>

When the source and destination buckets aren't owned by the same accounts, the owner of the destination bucket must also add a bucket policy to grant the owner of the source bucket permissions to perform replication actions, as follows\. In this policy, `DOC-EXAMPLE-BUCKET2` is the destination bucket\.

```
{
   "Version":"2012-10-17",
   "Id":"PolicyForDestinationBucket",
   "Statement":[
      {
         "Sid":"Permissions on objects",
         "Effect":"Allow",
         "Principal":{
            "AWS":"arn:aws:iam::SourceBucket-account-ID:role/service-role/source-account-IAM-role"
         },
         "Action":[
            "s3:ReplicateDelete",
            "s3:ReplicateObject"
         ],
         "Resource":"arn:aws:s3:::DOC-EXAMPLE-BUCKET2/*"
      },
      {
         "Sid":"Permissions on bucket",
         "Effect":"Allow",
         "Principal":{
            "AWS":"arn:aws:iam::SourceBucket-account-ID:role/service-role/source-account-IAM-role"
         },
         "Action": [
            "s3:List*",
            "s3:GetBucketVersioning",
            "s3:PutBucketVersioning"
         ],
         "Resource":"arn:aws:s3:::DOC-EXAMPLE-BUCKET2"
      }
   ]
}
```

For an example, see [Configuring replication when source and destination buckets are owned by different accounts](replication-walkthrough-2.md)\.

If objects in the source bucket are tagged, note the following:
+ If the source bucket owner grants Amazon S3 permission for the `s3:GetObjectVersionTagging` and `s3:ReplicateTags` actions to replicate object tags \(through the IAM role\), Amazon S3 replicates the tags along with the objects\. For information about the IAM role, see [Creating an IAM role](#setting-repl-config-same-acctowner)\. 
+ If the owner of the destination bucket doesn't want to replicate the tags, they can add the following statement to the destination bucket policy to explicitly deny permission for the `s3:ReplicateTags` action\. In this policy, `DOC-EXAMPLE-BUCKET2` is the destination bucket\.

  ```
  ...
     "Statement":[
        {
           "Effect":"Deny",
           "Principal":{
              "AWS":"arn:aws:iam::SourceBucket-account-id:role/service-role/source-account-IAM-role"
           },
           "Action":"s3:ReplicateTags",
           "Resource":"arn:aws:s3:::DOC-EXAMPLE-BUCKET2/*"
        }
     ]
  ...
  ```

## Changing replica ownership<a name="change-replica-ownership"></a>

When different AWS accounts own the source and destination buckets, you can tell Amazon S3 to change the ownership of the replica to the AWS account that owns the destination bucket\. For more information about owner override, see [Changing the replica owner](replication-change-owner.md)\.

## Enable receiving replicated objects from a source bucket<a name="receiving-replicated-objects"></a>

You can quickly generate the policies needed to enable receiving replicated objects from a source bucket through the AWS Management Console\. 

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Buckets**\.

1. In the **Buckets** list, choose the bucket that you want to use as a destination bucket\.

1. Choose the **Management** tab, and scroll down to **Replication rules**\.

1. For **Actions**, choose **Receive replicated objects**\. 

   Follow the prompts and enter the AWS account ID of the source bucket account and choose **Generate policies**\. This will generate an Amazon S3 bucket policy and a KMS key policy\.

1. To add this policy to your existing bucket policy, either choose **Apply settings** or choose **Copy** to manually copy the changes\. 

1. \(Optional\) Copy the AWS KMS policy to your desired KMS key policy on the AWS Key Management Service console\. 