# Example 3: Bucket owner granting permissions to objects it does not own<a name="example-walkthroughs-managing-access-example3"></a>

**Topics**
+ [Step 0: Preparing for the walkthrough](#access-policies-walkthrough-cross-account-acl-step0)
+ [Step 1: Do the Account A tasks](#access-policies-walkthrough-cross-account-acl-acctA-tasks)
+ [Step 2: Do the Account B tasks](#access-policies-walkthrough-cross-account-acl-acctB-tasks)
+ [Step 3: Test permissions](#access-policies-walkthrough-cross-account-acl-verify)
+ [Step 4: Clean up](#access-policies-walkthrough-cross-account-acl-cleanup)

The scenario for this example is that a bucket owner wants to grant permission to access objects, but not all objects in the bucket are owned by the bucket owner\. For this example, the bucket owner is trying to grant permission to users in its own account\.

A bucket owner can enable other AWS accounts to upload objects\. By default, the bucket owner doesn't own objects written to a bucket by another AWS account\. Objects are owned by the accounts that write them to an S3 bucket\. If the bucket owner doesn't own objects in the bucket, the object owner must first grant permission to the bucket owner using an object ACL\. Then, the bucket owner can grant permissions to an object that they do not own, For more information, see [Amazon S3 bucket and object ownership](access-control-overview.md#about-resource-owner)\.

If the bucket owner applies the bucket owner enforced setting for S3 Object Ownership for the bucket, the bucket owner will own all objects in the bucket, including objects written by another AWS account\. This will resolve the issue that objects are not owned by the bucket owner\. Then, you can delegate permission to users in your own account or to other AWS accounts\.

**Note**  
By default, when another AWS account uploads an object to your S3 bucket, that account \(the object writer\) owns the object, has access to it, and can grant other users access to it through ACLs\. You can use Object Ownership to change this default behavior so that ACLs are disabled and you, as the bucket owner, automatically own every object in your bucket\. As a result, access control for your data is based on policies, such as IAM policies, S3 bucket policies, virtual private cloud \(VPC\) endpoint policies, and AWS Organizations service control policies \(SCPs\)\.  
A majority of modern use cases in Amazon S3 no longer require the use of ACLs, and we recommend that you disable ACLs except in unusual circumstances where you need to control access for each object individually\. With Object Ownership, you can disable ACLs and rely on policies for access control\. When you disable ACLs, you can easily maintain a bucket with objects uploaded by different AWS accounts\. You, as the bucket owner, own all the objects in the bucket and can manage access to them using policies\. For more information, see [Controlling ownership of objects and disabling ACLs for your bucket](about-object-ownership.md)\.

In this example, we assume the bucket owner has not applied the bucket owner enforced setting for Object Ownership\. The bucket owner delegates permission to users in its own account\. The following is a summary of the walkthrough steps:

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/access-policy-ex3.png)

1. Account A administrator user attaches a bucket policy with two statements\.
   + Allow cross\-account permission to Account B to upload objects\.
   + Allow a user in its own account to access objects in the bucket\.

1. Account B administrator user uploads objects to the bucket owned by Account A\.

1. Account B administrator updates the object ACL adding grant that gives the bucket owner full\-control permission on the object\.

1. User in Account A verifies by accessing objects in the bucket, regardless of who owns them\.

For this example, you need two accounts\. The following table shows how we refer to these accounts and the administrator users in these accounts\. In this walkthrough, you don't use the account root credentials, according to the recommended IAM guidelines\. For more information, see [About using an administrator user to create resources and grant permissions](example-walkthroughs-managing-access.md#about-using-root-credentials)\. Instead, you create an administrator in each account and use those credentials in creating resources and granting them permissions\.


| AWS account ID | Account referred to as | Administrator in the account  | 
| --- | --- | --- | 
|  *1111\-1111\-1111*  |  Account A  |  AccountAadmin  | 
|  *2222\-2222\-2222*  |  Account B  |  AccountBadmin  | 

All the tasks of creating users and granting permissions are done in the AWS Management Console\. To verify permissions, the walkthrough uses the command line tools, AWS Command Line Interface \(AWS CLI\) and AWS Tools for Windows PowerShell, so you don't need to write any code\. 

## Step 0: Preparing for the walkthrough<a name="access-policies-walkthrough-cross-account-acl-step0"></a>

1. Make sure that you have two AWS accounts and each account has one administrator as shown in the table in the preceding section\.

   1. Sign up for an AWS account, if needed\. 

      1. Open the [Amazon S3 page](https://aws.amazon.com/s3/) and choose **Create an AWS Account**\. 

      1. Follow the on\-screen instructions\. AWS will notify you by email when your account is active and available for you to use\.

   1. Using Account A credentials, sign in to the [IAM console](https://console.aws.amazon.com/iam/home?#home) and do the following to create an administrator user:
      + Create user *AccountAadmin* and note down security credentials\. For more information about adding users, see [Creating an IAM user in your AWS account](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html) in the *IAM User Guide*\. 
      + Grant AccountAadmin administrator permissions by attaching a user policy giving full access\. For instructions, see [Managing IAM policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_manage.html) in the *IAM User Guide*\. 
      + In the IAM console **Dashboard**, note down the** IAM User Sign\-In URL**\. Users in this account must use this URL when signing in to the AWS Management Console\. For more information, see [How users sign in to your account](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_how-users-sign-in.html) in *IAM User Guide*\. 

   1. Repeat the preceding step using Account B credentials and create administrator user *AccountBadmin*\.

1. Set up either the AWS CLI or the Tools for Windows PowerShell\. Make sure that you save the administrator credentials as follows:
   + If using the AWS CLI, create two profiles, `AccountAadmin` and `AccountBadmin`, in the config file\.
   + If using the Tools for Windows PowerShell, make sure that you store credentials for the session as `AccountAadmin` and `AccountBadmin`\.

   For instructions, see [Setting up the tools for the example walkthroughs](policy-eval-walkthrough-download-awscli.md)\. 

## Step 1: Do the Account A tasks<a name="access-policies-walkthrough-cross-account-acl-acctA-tasks"></a>

Perform the following steps for Account A:

### Step 1\.1: Sign in to the console<a name="access-policies-walkthrough-cross-account-permissions-acctA-tasks-sign-in-example3"></a>

Using the IAM user sign\-in URL for Account A, sign in to the AWS Management Console as AccountAadmin user\. This user will create a bucket and attach a policy to it\. 

### Step 1\.2: Create a bucket and user, and add a bucket policy to grant user permissions<a name="access-policies-walkthrough-cross-account-acl-create-bucket"></a>

1. In the Amazon S3 console, create a bucket\. This exercise assumes that the bucket is created in the US East \(N\. Virginia\) Region, and the name is `DOC-EXAMPLE-BUCKET1`\.

   For instructions, see [Creating a bucket](create-bucket-overview.md)\. 

1. In the IAM console, create a user *Dave*\. 

   For instructions, see [Creating IAM users \(console\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html#id_users_create_console) in the *IAM User Guide*\. 

1. Note down the Dave credentials\. 

1. In the Amazon S3 console, attach the following bucket policy to `DOC-EXAMPLE-BUCKET1` bucket\. For instructions, see [Adding a bucket policy using the Amazon S3 console](add-bucket-policy.md)\. Follow the steps to add a bucket policy\. For information about how to find account IDs, see [Finding your AWS account ID](https://docs.aws.amazon.com/general/latest/gr/acct-identifiers.html#FindingYourAccountIdentifiers)\. 

   The policy grants Account B the `s3:PutObject` and `s3:ListBucket` permissions\. The policy also grants user Dave the `s3:GetObject` permission\. 

   ```
   {
      "Version": "2012-10-17",
      "Statement": [
         {
            "Sid": "Statement1",
            "Effect": "Allow",
            "Principal": {
               "AWS": "arn:aws:iam::AccountB-ID:root"
            },
            "Action": [
               "s3:PutObject",
               "s3:ListBucket"
            ],
            "Resource": [
               "arn:aws:s3:::DOC-EXAMPLE-BUCKET1/*",
               "arn:aws:s3:::DOC-EXAMPLE-BUCKET1"
            ]
         },
         {
            "Sid": "Statement3",
            "Effect": "Allow",
            "Principal": {
               "AWS": "arn:aws:iam::AccountA-ID:user/Dave"
            },
            "Action": [
               "s3:GetObject"
            ],
            "Resource": [
               "arn:aws:s3:::DOC-EXAMPLE-BUCKET1/*"
            ]
         }  
      ]
   }
   ```

## Step 2: Do the Account B tasks<a name="access-policies-walkthrough-cross-account-acl-acctB-tasks"></a>

Now that Account B has permissions to perform operations on Account A's bucket, the Account B administrator will do the following:
+ Upload an object to Account A's bucket\. 
+ Add a grant in the object ACL to allow Account A, the bucket owner, full control\. 

**Using the AWS CLI**

1. Using the `put-object` CLI command, upload an object\. The \-`-body` parameter in the command identifies the source file to upload\. For example, if the file is on `C:` drive of a Windows machine, you would specify `c:\HappyFace.jpg`\. The `--key` parameter provides the key name for the object\. 

   ```
   aws s3api put-object --bucket DOC-EXAMPLE-BUCKET1 --key HappyFace.jpg --body HappyFace.jpg --profile AccountBadmin
   ```

1. Add a grant to the object ACL to allow the bucket owner full control of the object\. For information about how to find a canonical user ID, see [Finding the canonical user ID for your AWS account](finding-canonical-user-id.md)\.

   ```
   aws s3api put-object-acl --bucket DOC-EXAMPLE-BUCKET1 --key HappyFace.jpg --grant-full-control id="AccountA-CanonicalUserID" --profile AccountBadmin
   ```

**Using the Tools for Windows PowerShell**

1. Using the `Write-S3Object` Tools for Windows PowerShellcommand, upload an object\. 

   ```
   Write-S3Object -BucketName DOC-EXAMPLE-BUCKET1 -key HappyFace.jpg -file HappyFace.jpg -StoredCredentials AccountBadmin
   ```

1. Add a grant to the object ACL to allow the bucket owner full control of the object\.

   ```
   Set-S3ACL -BucketName DOC-EXAMPLE-BUCKET1 -Key HappyFace.jpg -CannedACLName "bucket-owner-full-control" -StoredCreden
   ```

## Step 3: Test permissions<a name="access-policies-walkthrough-cross-account-acl-verify"></a>

Now verify that user Dave in Account A can access the object owned by Account B\.

**Using the AWS CLI**

1. Add user Dave credentials to the AWS CLI config file and create a new profile, `UserDaveAccountA`\. For more information, see [Setting up the tools for the example walkthroughs](policy-eval-walkthrough-download-awscli.md)\.

   ```
   [profile UserDaveAccountA]
   aws_access_key_id = access-key
   aws_secret_access_key = secret-access-key
   region = us-east-1
   ```

1. Run the `get-object` CLI command to download `HappyFace.jpg` and save it locally\. You provide user Dave credentials by adding the `--profile` parameter\.

   ```
   aws s3api get-object --bucket DOC-EXAMPLE-BUCKET1 --key HappyFace.jpg Outputfile.jpg --profile UserDaveAccountA
   ```

**Using the Tools for Windows PowerShell**

1. Store user Dave AWS credentials, as `UserDaveAccountA`, to persistent store\. 

   ```
   Set-AWSCredentials -AccessKey UserDave-AccessKey -SecretKey UserDave-SecretAccessKey -storeas UserDaveAccountA
   ```

1. Run the `Read-S3Object` command to download the `HappyFace.jpg` object and save it locally\. You provide user Dave credentials by adding the `-StoredCredentials` parameter\. 

   ```
   Read-S3Object -BucketName DOC-EXAMPLE-BUCKET1 -Key HappyFace.jpg -file HappyFace.jpg  -StoredCredentials UserDaveAccountA
   ```

## Step 4: Clean up<a name="access-policies-walkthrough-cross-account-acl-cleanup"></a>

1. After you are done testing, you can do the following to clean up\.

   1. Sign in to the [AWS Management Console](https://console.aws.amazon.com/) using Account A credentials, and do the following:
     + In the Amazon S3 console, remove the bucket policy attached to *DOC\-EXAMPLE\-BUCKET1*\. In the bucket **Properties**, delete the policy in the **Permissions** section\. 
     + If the bucket is created for this exercise, in the Amazon S3 console, delete the objects and then delete the bucket\. 
     + In the IAM console, remove the **AccountAadmin** user\.

1. Sign in to the [AWS Management Console](https://console.aws.amazon.com/) using Account B credentials\. In the IAM console, delete user **AccountBadmin**\.