# Creating replication rules on Outposts<a name="replication-between-outposts"></a>

S3 Replication on Outposts is the automatic, asynchronous replication of objects across buckets in the same or different AWS Outposts\. Replication copies newly created objects and object updates from a source Outposts bucket to a destination Outposts bucket or buckets\. For more information, see [Replicating objects for S3 on Outposts](S3OutpostsReplication.md)\.

**Note**  
Objects that existed in the source Outposts bucket before you set up replication rules aren't replicated\. In other words, S3 on Outposts doesn't replicate objects retroactively\. To replicate objects that were created before your replication configuration, you can use the `CopyObject` API operation to copy them to the same bucket\. After the objects are copied, they appear as "new" objects in the bucket and the replication configuration will apply to them\. For more information about copying an object, see [Copying an object in an Amazon S3 on Outposts bucket using the AWS SDK for Java](S3OutpostsCopyObject.md) and [https://docs.aws.amazon.com/AmazonS3/latest/API/API_CopyObject.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_CopyObject.html) in the *Amazon Simple Storage Service API Reference*\.

When you configure replication, you add replication rules to the source Outposts bucket\. Replication rules define which source Outposts bucket objects to replicate and the destination Outposts bucket or buckets where the replicated objects will be stored\. You can create a rule to replicate all the objects in a bucket or a subset of objects with a specific key name prefix, one or more object tags, or both\. A destination Outposts bucket can be in the same Outpost as the source Outposts bucket, or it can be in a different Outpost\.

For S3 on Outposts replication rules, you must provide both the source Outposts bucket's access point Amazon Resource Name \(ARN\) and the destination Outposts bucket's access point ARN instead of the source and destination Outposts bucket names\. 

If you specify an object version ID to delete, S3 on Outposts deletes that object version in the source Outposts bucket\. But it doesn't replicate the deletion to the destination Outposts bucket\. In other words, it doesn't delete the same object version from the destination Outposts bucket\. This behavior protects data from malicious deletions\.

When you add a replication rule to an Outposts bucket, the rule is enabled by default, so the rule starts working as soon as you save it\. 

In this example, you set up replication for source and destination Outposts buckets that are on different Outposts and are owned by the same AWS account\. Examples are provided for using the Amazon S3 console, the AWS Command Line Interface \(AWS CLI\), and the AWS SDK for Java and AWS SDK for \.NET\. For information about cross\-account S3 Replication on Outposts permissions, see [Granting permissions when the source and destination Outposts buckets are owned by different AWS accounts](outposts-replication-prerequisites-config.md#outposts-rep-prethree)\.

For prerequisites to set up S3 on Outposts replication rules, see [Prerequisites for creating replication rules](outposts-replication-prerequisites-config.md)\.

## Using the S3 console<a name="outposts-enable-replication"></a>

Follow these steps to configure a replication rule when the destination Amazon S3 on Outposts bucket is in a different Outpost from the source Outposts bucket\.

If the destination Outposts bucket is in a different account from the source Outposts bucket, you must add a bucket policy to the destination Outposts bucket to grant the owner of the source Outposts bucket account permission to replicate objects in the destination Outposts bucket\. For more information, see [Granting permissions when the source and destination buckets are owned by different AWS accounts](setting-repl-config-perm-overview.md#setting-repl-config-crossacct)\.

**To create a replication rule**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Outposts Buckets** list, choose the name of the bucket that you want to use as the source bucket\.

1. Choose the **Management** tab, scroll down to the **Replication rules** section, and then choose **Create replication rule**\.

1. For **Replication rule name**, enter a name for your rule to help identify the rule later\. The name is required and must be unique within the bucket\.

1. Under **Status**, **Enabled** is chosen by default\. An enabled rule starts to work as soon as you save it\. If you want to enable the rule later, choose **Disabled**\.

1. Under **Priority**, the rule's priority value determines which rule to apply if there are overlapping rules\. When objects are included in the scope of more than one replication rule, S3 on Outposts uses these priority values to avoid conflicts\. By default, new rules are added to the replication configuration at the highest priority\. The higher the number, the higher the priority\. 

   To change the priority for the rule, after you save the rule, choose the rule name from the replication rule list, choose **Actions**, and then choose **Edit priority**\. 

1. Under **Source bucket**, you have the following options for setting the replication source:
   + To replicate the whole bucket, choose **Apply to *all *objects in the bucket**\. 
   + To apply prefix or tag filtering to the replication source, choose **Limit the scope of this rule by using one or more filters**\. You can combine a prefix and tags\. 
     + To replicate all objects that have the same prefix, under **Prefix**, enter a prefix in the box\. Using the **Prefix** filter limits replication to all objects that have names that begin with the same string \(for example, `pictures`\)\.

       If you enter a prefix that is the name of a folder, you must use a **/** \(forward slash\) as the last character \(for example, `pictures/`\)\.
     + To replicate all objects that have one or more of the same object tags, choose **Add tag**, and then enter the key\-value pair in the boxes\. To add another tag, repeat the procedure\. For more information about object tags, see [Adding tags for S3 on Outposts buckets](S3OutpostsBucketTags.md)\.

1. To access your S3 on Outposts source bucket for replication, under **Source access point name**, choose an access point that is attached to the source bucket\.

1. Under **Destination**, choose the access point ARN of the destination Outposts bucket where you want S3 on Outposts to replicate objects\. The destination Outposts bucket can be in the same or a different AWS account as the source Outposts bucket\.

   If the destination bucket is in a different account from the source Outposts bucket, you must add a bucket policy to the destination Outposts bucket to grant the owner of the source Outposts bucket account permission to replicate objects to the destination Outposts bucket\. For more information, see [Granting permissions when the source and destination Outposts buckets are owned by different AWS accounts](outposts-replication-prerequisites-config.md#outposts-rep-prethree)\.
**Note**  
If versioning is not enabled on the destination Outposts bucket, you get a warning that contains an **Enable versioning** button\. Choose this button to enable versioning on the bucket\.

1. Set up an AWS Identity and Access Management \(IAM\) service role that S3 on Outposts can assume to replicate objects on your behalf\.

   To set up an IAM role, under **IAM role**, do one of the following:
   + To have S3 on Outposts create a new IAM role for your replication configuration, choose **Choose from existing IAM roles**, and then choose **Create new role**\. When you save the rule, a new policy is generated for the IAM role that matches the source and destination Outposts buckets that you choose\. We recommend that you choose **Create new role**\.
   + You can also choose to use an existing IAM role\. If you do, you must choose a role that grants S3 on Outposts the necessary permissions for replication\. If this role doesn't grant S3 on Outposts sufficient permissions to follow your replication rule, replication fails\.

     To choose an existing role, choose **Choose from existing IAM roles**, and then choose the role from the dropdown menu\. You can also choose **Enter an IAM role ARN** and then enter the IAM role's Amazon Resource Name \(ARN\)\. 
**Important**  
When you add a replication rule to an S3 on Outposts bucket, you must have the `iam:CreateRole` and `iam:PassRole` permissions to be able to create and pass the IAM role that grants S3 on Outposts replication permissions\. For more information, see [Granting a user permissions to pass a role to an AWS service](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_passrole.html) in the *IAM User Guide*\.

1. All objects in Outposts buckets are encrypted by default\. For more information about S3 on Outposts encryption, see [Data encryption in S3 on Outposts](s3-outposts-data-encryption.md)\. Only objects that are encrypted by using server\-side encryption with Amazon S3 managed keys \(SSE\-S3\) can be replicated\. The replication of objects that are encrypted by using server\-side encryption with AWS Key Management Service \(AWS KMS\) keys \(SSE\-KMS\) or server\-side encryption with customer\-provided encryption keys \(SSE\-C\) is not supported\.

1. As needed, enable the following additional options while setting the replication rule configuration:
   + If you want to enable S3 on Outposts replication metrics in your replication configuration, select **Replication metrics**\. For more information, see [Monitoring progress with replication metrics](manage-outposts-replication.md#outposts-enabling-replication-metrics)\.
   + If you want to enable delete marker replication in your replication configuration, select **Delete marker replication**\. For more information, see [How delete operations affect replication](S3OutpostsReplication.md#outposts-replication-delete-op)\.
   + If you want to replicate metadata changes made to the replicas back to the source objects, select **Replica modification sync**\. For more information, see [Replication status if Amazon S3 replica modification sync on Outposts is enabled](manage-outposts-replication.md#outposts-replication-status-sync)\.

1. To finish, choose **Create rule**\.

After you save your rule, you can edit, enable, disable, or delete your rule\. To do so, go to the **Management** tab for the source Outposts bucket, scroll down to the **Replication rules** section, choose your rule, and then choose **Edit rule**\. 

## Using the AWS CLI<a name="outposts-replication-ex1-cli"></a>

To use the AWS CLI to set up replication when the source and destination Outposts buckets are owned by the same AWS account, you do the following:
+ Create source and destination Outposts buckets\.
+ Enable versioning on both of the buckets\.
+ Create an IAM role that gives S3 on Outposts permission to replicate objects\.
+ Add the replication configuration to the source Outposts bucket\.

To verify your setup, you test it\.

**To set up replication when the source and destination Outposts buckets are owned by the same AWS account**

1. Set a credentials profile for the AWS CLI\. In this example, we use the profile name `acctA`\. For information about setting credential profiles, see [Named profiles](https://docs.aws.amazon.com/cli/latest/userguide/cli-multiple-profiles.html) in the *AWS Command Line Interface User Guide*\. 
**Important**  
The profile that you use for this exercise must have the necessary permissions\. For example, in the replication configuration, you specify the IAM service role that S3 on Outposts can assume\. You can do this only if the profile that you use has the `iam:CreateRole` and `iam:PassRole` permissions\. For more information, see [Granting a user permissions to pass a role to an AWS service](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_passrole.html) in the *IAM User Guide*\. If you use administrator credentials to create a named profile, the named profile will have the necessary permission to perform all the tasks\. 

1. Create a source bucket and enable versioning on it\. The following `create-bucket` command creates a `SOURCE-OUTPOSTS-BUCKET` bucket in the US East \(N\. Virginia\) \(`us-east-1`\) Region\. To use this command, replace the `user input placeholders` with your own information\.

   ```
   aws s3control create-bucket --bucket SOURCE-OUTPOSTS-BUCKET --outpost-id SOURCE-OUTPOST-ID --profile acctA --region us-east-1
   ```

   The following `put-bucket-versioning` command enables versioning on the `SOURCE-OUTPOSTS-BUCKET` bucket\. To use this command, replace the `user input placeholders` with your own information\.

   ```
   aws s3control put-bucket-versioning --account-id 123456789012 --bucket arn:aws:s3-outposts:region:123456789012:outpost/SOURCE-OUTPOST-ID/bucket/SOURCE-OUTPOSTS-BUCKET --versioning-configuration Status=Enabled --profile acctA
   ```

1. Create a destination bucket and enable versioning on it\. The following `create-bucket` command creates a `DESTINATION-OUTPOSTS-BUCKET` bucket in the US West \(Oregon\) \(`us-west-2`\) Region\. To use this command, replace the `user input placeholders` with your own information\.
**Note**  
To set up a replication configuration when both the source and destination Outposts buckets are in the same AWS account, you use the same named profile\. This example uses `acctA`\. To test the replication configuration when the buckets are owned by different AWS accounts, you specify different profiles for each bucket\.

   ```
   aws s3control create-bucket --bucket DESTINATION-OUTPOSTS-BUCKET --create-bucket-configuration LocationConstraint=us-west-2 --outpost-id DESTINATION-OUTPOST-ID --profile acctA --region us-west-2                       
   ```

   The following `put-bucket-versioning` command enables versioning on the `DESTINATION-OUTPOSTS-BUCKET` bucket\. To use this command, replace the `user input placeholders` with your own information\.

   ```
   aws s3control put-bucket-versioning --account-id 123456789012 --bucket arn:aws:s3-outposts:region:123456789012:outpost/DESTINATION-OUTPOST-ID/bucket/DESTINATION-OUTPOSTS-BUCKET --versioning-configuration Status=Enabled --profile acctA
   ```

1. Create an IAM service role\. Later in the replication configuration, you add this service role to the `SOURCE-OUTPOSTS-BUCKET` bucket\. S3 on Outposts assumes this role to replicate objects on your behalf\. You create an IAM role in two steps:

   1. Create an IAM role\.

      1. Copy the following trust policy and save it to a file named `s3-on-outposts-role-trust-policy.json` in the current directory on your local computer\. This policy grants S3 on Outposts service principal permissions to assume the service role\.

         ```
         {
            "Version":"2012-10-17",
            "Statement":[
               {
                  "Effect":"Allow",
                  "Principal":{
                     "Service":"s3-outposts.amazonaws.com"
                  },
                  "Action":"sts:AssumeRole"
               }
            ]
         }
         ```

      1. Run the following command to create the role\. Replace the `user input placeholders` with your own information\.

         ```
         aws iam create-role --role-name replicationRole --assume-role-policy-document file://s3-on-outposts-role-trust-policy.json --profile acctA
         ```

   1. Attach a permissions policy to the service role\.

      1. Copy the following permissions policy and save it to a file named `s3-on-outposts-role-permissions-policy.json` in the current directory on your local computer\. This policy grants permissions for various S3 on Outposts bucket and object actions\. To use this policy, replace the `user input placeholders` with your own information\.

         ```
         {
            "Version":"2012-10-17",
            "Statement":[
               {
                  "Effect":"Allow",
                  "Action":[
                     "s3-outposts:GetObjectVersionForReplication",
                     "s3-outposts:GetObjectVersionTagging"
                  ],
                  "Resource":[
                     "arn:aws:s3-outposts:region:123456789012:outpost/SOURCE-OUTPOST-ID/bucket/SOURCE-OUTPOSTS-BUCKET/object/*",
                     "arn:aws:s3-outposts:region:123456789012:outpost/SOURCE-OUTPOST-ID/accesspoint/SOURCE-OUTPOSTS-BUCKET-ACCESS-POINT/object/*"
                  ]
               },
               {
                  "Effect":"Allow",
                  "Action":[
                     "s3-outposts:ReplicateObject",
                     "s3-outposts:ReplicateDelete"
                  ],
                  "Resource":[
                     "arn:aws:s3-outposts:region:123456789012:outpost/DESTINATION-OUTPOST-ID/bucket/DESTINATION-OUTPOSTS-BUCKET/object/*",
                     "arn:aws:s3-outposts:region:123456789012:outpost/DESTINATION-OUTPOST-ID/accesspoint/DESTINATION-OUTPOSTS-BUCKET-ACCESS-POINT/object/*"
                  ] 
               }
            ]
         }
         ```

      1. Run the following command to create a policy and attach it to the role\. Replace the `user input placeholders` with your own information\.

         ```
         aws iam put-role-policy --role-name replicationRole --policy-document file://s3-on-outposts-role-permissions-policy.json --policy-name replicationRolePolicy --profile acctA
         ```

1. Add a replication configuration to the `SOURCE-OUTPOSTS-BUCKET` bucket\. 

   1. Although the S3 on Outposts API requires a replication configuration in XML format, the AWS CLI requires that you specify the replication configuration in JSON format\. Save the following JSON in a file called `replication.json` to the local directory on your computer\. To use this configuration, replace the `user input placeholders` with your own information\.

      ```
      {
        "Role": "IAM-role-ARN",
        "Rules": [
          {
            "Status": "Enabled",
            "Priority": 1,
            "DeleteMarkerReplication": { "Status": "Disabled" },
            "Filter" : { "Prefix": "Tax"},
            "Destination": {
              "Bucket": 
              "arn:aws:s3-outposts:region:123456789012:outpost/DESTINATION-OUTPOST-ID/accesspoint/DESTINATION-OUTPOSTS-BUCKET-ACCESS-POINT"
            }
          }
        ]
      }
      ```

   1. Run the following `put-bucket-replication` command to add the replication configuration to your source Outposts bucket\. To use this command, replace the `user input placeholders` with your own information\.

      ```
      aws s3control put-bucket-replication --account-id 123456789012 --bucket arn:aws:s3-outposts:region:123456789012:outpost/SOURCE-OUTPOST-ID/bucket/SOURCE-OUTPOSTS-BUCKET --replication-configuration file://replication.json --profile acctA
      ```

   1. To retrieve the replication configuration, use the `get-bucket-replication` command\. To use this command, replace the `user input placeholders` with your own information\.

      ```
      aws s3control get-bucket-replication --account-id 123456789012 --bucket arn:aws:s3-outposts:region:123456789012:outpost/SOURCE-OUTPOST-ID/bucket/SOURCE-OUTPOSTS-BUCKET --profile acctA
      ```

1. Test the setup in the Amazon S3 console: 

   1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

   1. In the `SOURCE-OUTPOSTS-BUCKET` bucket, create a folder named `Tax`\. 

   1. Add sample objects to the `Tax` folder in the `SOURCE-OUTPOSTS-BUCKET` bucket\. 

   1. In the `DESTINATION-OUTPOSTS-BUCKET` bucket, verify the following:
      + S3 on Outposts replicated the objects\.
**Note**  
The amount of time that it takes for S3 on Outposts to replicate an object depends on the size of the object\. For information about how to see the status of replication, see [Getting replication status information](manage-outposts-replication.md#outposts-replication-status)\.
      + On the object's **Properties** tab, the **Replication status** is set to **Replica** \(identifying this as a replica object\)\.