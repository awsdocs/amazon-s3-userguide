# Prerequisites for creating replication rules<a name="outposts-replication-prerequisites-config"></a>

**Topics**
+ [Connecting your source and destination Outpost subnets](#outposts-rep-preone)
+ [Creating an IAM role](#outposts-rep-pretwo)

## Connecting your source and destination Outpost subnets<a name="outposts-rep-preone"></a>

To have your replication traffic go from your source Outpost to your destination Outpost over your local gateway, you must add a new route to set up networking\. You must connect the Classless Inter\-Domain Routing \(CIDR\) networking ranges of your access points together\. For each pair of access points, you need to set up this connection only once\. 

Some steps to set up the connection are different, depending on the access type of your Outposts endpoints that are associated with your access points\. The access type for endpoints is either **Private** \(direct virtual private cloud \[VPC\] routing for AWS Outposts\) or **Customer owned IP** \(a customer\-owned IP address pool \[CoIP pool\] within your on\-premises network\)\.

### Step 1: Find the CIDR range of your source Outposts endpoint<a name="outposts-pre-step1"></a>

**To find the CIDR range of your source endpoint that's associated with your source access point**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Outposts buckets**\.

1. In the **Outposts buckets** list, choose the source bucket that you want for replication\.

1. Choose the **Outposts access points** tab, and choose the Outposts access point for the source bucket for your replication rule\.

1. Choose the Outposts endpoint\.

1. Copy the subnet ID for use in [Step 5](#outposts-pre-step5)\.

1. The method that you use to find the CIDR range of the source Outposts endpoint depends on the access type of your endpoint\.

   In the **Outposts endpoint overview** section, see the **Access Type**\.
   + If the access type is **Private**, copy the **Classless inter\-domain routing \(CIDR\)** value to use in [Step 6](#outposts-pre-step6)\.
   + If the access type is **Customer Owned IP**, do the following:

     1. Copy the **Customer owned IPv4 pool** value to use as the ID of the address pool later on\.

     1. Open the AWS Outposts console at [https://console\.aws\.amazon\.com/outposts/](https://console.aws.amazon.com/outposts/home)\.

     1. In the navigation pane, choose **Local gateway route tables**\.

     1. Choose the **Local gateway route table ID** value of your source Outpost\.

     1. In the details pane, choose the **CoIP pools** tab\. Paste the value of your CoIP pool ID that you copied previously in the search box\.

     1. For the matched CoIP pool, copy the corresponding **CIDRs** value of your source Outposts endpoint for use in [Step 6](#outposts-pre-step6)\.

### Step 2: Find the subnet ID and the CIDR range of your destination Outposts endpoint<a name="outposts-pre-step2"></a>

To find the subnet ID and the CIDR range of your destination endpoint that's associated with your destination access point, follow the same substeps in [Step 1](#outposts-pre-step1) and change your source Outposts endpoint to your destination Outposts endpoint when you apply those substeps\. Copy the subnet ID value of your destination Outposts endpoint for use in [Step 6](#outposts-pre-step6)\. Copy the CIDR value of your destination Outposts endpoint for use in [Step 5](#outposts-pre-step5)\. 

### Step 3: Find the local gateway ID of your source Outpost<a name="outposts-pre-step3"></a>

**To find the local gateway ID of your source Outpost**

1. Open the AWS Outposts console at [https://console\.aws\.amazon\.com/outposts/](https://console.aws.amazon.com/outposts/home)\.

1. In the left navigation pane, choose **Local gateways**\.

1. On the **Local gateways** page, find the Outpost ID of your source Outpost that you want to use for replication\. 

1. Copy the local gateway ID value of your source Outpost for use in [Step 5](#outposts-pre-step5)\.

For more information about local gateway, see [Local gateway](https://docs.aws.amazon.com/outposts/latest/userguide/outposts-local-gateways.html) in the *AWS Outposts User Guide*\.

### Step 4: Find the local gateway ID of your destination Outpost<a name="outposts-pre-step4"></a>

To find the local gateway ID of your destination Outpost, follow the same substeps in [Step 3](#outposts-pre-step3), except look for the Outpost ID for your destination Outpost\. Copy the local gateway ID value of your destination Outpost for use in [Step 6](#outposts-pre-step6)\. 

### Step 5: Set up the connection from your source Outpost subnet to your destination Outpost subnet<a name="outposts-pre-step5"></a>

**To connect from your source Outpost subnet to your destination Outpost subnet**

1. Sign in to the AWS Management Console and open the VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the left navigation pane, choose **Subnets**\.

1. In the search box, enter the subnet ID for your source Outposts endpoint that you found in [Step 1](#outposts-pre-step1)\. Choose the subnet with the matched subnet ID\. 

1. For the matched subnet item, choose the **Route table** value of this subnet\.

1. On the page with a selected route table, choose **Actions**, and then choose **Edit routes**\.

1. On the **Edit routes** page, choose **Add route**\.

1. Under **Destination**, enter the CIDR range of your destination Outposts endpoint that you found in [Step 2](#outposts-pre-step2)\.

1. Under **Target**, choose **Outpost Local Gateway**, and enter the local gateway ID of your source Outpost that you found in [Step 3](#outposts-pre-step3)\.

1. Choose **Save changes**\.

1. Make sure the **Status** for the route is **Active**\.

### Step 6: Set up the connection from your destination Outpost subnet to your source Outpost subnet<a name="outposts-pre-step6"></a>

1. Sign in to the AWS Management Console and open the VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the left navigation pane, choose **Subnets**\.

1. In the search box, enter the subnet ID for your destination Outposts endpoint that you found in [Step 2](#outposts-pre-step2)\. Choose the subnet with the matched subnet ID\. 

1. For the matched subnet item, choose the **Route table** value of this subnet\.

1. On the page with a selected route table, choose **Actions**, and then choose **Edit routes**\.

1. On the **Edit routes** page, choose **Add route**\.

1. Under **Destination**, enter the CIDR range of your source Outposts endpoint that you found in [Step 1](#outposts-pre-step1)\.

1. Under **Target**, choose **Outpost Local Gateway**, and enter the local gateway ID of your destination Outpost that you found in [Step 4](#outposts-pre-step4)\.

1. Choose **Save changes**\.

1. Make sure the **Status** for the route is **Active**\.

 After you connect the CIDR networking ranges of your source and destination access points, you must create an AWS Identity and Access Management \(IAM\) role\. 

## Creating an IAM role<a name="outposts-rep-pretwo"></a>

By default, all S3 on Outposts resources—buckets, objects, and related subresources—are private, and only the resource owner can access the resource\. S3 on Outposts needs permissions to read and replicate objects from the source Outposts bucket\. You grant these permissions by creating an IAM *service role* and specifying that role in your replication configuration\. 

This section explains the trust policy and minimum required permissions policy\. The example walkthroughs provide step\-by\-step instructions to create an IAM role\. For more information, see [Creating replication rules on Outposts](replication-between-outposts.md)\. For more information about IAM roles, see [IAM roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html) in the *IAM User Guide*\. 
+ The following example shows a *trust policy*, where you identify S3 on Outposts as the service principal that can assume the role\.

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
+ The following example shows an *access policy*, where you grant the role permissions to perform replication tasks on your behalf\. When S3 on Outposts assumes the role, it has the permissions that you specify in this policy\. To use this policy, replace the `user input placeholders` with your own information\. Make sure to replace them with the Outpost IDs of your source and destination Outposts and the bucket names and access point names of your source and destination Outposts buckets\.

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

  The access policy grants permissions for the following actions:
  + `s3-outposts:GetObjectVersionForReplication` – Permission for this action is granted on all objects to allow S3 on Outposts to get a specific object version that's associated with each object\. 

    
  + `s3-outposts:GetObjectVersionTagging` – Permission for this action on objects in the *`SOURCE-OUTPOSTS-BUCKET`* bucket \(the source bucket\) allows S3 on Outposts to read object tags for replication\. For more information, see [Adding tags for S3 on Outposts buckets](S3OutpostsBucketTags.md)\. If S3 on Outposts doesn't have this permission, it replicates the objects, but not the object tags\.
  + `s3-outposts:ReplicateObject` and `s3-outposts:ReplicateDelete` – Permissions for these actions on all objects in the *`DESTINATION-OUTPOSTS-BUCKET`* bucket \(the destination bucket\) allow S3 on Outposts to replicate objects or delete markers to the destination Outposts bucket\. For information about delete markers, see [How delete operations affect replication](replication-what-is-isnot-replicated.md#replication-delete-op)\. 
**Note**  
Permission for the `s3-outposts:ReplicateObject` action on the *`DESTINATION-OUTPOSTS-BUCKET`* bucket \(the destination bucket\) also allows replication of object tags\. Therefore, you don't need to explicitly grant permission for the `s3-outposts:ReplicateTags` action\.
For cross\-account replication, the owner of the destination Outposts bucket must update its bucket policy to grant permission for the `s3-outposts:ReplicateObject` action on the*`DESTINATION-OUTPOSTS-BUCKET`*\. The `s3-outposts:ReplicateObject` action allows S3 on Outposts to replicate objects and object tags to the destination Outposts bucket\.

  For a list of S3 on Outposts actions, see [Actions defined by S3 on Outposts](https://docs.aws.amazon.com/service-authorization/latest/reference/list_amazons3onoutposts.html#amazons3onoutposts-actions-as-permissions)\.
**Important**  
The AWS account that owns the IAM role must have permissions for the actions that it grants to the IAM role\.   
For example, suppose that the source Outposts bucket contains objects owned by another AWS account\. The owner of the objects must explicitly grant the AWS account that owns the IAM role the required permissions through the bucket policy and the access point policy\. Otherwise, S3 on Outposts can't access the objects, and replication of the objects fails\.   
The permissions described here are related to the minimum replication configuration\. If you choose to add optional replication configurations, you must grant additional permissions to S3 on Outposts\.

### Granting permissions when the source and destination Outposts buckets are owned by different AWS accounts<a name="outposts-rep-prethree"></a>

When the source and destination Outposts buckets aren't owned by the same accounts, the owner of the destination Outposts bucket must update the bucket and access point policies for the destination bucket\. These policies must grant the owner of the source Outposts bucket and the IAM service role permissions to perform replication actions, as shown in the following policy examples, or replication will fail\. In these policy examples, *`DESTINATION-OUTPOSTS-BUCKET`* is the destination bucket\. To use these policy examples, replace the `user input placeholders` with your own information\.

If you're creating the IAM service role manually, set the role path as `role/service-role/`, as shown in the following policy examples\. For more information, see [IAM ARNs](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_identifiers.html#identifiers-arns) in the *IAM User Guide*\. 

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
            "s3-outposts:ReplicateDelete",
            "s3-outposts:ReplicateObject"
         ],
         "Resource":[
            "arn:aws:s3-outposts:region:DestinationBucket-account-ID:outpost/DESTINATION-OUTPOST-ID/bucket/DESTINATION-OUTPOSTS-BUCKET/object/*"
         ]  
      }

   ]
}
```

```
{
"Version":"2012-10-17",
   "Id":"PolicyForDestinationAccessPoint",
   "Statement":[
      {
         "Sid":"Permissions on objects",
         "Effect":"Allow",
         "Principal":{
            "AWS":"arn:aws:iam::SourceBucket-account-ID:role/service-role/source-account-IAM-role"
         },
         "Action":[
            "s3-outposts:ReplicateDelete",
            "s3-outposts:ReplicateObject"
         ],
         "Resource" :[
            "arn:aws:s3-outposts:region:DestinationBucket-account-ID:outpost/DESTINATION-OUTPOST-ID/accesspoint/DESTINATION-OUTPOSTS-BUCKET-ACCESS-POINT/object/*"
         ]
      }
   ]              
}
```

**Note**  
If objects in the source Outposts bucket are tagged, note the following:  
If the source Outposts bucket owner grants S3 on Outposts permission for the `s3-outposts:GetObjectVersionTagging` and `s3-outposts:ReplicateTags` actions to replicate object tags \(through the IAM role\), Amazon S3 replicates the tags along with the objects\. For information about the IAM role, see [Creating an IAM role](#outposts-rep-pretwo)\. 