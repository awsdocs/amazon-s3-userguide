# Configuring IAM policies for Batch Replication<a name="s3-batch-replication-policies"></a>

Because S3 Batch Replication is a type of Batch Operations job, you must create a Batch Operations AWS Identity and Access Management \(IAM\) role to grant Amazon S3 permissions to perform actions on your behalf\. You also must attach a Batch Replication IAM policy to the Batch Operations IAM role\. The following example creates an IAM role that gives Batch Operations permission to initiate a Batch Replication job\.

**Create IAM role and policy**  


1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. Under **Access management**, choose **Roles**\.

1. Choose **Create Role**\.

1. Choose **AWS service** as the type of trusted entity, **Amazon S3** as the service, and **S3 Batch Operations** as the use case\.

1. Choose **Next: Permissions**\.

1. Choose **Create Policy**\.

1. Choose **JSON** and insert one of the following policies based on your manifest\.
**Note**  
Different permission are needed if you are generating a manifest or supplying one\. For more information see, [Specifying a manifest for a Batch Replication job](s3-batch-replication-batch.md#batch-replication-manifest)\.

**Policy if using and storing a S3 generated manifest**

   ```
   {
      "Version":"2012-10-17",
      "Statement":[
         {
            "Action":[
               "s3:InitiateReplication"
            ],
            "Effect":"Allow",
            "Resource":[
               "arn:aws:s3:::*** replication source bucket ***/*"
            ]
         },
         {
            "Action":[
               "s3:GetReplicationConfiguration",
               "s3:PutInventoryConfiguration"
            ],
            "Effect":"Allow",
            "Resource":[
               "arn:aws:s3:::*** replication source bucket ***"
            ]
         },
         {
            "Action":[
               "s3:GetObject",
               "s3:GetObjectVersion"
            ],
            "Effect":"Allow",
            "Resource":[
               "arn:aws:s3:::*** manifest bucket ***/*"
            ]
         },
         {
            "Effect":"Allow",
            "Action":[
               "s3:PutObject"
            ],
            "Resource":[
               "arn:aws:s3:::*** completion report bucket ****/*",
               "arn:aws:s3:::*** manifest bucket ****/*"    
            ]
         }
      ]
   }
   ```

**Policy if using a user supplied manifest**

   ```
   {
      "Version":"2012-10-17",
      "Statement":[
         {
            "Action":[
               "s3:InitiateReplication"
            ],
            "Effect":"Allow",
            "Resource":[
               "arn:aws:s3:::*** replication source bucket ***/*"
            ]
         },
               {
            "Action":[
               "s3:GetObject",
               "s3:GetObjectVersion"
            ],
            "Effect":"Allow",
            "Resource":[
               "arn:aws:s3:::*** manifest bucket ***/*"
            ]
         },
         {
            "Effect":"Allow",
            "Action":[
               "s3:PutObject"
            ],
            "Resource":[
               "arn:aws:s3:::*** completion report bucket ****/*"    
            ]
         }
      ]
   }
   ```

1. Choose **Next: Tags**\.

1. Choose **Next: Review**\.

1. Choose a name for the policy and choose **Create policy**\.

1. Attach this policy to your role and choose **Next: Tags**\.

1. Choose **Next: Review**\.

1. Choose a name for the role and choose **Create role**\.

**Verify trust policy**  


1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. Under **Access management**, choose **Roles**, and select your newly created role\.

1. Under **Trust relationships** tab, choose **Edit trust relationship**\.

1. Verify this role is using the following trust policy:

   ```
   {
      "Version":"2012-10-17",
      "Statement":[
         {
            "Effect":"Allow",
            "Principal":{
               "Service":"batchoperations.s3.amazonaws.com"
            },
            "Action":"sts:AssumeRole"
         }
      ]
   }
   ```