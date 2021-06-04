# Using a CSV manifest stored in the source account to copy objects across AWS accounts<a name="specify-batchjob-manifest-xaccount-csv"></a>

You can use a CSV file that is stored in a different AWS account as a manifest for an S3 Batch Operations job\. For using an S3 Inventory Report, see [Using an inventory report delivered to the destination account to copy objects across AWS accounts](specify-batchjob-manifest-xaccount-inventory.md)\.

The following procedure shows how to set up permissions when using an S3 Batch Operations job to copy objects from a source account to a destination account with the CSV manifest file stored in the source account\.

**To set up a CSV manifest stored in a different AWS account**

1. Create a role in the destination account that is based on the S3 Batch Operations trust policy\. In this procedure, the *destination account* is the account that the objects are being copied to\.

   For more information about the trust policy, see [Trust policy](batch-ops-iam-role-policies.md#batch-ops-iam-role-policies-trust)\.

   For more information about creating a role, see [Creating a role to delegate permissions to an AWS service ](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-service.html) in the *IAM User Guide*\.

   If you create the role using the console, enter a name for the role \(the example role uses the name `BatchOperationsDestinationRoleCOPY`\)\. Choose the **S3** service, and then choose the **S3 bucket Batch Operations** use case, which applies the trust policy to the role\.

   Then choose **Create policy** to attach the following policy to the role\.

   ```
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Sid": "AllowBatchOperationsDestinationObjectCOPY",
         "Effect": "Allow",
         "Action": [
           "s3:PutObject",
           "s3:PutObjectVersionAcl",
           "s3:PutObjectAcl",
           "s3:PutObjectVersionTagging",
           "s3:PutObjectTagging",
           "s3:GetObject",
           "s3:GetObjectVersion",
           "s3:GetObjectAcl",
           "s3:GetObjectTagging",
           "s3:GetObjectVersionAcl",
           "s3:GetObjectVersionTagging"
         ],
         "Resource": [
           "arn:aws:s3:::ObjectDestinationBucket/*",
           "arn:aws:s3:::ObjectSourceBucket/*",
           "arn:aws:s3:::ObjectSourceManifestBucket/*"
         ]
       }
     ]
   }
   ```

   Using the policy, the role grants `batchoperations.s3.amazonaws.com` permission to read the manifest in the source manifest bucket\. It grants permissions to GET objects, ACLs, tags, and versions in the source object bucket\. It also grants permissions to PUT objects, ACLs, tags, and versions into the destination object bucket\.

1. In the source account, create a bucket policy for the bucket that contains the manifest to grant the role that you created in the previous step to GET objects and versions in the source manifest bucket\.

   This step allows S3 Batch Operations to read the manifest using the trusted role\. Apply the bucket policy to the bucket that contains the manifest\.

   The following is an example of the bucket policy to apply to the source manifest bucket\.

   ```
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Sid": "AllowBatchOperationsSourceManfiestRead",
         "Effect": "Allow",
         "Principal": {
           "AWS": [
             "arn:aws:iam::DestinationAccountNumber:user/ConsoleUserCreatingJob",
             "arn:aws:iam::DestinationAccountNumber:role/BatchOperationsDestinationRoleCOPY"
           ]
         },
         "Action": [
           "s3:GetObject",
           "s3:GetObjectVersion"
         ],
         "Resource": "arn:aws:s3:::ObjectSourceManifestBucket/*"
       }
     ]
   }
   ```

   This policy also grants permissions to allow a console user who is creating a job in the destination account the same permissions in the source manifest bucket through the same bucket policy\.

1. In the source account, create a bucket policy for the source bucket that grants the role you created to GET objects, ACLs, tags, and versions in the source object bucket\. S3 Batch Operations can then get objects from the source bucket through the trusted role\.

   The following is an example of the bucket policy for the bucket that contains the source objects\.

   ```
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Sid": "AllowBatchOperationsSourceObjectCOPY",
         "Effect": "Allow",
         "Principal": {
           "AWS": "arn:aws:iam::DestinationAccountNumber:role/BatchOperationsDestinationRoleCOPY"
         },
         "Action": [
           "s3:GetObject",
           "s3:GetObjectVersion",
           "s3:GetObjectAcl",
           "s3:GetObjectTagging",
           "s3:GetObjectVersionAcl",
           "s3:GetObjectVersionTagging"
         ],
         "Resource": "arn:aws:s3:::ObjectSourceBucket/*"
       }
     ]
   }
   ```

1. Create an S3 Batch Operations job in the destination account\. You need the Amazon Resource Name \(ARN\) for the role that you created in the destination account\.

   For general information about creating a job, see [Creating an S3 Batch Operations job](batch-ops-create-job.md)\.

   For information about creating a job using the console, see [Creating an S3 Batch Operations job](batch-ops-create-job.md)\.