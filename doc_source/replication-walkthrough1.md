# Configuring replication for source and destination buckets owned by the same account<a name="replication-walkthrough1"></a>

Replication is the automatic, asynchronous copying of objects across buckets in the same or different AWS Regions\. Replication copies newly created objects and object updates from a source bucket to a destination bucket or buckets\. For more information, see [Replicating objects](replication.md)\.

When you configure replication, you add replication rules to the source bucket\. Replication rules define which source bucket objects to replicate and the destination bucket or buckets where the replicated objects are stored\. You can create a rule to replicate all the objects in a bucket or a subset of objects with a specific key name prefix, one or more object tags, or both\. A destination bucket can be in the same AWS account as the source bucket, or it can be in a different account\.

If you specify an object version ID to delete, Amazon S3 deletes that object version in the source bucket\. But it doesn't replicate the deletion in the destination bucket\. In other words, it doesn't delete the same object version from the destination bucket\. This protects data from malicious deletions\.

When you add a replication rule to a bucket, the rule is enabled by default, so it starts working as soon as you save it\. 

In this example, you set up replication for source and destination buckets that are owned by the same AWS account\. Examples are provided for using the Amazon S3 console, the AWS Command Line Interface \(AWS CLI\), and the AWS SDK for Java and AWS SDK for \.NET\.

## Using the S3 console<a name="enable-replication"></a>

Follow these steps to configure a replication rule when the destination bucket is in the same AWS account as the source bucket\.

If the destination bucket is in a different account from the source bucket, you must add a bucket policy to the destination bucket to grant the owner of the source bucket account permission to replicate objects in the destination bucket\. For more information, see [Granting permissions when the source and destination buckets are owned by different AWS accounts](setting-repl-config-perm-overview.md#setting-repl-config-crossacct)\.

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the name of the bucket that you want\.

1. Choose **Management**, scroll down to **Replication rules**, and then choose **Create replication rule**\.

    

1. Under **Rule name**, enter a name for your rule to help identify the rule later\. The name is required and must be unique within the bucket\.

1. Under **Status**, see that **Enabled** is selected by default\. An enabled rule starts to work as soon as you save it\. If you want to enable the rule later, select **Disabled**\.

1.  If the bucket has existing replication rules, you are instructed to set a priority for the rule\. You must set a priority for the rule to avoid conflicts caused by objects that are included in the scope of more than one rule\. In the case of overlapping rules, Amazon S3 uses the rule priority to determine which rule to apply\. The higher the number, the higher the priority\. For more information about rule priority, see [Replication configuration](replication-add-config.md)\.

1. In the **Replication rule configuration**, under **Source bucket**, you have the following options for setting the replication source:
   + To replicate the whole bucket, choose **Apply to all objects in the bucket**\. 
   + To replicate all objects that have the same prefix, choose **Limit the scope of this rule using one or more filters**\. This limits replication to all objects that have names that begin with the string \(for example `pictures`\)\. Enter a prefix in the box\. 

     If you enter a prefix that is the name of a folder, you must use **/** \(forward slash\) as the last character \(for example, `pictures/`\)\.

     To replicate all objects with one or more object tags, select **Add tag** and enter the key\-value pair in the boxes\. Repeat the procedure to add another tag\. You can combine a prefix and tags\. For more information about object tags, see [Categorizing your storage using tags](object-tagging.md)\.

   The new schema supports prefix and tag filtering and the prioritization of rules\. For more information about the new schema, see [Backward compatibility](replication-add-config.md#replication-backward-compat-considerations)\. For more information about the XML used with the Amazon S3 API that works behind the user interface, see [Replication configuration](replication-add-config.md)\. The new schema is described as *replication configuration XML V2*\.

1. Under **Destination**, select the bucket where you want Amazon S3 to replicate objects\.
**Note**  
The number of destination buckets is limited to the number of AWS Regions in a given partition\. A partition is a grouping of Regions\. AWS currently has three partitions: `aws` \(Standard Regions\), `aws-cn` \(China Regions\), and `aws-us-gov` \(AWS GovCloud \[US\] Regions\)\. You can use [service quotas](https://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html) to request an increase in your destination bucket limit\.
   + To replicate to a bucket or buckets in your account, select **Choose a bucket in this account**, and enter or browse for the destination bucket names\. 
   + To replicate to a bucket or buckets in a different AWS account, select **Choose a bucket in another account**, and enter the destination bucket account ID and name\. 

     If the destination is in a different account from the source bucket, you must add a bucket policy to the destination buckets to grant the owner of the source bucket account permission to replicate objects\. For more information, see [Granting permissions when the source and destination buckets are owned by different AWS accounts](setting-repl-config-perm-overview.md#setting-repl-config-crossacct)\.

     If you want to enable **Object Ownership** to help standardize ownership of new objects in the destination bucket, choose **Change object ownership to the destination bucket owner**\. For more information about this option, see [Controlling ownership of objects and disabling ACLs for your bucket](about-object-ownership.md)\.
**Note**  
If versioning is not enabled on the destination bucket, you get a warning that contains an **Enable versioning** button\. Choose this button to enable versioning on the bucket\.

1. Set up an AWS Identity and Access Management \(IAM\) role that Amazon S3 can assume to replicate objects on your behalf\.

   To set up an IAM role, on the **Replication rule configuration** section, under **IAM role**, do one of the following:
   + Under **Choose from existing IAM roles** you may choose to **Create new role** to have Amazon S3 create a new IAM role for you\. When you save the rule, a new policy is generated for the IAM role that matches the source and destination buckets that you choose\.
   + You may can choose to use an existing IAM role\. If you do, you must choose a role that grants Amazon S3 the necessary permissions for replication\. Replication fails if this role does not grant Amazon S3 sufficient permissions to follow your replication rule\.

     To choose an existing role, select the role from the dropdown menu or enter the IAM role ARN\. 
**Important**  
When you add a replication rule to a bucket, you must have the `iam:PassRole` permission to be able to pass the IAM role that grants Amazon S3 replication permissions\. For more information, see [Granting a user permissions to pass a role to an AWS service](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_passrole.html) in the *IAM User Guide*\.

1. To replicate objects in the source bucket that are encrypted with AWS Key Management Service \(AWS KMS\), under **Replication criteria**, select **Replicate objects encrypted with AWS KMS**\. Under **AWS KMS key for encrypting destination objects** are the source keys that you allow replication to use\. All source KMS keys are included by default\. You can choose to narrow the KMS key selection\. 

   Objects encrypted by AWS KMS keys that you do not select are not replicated\. A KMS key or a group of KMS keys is chosen for you, but you can choose the KMS keys if you want\. For information about using AWS KMS with replication, see [Replicating objects created with server\-side encryption \(SSE\) using KMS keys](replication-config-for-kms-objects.md)\.
**Important**  
When you replicate objects that are encrypted with AWS KMS, the AWS KMS request rate doubles in the source Region and increases in the destination Region by the same amount\. These increased call rates to AWS KMS are due to the way that data is re\-encrypted using the KMS key that you define for the replication destination Region\. AWS KMS has a request rate limit that is per calling account per Region\. For information about the limit defaults, see [AWS KMS Limits \- Requests per Second: Varies](https://docs.aws.amazon.com/kms/latest/developerguide/limits.html#requests-per-second) in the *AWS Key Management Service Developer Guide*\.   
If your current Amazon S3 PUT object request rate during replication is more than half the default AWS KMS rate limit for your account, we recommend that you request an increase to your AWS KMS request rate limit\. To request an increase, create a case in the AWS Support Center at [Contact Us](https://aws.amazon.com/contact-us/)\. For example, suppose that your current PUT object request rate is 1,000 requests per second and you use AWS KMS to encrypt your objects\. In this case, we recommend that you ask AWS Support to increase your AWS KMS rate limit to 2,500 requests per second, in both your source and destination Regions \(if different\), to ensure that there is no throttling by AWS KMS\.   
To see your PUT object request rate in the source bucket, view `PutRequests` in the Amazon CloudWatch request metrics for Amazon S3\. For information about viewing CloudWatch metrics, see [Using the S3 console](configure-request-metrics-bucket.md#configure-metrics)

   If you chose to replicate objects encrypted with AWS KMS, enter the Amazon Resource Name \(ARN\) of the AWS KMS key to use to encrypt the replicas in the destination bucket\. You can find the ARN for your KMS key in the IAM console, under **Encryption keys**\. Or, you can choose a KMS key name from the drop\-down list\.

   For more information about creating an AWS KMS key, see [Creating Keys](https://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html) in the *AWS Key Management Service Developer Guide*\.
**Important**  
The Amazon S3 console lists only 100 KMS keys per AWS Region\. If you have more than 100 KMS keys in the same Region, you can see only the first 100 KMS keys in the S3 console\. To use a KMS key that is not listed in the console, choose **Custom KMS ARN**, and enter the KMS key ARN\.

1. You have the following additional options while setting the Replication rule configuration:
   + If you want to replicate your data into a specific storage class in the destination, choose **Change the storage class for the replicated objects**\. Then choose the storage class that you want to use for the replicated objects in the destination\. If you don't choose this option, the storage class for replicated objects is the same class as the original objects\. 
   + If you want to enable S3 Replication Time Control \(S3 RTC\) in your replication configuration, select **S3 Replication Time Control**\. For more information about this option, see [Meeting compliance requirements using S3 Replication Time Control \(S3 RTC\)](replication-time-control.md)\.
**Note**  
When you use S3 RTC or S3 Replication metrics, additional fees apply\.
   + If you want to enable S3 Replication metrics in your replication configuration, select **Replication metrics and events**\. For more information see, [Monitoring progress with replication metrics and Amazon S3 event notifications](replication-metrics.md)\.
   + If you want to enable delete marker replication in your replication configuration, select **Delete marker replication**\. For more information see, [Replicating delete markers between buckets](delete-marker-replication.md)\.
   + If you want to enable Amazon S3 replica modification sync in your replication configuration, select **Replica modification sync**\. For more information see, [Replicating metadata changes with Amazon S3 replica modification sync](replication-for-metadata-changes.md)\.

1. To finish, choose **Save**\.

1. After you save your rule, you can edit, enable, disable, or delete your rule by selecting your rule and choosing **Edit rule**\. 

## Using the AWS CLI<a name="replication-ex1-cli"></a>

To use the AWS CLI to set up replication when the source and destination buckets are owned by the same AWS account, you do the following:
+ Create source and destination buckets
+ Enable versioning on the buckets
+ Create an IAM role that gives Amazon S3 permission to replicate objects
+ Add the replication configuration to the source bucket

To verify your setup, you test it\.

**To set up replication when source and destination buckets are owned by the same AWS account**

1. Set a credentials profile for the AWS CLI\. In this example, we use the profile name `acctA`\. For information about setting credential profiles, see [Named Profiles](https://docs.aws.amazon.com/cli/latest/userguide/cli-multiple-profiles.html) in the *AWS Command Line Interface User Guide*\. 
**Important**  
The profile you use for this exercise must have the necessary permissions\. For example, in the replication configuration, you specify the IAM role that Amazon S3 can assume\. You can do this only if the profile you use has the `iam:PassRole` permission\. For more information, see [Granting a User Permissions to Pass a Role to an AWS Service](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_passrole.html) in the *IAM User Guide*\. If you use administrator credentials to create a named profile, you can perform all the tasks\. 

1. Create a `source` bucket and enable versioning on it\. The following code creates a `source` bucket in the US East \(N\. Virginia\) \(us\-east\-1\) Region\.

   

   ```
   aws s3api create-bucket \
   --bucket source \
   --region us-east-1 \
   --profile acctA
   ```

   ```
   aws s3api put-bucket-versioning \
   --bucket source \
   --versioning-configuration Status=Enabled \
   --profile acctA
   ```

1. Create a `destination` bucket and enable versioning on it\. The following code creates a `destination` bucket in the US West \(Oregon\) \(us\-west\-2\) Region\. 
**Note**  
To set up replication configuration when both source and destination buckets are in the same AWS account, you use the same profile\. This example uses `acctA`\. To test replication configuration when the buckets are owned by different AWS accounts, you specify different profiles for each\. This example uses the `acctB` profile for the destination bucket\.

   

   ```
   aws s3api create-bucket \
   --bucket destination \
   --region us-west-2 \
   --create-bucket-configuration LocationConstraint=us-west-2 \
   --profile acctA
   ```

   ```
   aws s3api put-bucket-versioning \
   --bucket destination \
   --versioning-configuration Status=Enabled \
   --profile acctA
   ```

1. Create an IAM role\. You specify this role in the replication configuration that you add to the *source* bucket later\. Amazon S3 assumes this role to replicate objects on your behalf\. You create an IAM role in two steps:
   + Create a role\.
   + Attach a permissions policy to the role\.

   1. Create the IAM role\.

      1. Copy the following trust policy and save it to a file named `s3-role-trust-policy.json` in the current directory on your local computer\. This policy grants Amazon S3 service principal permissions to assume the role\.

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

      1. Run the following command to create a role\.

         ```
         $ aws iam create-role \
         --role-name replicationRole \
         --assume-role-policy-document file://s3-role-trust-policy.json  \
         --profile acctA
         ```

   1. Attach a permissions policy to the role\.

      1. Copy the following permissions policy and save it to a file named `s3-role-permissions-policy.json` in the current directory on your local computer\. This policy grants permissions for various Amazon S3 bucket and object actions\. 

         ```
         {
            "Version":"2012-10-17",
            "Statement":[
               {
                  "Effect":"Allow",
                  "Action":[
                     "s3:GetObjectVersionForReplication",
                     "s3:GetObjectVersionAcl",
                     "s3:GetObjectVersionTagging"
                  ],
                  "Resource":[
                     "arn:aws:s3:::source-bucket/*"
                  ]
               },
               {
                  "Effect":"Allow",
                  "Action":[
                     "s3:ListBucket",
                     "s3:GetReplicationConfiguration"
                  ],
                  "Resource":[
                     "arn:aws:s3:::source-bucket"
                  ]
               },
               {
                  "Effect":"Allow",
                  "Action":[
                     "s3:ReplicateObject",
                     "s3:ReplicateDelete",
                     "s3:ReplicateTags"
                  ],
                  "Resource":"arn:aws:s3:::destination-bucket/*"
               }
            ]
         }
         ```

      1. Run the following command to create a policy and attach it to the role\.

         ```
         $ aws iam put-role-policy \
         --role-name replicationRole \
         --policy-document file://s3-role-permissions-policy.json \
         --policy-name replicationRolePolicy \
         --profile acctA
         ```

1. Add replication configuration to the `source` bucket\. 

   1. Although the Amazon S3 API requires replication configuration as XML, the AWS CLI requires that you specify the replication configuration as JSON\. Save the following JSON in a file called `replication.json` to the local directory on your computer\.

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
              "Bucket": "arn:aws:s3:::destination-bucket"
            }
          }
        ]
      }
      ```

   1. Update the JSON by providing values for the `destination-bucket` and `IAM-role-ARN`\. Save the changes\.

   1. Run the following command to add the replication configuration to your source bucket\. Be sure to provide the `source` bucket name\.

      ```
      $ aws s3api put-bucket-replication \
      --replication-configuration file://replication.json \
      --bucket source \
      --profile acctA
      ```

   To retrieve the replication configuration, use the `get-bucket-replication` command\.

   ```
   $ aws s3api get-bucket-replication \
   --bucket source \
   --profile acctA
   ```

1. Test the setup in the Amazon S3 console: 

   1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

   1. In the `source` bucket, create a folder named `Tax`\. 

   1. Add sample objects to the `Tax` folder in the `source` bucket\. 
**Note**  
The amount of time it takes for Amazon S3 to replicate an object depends on the size of the object\. For information about how to see the status of replication, see [Getting replication status information](replication-status.md)\.

      In the `destination` bucket, verify the following:
      + That Amazon S3 replicated the objects\.
      + In object **properties**, that the **Replication Status** is set to `Replica` \(identifying this as a replica object\)\.
      + In object **properties**, that the permission section shows no permissions\. This means that the replica is still owned by the `source` bucket owner, and the `destination` bucket owner has no permission on the object replica\. You can add optional configuration to tell Amazon S3 to change the replica ownership\. For an example, see [Changing the replica owner when source and destination buckets are owned by different accounts](replication-walkthrough-3.md)\.   
![\[Screen shot of object properties showing the replication status and permissions.\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/crr-wt2-10.png)

       

## Using the AWS SDKs<a name="replication-ex1-sdk"></a>

Use the following code examples to add a replication configuration to a bucket with the AWS SDK for Java and AWS SDK for \.NET, respectively\.

------
#### [ Java ]

The following example adds a replication configuration to a bucket and then retrieves and verifies the configuration\. For instructions on creating and testing a working sample, see [Testing the Amazon S3 Java Code Examples](UsingTheMPJavaAPI.md#TestingJavaSamples)\. 

```
import com.amazonaws.AmazonServiceException;
import com.amazonaws.SdkClientException;
import com.amazonaws.auth.profile.ProfileCredentialsProvider;
import com.amazonaws.regions.Regions;
import com.amazonaws.services.identitymanagement.AmazonIdentityManagement;
import com.amazonaws.services.identitymanagement.AmazonIdentityManagementClientBuilder;
import com.amazonaws.services.identitymanagement.model.CreateRoleRequest;
import com.amazonaws.services.identitymanagement.model.PutRolePolicyRequest;
import com.amazonaws.services.s3.AmazonS3;
import com.amazonaws.services.s3.AmazonS3Client;
import com.amazonaws.services.s3.model.BucketReplicationConfiguration;
import com.amazonaws.services.s3.model.BucketVersioningConfiguration;
import com.amazonaws.services.s3.model.CreateBucketRequest;
import com.amazonaws.services.s3.model.DeleteMarkerReplication;
import com.amazonaws.services.s3.model.DeleteMarkerReplicationStatus;
import com.amazonaws.services.s3.model.ReplicationDestinationConfig;
import com.amazonaws.services.s3.model.ReplicationRule;
import com.amazonaws.services.s3.model.ReplicationRuleStatus;
import com.amazonaws.services.s3.model.SetBucketVersioningConfigurationRequest;
import com.amazonaws.services.s3.model.StorageClass;
import com.amazonaws.services.s3.model.replication.ReplicationFilter;
import com.amazonaws.services.s3.model.replication.ReplicationFilterPredicate;
import com.amazonaws.services.s3.model.replication.ReplicationPrefixPredicate;

import java.io.IOException;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class CrossRegionReplication {

    public static void main(String[] args) throws IOException {
        Regions clientRegion = Regions.DEFAULT_REGION;
        String accountId = "*** Account ID ***";
        String roleName = "*** Role name ***";
        String sourceBucketName = "*** Source bucket name ***";
        String destBucketName = "*** Destination bucket name ***";
        String prefix = "Tax/";

        String roleARN = String.format("arn:aws:iam::%s:%s", accountId, roleName);
        String destinationBucketARN = "arn:aws:s3:::" + destBucketName;

        AmazonS3 s3Client = AmazonS3Client.builder()
            .withCredentials(new ProfileCredentialsProvider())
            .withRegion(clientRegion)
            .build();

        createBucket(s3Client, clientRegion, sourceBucketName);
        createBucket(s3Client, clientRegion, destBucketName);
        assignRole(roleName, clientRegion, sourceBucketName, destBucketName);


        try {


            // Create the replication rule.
            List<ReplicationFilterPredicate> andOperands = new ArrayList<ReplicationFilterPredicate>();
            andOperands.add(new ReplicationPrefixPredicate(prefix));


            Map<String, ReplicationRule> replicationRules = new HashMap<String, ReplicationRule>();
            replicationRules.put("ReplicationRule1",
                new ReplicationRule()
                    .withPriority(0)
                    .withStatus(ReplicationRuleStatus.Enabled)
                    .withDeleteMarkerReplication(new DeleteMarkerReplication().withStatus(DeleteMarkerReplicationStatus.DISABLED))
                    .withFilter(new ReplicationFilter().withPredicate(new ReplicationPrefixPredicate(prefix)))
                    .withDestinationConfig(new ReplicationDestinationConfig()
                        .withBucketARN(destinationBucketARN)
                        .withStorageClass(StorageClass.Standard)));

            // Save the replication rule to the source bucket.
            s3Client.setBucketReplicationConfiguration(sourceBucketName,
                new BucketReplicationConfiguration()
                    .withRoleARN(roleARN)
                    .withRules(replicationRules));

            // Retrieve the replication configuration and verify that the configuration
            // matches the rule we just set.
            BucketReplicationConfiguration replicationConfig = s3Client.getBucketReplicationConfiguration(sourceBucketName);
            ReplicationRule rule = replicationConfig.getRule("ReplicationRule1");
            System.out.println("Retrieved destination bucket ARN: " + rule.getDestinationConfig().getBucketARN());
            System.out.println("Retrieved priority: " + rule.getPriority());
            System.out.println("Retrieved source-bucket replication rule status: " + rule.getStatus());
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

    private static void createBucket(AmazonS3 s3Client, Regions region, String bucketName) {
        CreateBucketRequest request = new CreateBucketRequest(bucketName, region.getName());
        s3Client.createBucket(request);
        BucketVersioningConfiguration configuration = new BucketVersioningConfiguration().withStatus(BucketVersioningConfiguration.ENABLED);

        SetBucketVersioningConfigurationRequest enableVersioningRequest = new SetBucketVersioningConfigurationRequest(bucketName, configuration);
        s3Client.setBucketVersioningConfiguration(enableVersioningRequest);


    }

    private static void assignRole(String roleName, Regions region, String sourceBucket, String destinationBucket) {
        AmazonIdentityManagement iamClient = AmazonIdentityManagementClientBuilder.standard()
            .withRegion(region)
            .withCredentials(new ProfileCredentialsProvider())
            .build();
        StringBuilder trustPolicy = new StringBuilder();
        trustPolicy.append("{\\r\\n   ");
        trustPolicy.append("\\\"Version\\\":\\\"2012-10-17\\\",\\r\\n   ");
        trustPolicy.append("\\\"Statement\\\":[\\r\\n      {\\r\\n         ");
        trustPolicy.append("\\\"Effect\\\":\\\"Allow\\\",\\r\\n         \\\"Principal\\\":{\\r\\n            ");
        trustPolicy.append("\\\"Service\\\":\\\"s3.amazonaws.com\\\"\\r\\n         },\\r\\n         ");
        trustPolicy.append("\\\"Action\\\":\\\"sts:AssumeRole\\\"\\r\\n      }\\r\\n   ]\\r\\n}");

        CreateRoleRequest createRoleRequest = new CreateRoleRequest()
            .withRoleName(roleName)
            .withAssumeRolePolicyDocument(trustPolicy.toString());

        iamClient.createRole(createRoleRequest);

        StringBuilder permissionPolicy = new StringBuilder();
        permissionPolicy.append("{\\r\\n   \\\"Version\\\":\\\"2012-10-17\\\",\\r\\n   \\\"Statement\\\":[\\r\\n      {\\r\\n         ");
        permissionPolicy.append("\\\"Effect\\\":\\\"Allow\\\",\\r\\n         \\\"Action\\\":[\\r\\n             ");
        permissionPolicy.append("\\\"s3:GetObjectVersionForReplication\\\",\\r\\n            ");
        permissionPolicy.append("\\\"s3:GetObjectVersionAcl\\\"\\r\\n         ],\\r\\n         \\\"Resource\\\":[\\r\\n            ");
        permissionPolicy.append("\\\"arn:aws:s3:::");
        permissionPolicy.append(sourceBucket);
        permissionPolicy.append("/*\\\"\\r\\n         ]\\r\\n      },\\r\\n      {\\r\\n         ");
        permissionPolicy.append("\\\"Effect\\\":\\\"Allow\\\",\\r\\n         \\\"Action\\\":[\\r\\n            ");
        permissionPolicy.append("\\\"s3:ListBucket\\\",\\r\\n            \\\"s3:GetReplicationConfiguration\\\"\\r\\n         ");
        permissionPolicy.append("],\\r\\n         \\\"Resource\\\":[\\r\\n            \\\"arn:aws:s3:::");
        permissionPolicy.append(sourceBucket);
        permissionPolicy.append("\\r\\n         ");
        permissionPolicy.append("]\\r\\n      },\\r\\n      {\\r\\n         \\\"Effect\\\":\\\"Allow\\\",\\r\\n         ");
        permissionPolicy.append("\\\"Action\\\":[\\r\\n            \\\"s3:ReplicateObject\\\",\\r\\n            ");
        permissionPolicy.append("\\\"s3:ReplicateDelete\\\",\\r\\n            \\\"s3:ReplicateTags\\\",\\r\\n            ");
        permissionPolicy.append("\\\"s3:GetObjectVersionTagging\\\"\\r\\n\\r\\n         ],\\r\\n         ");
        permissionPolicy.append("\\\"Resource\\\":\\\"arn:aws:s3:::");
        permissionPolicy.append(destinationBucket);
        permissionPolicy.append("/*\\\"\\r\\n      }\\r\\n   ]\\r\\n}");

        PutRolePolicyRequest putRolePolicyRequest = new PutRolePolicyRequest()
            .withRoleName(roleName)
            .withPolicyDocument(permissionPolicy.toString())
            .withPolicyName("crrRolePolicy");

        iamClient.putRolePolicy(putRolePolicyRequest);


    }
}
```

------
#### [ C\# ]

The following AWS SDK for \.NET code example adds a replication configuration to a bucket and then retrieves it\. To use this code, provide the names for your buckets and the Amazon Resource Name \(ARN\) for your IAM role\. For instructions on how to create and test a working sample, see [Running the Amazon S3 \.NET Code Examples](UsingTheMPDotNetAPI.md#TestingDotNetApiSamples)\.

```
using Amazon;
using Amazon.S3;
using Amazon.S3.Model;
using System;
using System.Threading.Tasks;

namespace Amazon.DocSamples.S3
{
    class CrossRegionReplicationTest
    {
        private const string sourceBucket = "*** source bucket ***";
        // Bucket ARN example - arn:aws:s3:::destinationbucket
        private const string destinationBucketArn = "*** destination bucket ARN ***";
        private const string roleArn = "*** IAM Role ARN ***";
        // Specify your bucket region (an example region is shown).
        private static readonly RegionEndpoint sourceBucketRegion = RegionEndpoint.USWest2;
        private static IAmazonS3 s3Client;
        public static void Main()
        {
            s3Client = new AmazonS3Client(sourceBucketRegion);
            EnableReplicationAsync().Wait();
        }
        static async Task EnableReplicationAsync()
        {
            try
            {
                ReplicationConfiguration replConfig = new ReplicationConfiguration
                {
                    Role = roleArn,
                    Rules =
                        {
                            new ReplicationRule
                            {
                                Prefix = "Tax",
                                Status = ReplicationRuleStatus.Enabled,
                                Destination = new ReplicationDestination
                                {
                                    BucketArn = destinationBucketArn
                                }
                            }
                        }
                };

                PutBucketReplicationRequest putRequest = new PutBucketReplicationRequest
                {
                    BucketName = sourceBucket,
                    Configuration = replConfig
                };

                PutBucketReplicationResponse putResponse = await s3Client.PutBucketReplicationAsync(putRequest);

                // Verify configuration by retrieving it.
                await RetrieveReplicationConfigurationAsync(s3Client);
            }
            catch (AmazonS3Exception e)
            {
                Console.WriteLine("Error encountered on server. Message:'{0}' when writing an object", e.Message);
            }
            catch (Exception e)
            {
                Console.WriteLine("Unknown encountered on server. Message:'{0}' when writing an object", e.Message);
            }
        }
        private static async Task RetrieveReplicationConfigurationAsync(IAmazonS3 client)
        {
            // Retrieve the configuration.
            GetBucketReplicationRequest getRequest = new GetBucketReplicationRequest
            {
                BucketName = sourceBucket
            };
            GetBucketReplicationResponse getResponse = await client.GetBucketReplicationAsync(getRequest);
            // Print.
            Console.WriteLine("Printing replication configuration information...");
            Console.WriteLine("Role ARN: {0}", getResponse.Configuration.Role);
            foreach (var rule in getResponse.Configuration.Rules)
            {
                Console.WriteLine("ID: {0}", rule.Id);
                Console.WriteLine("Prefix: {0}", rule.Prefix);
                Console.WriteLine("Status: {0}", rule.Status);
            }
        }
    }
}
```

------