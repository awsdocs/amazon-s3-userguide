# Creating an Amazon S3 on Outposts bucket with Amazon S3 console<a name="s3-outposts-create-bucket"></a>

With Amazon S3 on Outposts, you can create S3 buckets on your AWS Outposts and easily store and retrieve objects on\-premises for applications that require local data access, local data processing, and data residency\. S3 on Outposts provides a new storage class, `OUTPOSTS` which uses the S3 APIs, and is designed to durably and redundantly store data across multiple devices and servers on your AWS Outposts\. You communicate with your Outposts bucket using an access point and endpoint connection over a virtual private cloud \(VPC\)\. You can use the same APIs and features on Outposts buckets as you do on Amazon S3, such as access policies, encryption, and tagging\. You can use S3 on Outposts through the AWS Management Console, AWS CLI, AWS SDKs, or REST API\. For more information, see [Using Amazon S3 on Outposts](S3onOutposts.md)\.

Before you can upload data to S3 on Outposts, you must create an Outposts bucket in one of your AWS Outposts\. After you create a bucket, you can upload, download, and manage your data objects on the Outposts bucket\.

**Note**  
The AWS account that creates the bucket owns it and is the only one that can commit actions to it\. Buckets have configuration properties, including Outpost, tags, default encryption, access point settings including, VPC and an access point policy for accessing the objects in the bucket, and other metadata\. For more information see, [Amazon S3 on Outposts specifications](S3OnOutpostsRestrictionsLimitations.md#S3OnOutpostsSpecifications)\.  
The Amazon S3 console doesn’t support S3 on Outposts object actions\. For that you will need to use the REST API, AWS CLI, or AWS SDKs\.



**To create an Outposts bucket:**

1. Sign in to the [Amazon S3 console](https://console.aws.amazon.com/s3)\.

1. Choose **Outposts buckets** from the left navigation menu\.

1. Choose **Create Outposts** bucket\.

1. In **Bucket name**, enter a DNS\-compliant name for your bucket\.

   The bucket name must:
   + Bucket names must be unique within the account for this AWS Region and Outpost\.
   + Be unique across all of Amazon S3\.
   + Be between 3 and 63 characters long\.
   + Not contain uppercase characters\.
   + Start with a lowercase letter or number\.

     After you create the bucket, you can't change its name\. For information about naming buckets, see [Bucket naming rules](bucketnamingrules.md)\.
**Important**  
Avoid including sensitive information, such as account numbers, in the bucket name\. The bucket name is visible in the URLs that point to the objects in the bucket\.

1. In **Outpost**, choose the Outpost where you want the bucket to reside\. 

1. Add any **optional tags** that you would like to associate with Outposts Bucket to track the storage cost or other criteria for individual projects or groups of projects, label your S3 buckets using cost allocation tags\. For more information, see [Using cost allocation S3 bucket tags](CostAllocTagging.md)\.

1. All objects stored in your Outposts bucket are stored using server\-side encryption with Amazon S3 managed encryption keys \(SSE\-S3\) by default\. You can also explicitly choose to store objects using server\-side encryption with customer\-provided encryption keys \(SSE\-C\)\. For that you will need to use the REST API, AWS CLI, or AWS SDKs\.

1. In the **Outposts access point settings** section, enter the **access point name**\. S3 on Outposts access points simplify managing data access at scale for shared datasets in S3 on Outposts\. Access points are named network endpoints that are attached to Outposts buckets that you can use to perform S3 object operations\. For more information, see [API operations supported by Amazon S3 on Outposts Amazon S3 features not supported by Amazon S3 on Outposts ](S3OnOutpostsRestrictionsLimitations.md#S3OnOutpostsAPILimitations)\. 

   Access point names must be unique within the account for this Region and Outpost, and comply with the [Access points restrictions and limitations](access-points-restrictions-limitations.md)\.

1. Choose the **VPC** for this Amazon S3 on Outposts access point\. virtual private cloud \(VPC\) \(VPC\) enables you to launch AWS resources into a virtual network that you've defined\. This virtual network closely resembles a traditional network that you'd operate in your own data center, with the benefits of using the scalable infrastructure of AWS\. If you don’t have a VPC, choose the **Create VPC button**\. For more information, see [Creating access points restricted to a virtual private cloud](access-points-vpc.md)

1. Choose a **subnet** for your endpoint\. A *subnet* is a range of IP addresses in your VPC\. If you don't have the subnet you want, choose **Create subnet**\. For more information, see [Accessing S3 buckets On Outposts](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-security-groups.html)\. 

1. \(Optional\)Specify the **access point policy**\. The console automatically displays the **Amazon Resource Name \(ARN\)** for the access point, which you can use in the policy\.

1. Choose **Create Outposts bucket**\.
**Note**  
It can take up to 20 minutes for your Outposts endpoint to be created and your bucket to be ready to use\. To configure additional bucket settings choose **View details**\.