# Enabling S3 Object Lock using S3 Batch Operations<a name="batch-ops-object-lock"></a>

You can use S3 Batch Operations with S3 Object Lock to manage retention or enable a legal hold for many Amazon S3 objects at once\. You specify the list of target objects in your manifest and submit it to Batch Operations for completion\. For more information, see [S3 Object Lock retention](batch-ops-retention-date.md) and [S3 Object Lock legal hold](batch-ops-legal-hold.md)\. 

The following examples show how to create an IAM role with S3 Batch Operations permissions and update the role permissions to create jobs that enable Object Lock\. In the examples, replace any variable values with those that suit your needs\. You must also have a `CSV` manifest identifying the objects for your S3 Batch Operations job\. For more information, see [Specifying a manifest](batch-ops-create-job.md#specify-batchjob-manifest)\.

## Using the AWS CLI<a name="batchops-example-cli-object-lock"></a>

1. Create an IAM role and assign S3 Batch Operations permissions to run\.

   This step is required for all S3 Batch Operations jobs\.

   ```
   export AWS_PROFILE='aws-user'
   
   read -d '' bops_trust_policy <<EOF
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Principal": {
           "Service": [
             "batchoperations.s3.amazonaws.com"
           ]
         },
         "Action": "sts:AssumeRole"
       }
     ]
   }
   EOF
   aws iam create-role --role-name bops-objectlock --assume-role-policy-document "${bops_trust_policy}"
   ```

1. Set up S3 Batch Operations with S3 Object Lock to run\.

   In this step, you allow the role to do the following:

   1. Run Object Lock on the S3 bucket that contains the target objects that you want Batch Operations to run on\.

   1. Read the S3 bucket where the manifest CSV file and the objects are located\.

   1. Write the results of the S3 Batch Operations job to the reporting bucket\.

   ```
   read -d '' bops_permissions <<EOF
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Action": "s3:GetBucketObjectLockConfiguration",
               "Resource": [
                   "arn:aws:s3:::{{ManifestBucket}}"
               ]
           },
           {
               "Effect": "Allow",
               "Action": [
                   "s3:GetObject",
                   "s3:GetObjectVersion",
                   "s3:GetBucketLocation"
               ],
               "Resource": [
                   "arn:aws:s3:::{{ManifestBucket}}/*"
               ]
           },
           {
               "Effect": "Allow",
               "Action": [
                   "s3:PutObject",
                   "s3:GetBucketLocation"
               ],
               "Resource": [
                   "arn:aws:s3:::{{ReportBucket}}/*"
               ]
           }
       ]
   }
   EOF
   
   aws iam put-role-policy --role-name bops-objectlock --policy-name object-lock-permissions --policy-document "${bops_permissions}"
   ```

## Using the AWS SDK for Java<a name="batchops-examples-java-object-lock"></a>

The following examples show how to create an IAM role with S3 Batch Operations permissions, and update the role permissions to create jobs that enable object lock using the AWS SDK for Java\. In the code, replace any variable values with those that suit your needs\. You must also have a `CSV` manifest identifying the objects for your S3 Batch Operations job\. For more information, see [Specifying a manifest](batch-ops-create-job.md#specify-batchjob-manifest)\.

You perform the following steps:

1. Create an IAM role and assign S3 Batch Operations permissions to run\. This step is required for all S3 Batch Operations jobs\.

1. Set up S3 Batch Operations with S3 Object Lock to run\.

   You allow the role to do the following:

   1. Run Object Lock on the S3 bucket that contains the target objects that you want Batch Operations to run on\.

   1. Read the S3 bucket where the manifest CSV file and the objects are located\.

   1. Write the results of the S3 Batch Operations job to the reporting bucket\.

```
public void createObjectLockRole() {
    final String roleName = "bops-object-lock";

    final String trustPolicy = "{" +
            "  \"Version\": \"2012-10-17\", " +
            "  \"Statement\": [ " +
            "    { " +
            "      \"Effect\": \"Allow\", " +
            "      \"Principal\": { " +
            "        \"Service\": [" +
            "          \"batchoperations.s3.amazonaws.com\"" +
            "        ]" +
            "      }, " +
            "      \"Action\": \"sts:AssumeRole\" " +
            "    } " +
            "  ]" +
            "}";

    final String bopsPermissions = "{" +
            "    \"Version\": \"2012-10-17\"," +
            "    \"Statement\": [" +
            "        {" +
            "            \"Effect\": \"Allow\"," +
            "            \"Action\": \"s3:GetBucketObjectLockConfiguration\"," +
            "            \"Resource\": [" +
            "                \"arn:aws:s3:::ManifestBucket\"" +
            "            ]" +
            "        }," +
            "        {" +
            "            \"Effect\": \"Allow\"," +
            "            \"Action\": [" +
            "                \"s3:GetObject\"," +
            "                \"s3:GetObjectVersion\"," +
            "                \"s3:GetBucketLocation\"" +
            "            ]," +
            "            \"Resource\": [" +
            "                \"arn:aws:s3:::ManifestBucket/*\"" +
            "            ]" +
            "        }," +
            "        {" +
            "            \"Effect\": \"Allow\"," +
            "            \"Action\": [" +
            "                \"s3:PutObject\"," +
            "                \"s3:GetBucketLocation\"" +
            "            ]," +
            "            \"Resource\": [" +
            "                \"arn:aws:s3:::ReportBucket/*\"" +
            "            ]" +
            "        }" +
            "    ]" +
            "}";

    final AmazonIdentityManagement iam =
            AmazonIdentityManagementClientBuilder.defaultClient();

    final CreateRoleRequest createRoleRequest = new CreateRoleRequest()
            .withAssumeRolePolicyDocument(bopsPermissions)
            .withRoleName(roleName);

    final CreateRoleResult createRoleResult = iam.createRole(createRoleRequest);

    final PutRolePolicyRequest putRolePolicyRequest = new PutRolePolicyRequest()
            .withPolicyDocument(bopsPermissions)
            .withPolicyName("bops-permissions")
            .withRoleName(roleName);

    final PutRolePolicyResult putRolePolicyResult = iam.putRolePolicy(putRolePolicyRequest);
}
```