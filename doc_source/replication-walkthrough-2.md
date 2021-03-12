--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Configuring replication for source and destination buckets are owned by different accounts<a name="replication-walkthrough-2"></a>

Setting up replication when *source* and *destination* buckets are owned by different AWS accounts is similar to setting replication when both buckets are owned by the same account\. The only difference is that the *destination* bucket owner must grant the *source* bucket owner permission to replicate objects by adding a bucket policy\. 

**To configure replication when the source and destination buckets are owned by different AWS accounts**

1. In this example, you create *source* and *destination* buckets in two different AWS accounts\. You need to have two credential profiles set for the AWS CLI \(in this example, we use `acctA` and `acctB` for profile names\)\. For more information about setting credential profiles, see [Named Profiles](https://docs.aws.amazon.com/cli/latest/userguide/cli-multiple-profiles.html) in the *AWS Command Line Interface User Guide*\. 

1. Follow the step\-by\-step instructions in [Configuring for buckets in the same account](replication-walkthrough1.md) with the following changes:
   + For all AWS CLI commands related to *source* bucket activities \(for creating the *source* bucket, enabling versioning, and creating the IAM role\), use the `acctA` profile\. Use the `acctB` profile to create the *destination* bucket\. 
   + Make sure that the permissions policy specifies the *source* and *destination* buckets that you created for this example\.

1. In the console, add the following bucket policy on the *destination* bucket to allow the owner of the *source* bucket to replicate objects\. Be sure to edit the policy by providing the AWS account ID of the *source* bucket owner and the *destination* bucket name\.

   ```
   {
      "Version":"2008-10-17",
      "Id":"",
      "Statement":[
         {
            "Sid":"Set permissions for objects",
            "Effect":"Allow",
            "Principal":{
               "AWS":"arn:aws:iam::source-bucket-acct-ID:source-acct-IAM-role"
            },
            "Action":["s3:ReplicateObject", "s3:ReplicateDelete"],
            "Resource":"arn:aws:s3:::destination/*"
         },
         {
            "Sid":"Set permissions on bucket",
            "Effect":"Allow",
            "Principal":{
               "AWS":"arn:aws:iam::source-bucket-acct-ID:source-acct-IAM-role"
            },
            "Action":["s3:GetBucketVersioning", "s3:PutBucketVersioning"],
            "Resource":"arn:aws:s3:::destination"
         }
      ]
   }
   ```

Choose the bucket and add the bucket policy\. For instructions, see [Adding a bucket policy using the Amazon S3 console](add-bucket-policy.md)\.