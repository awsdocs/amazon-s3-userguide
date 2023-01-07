# Adding a bucket policy by using the Amazon S3 console<a name="add-bucket-policy"></a>

You can use the [AWS Policy Generator](http://aws.amazon.com/blogs/aws/aws-policy-generator/) and the Amazon S3 console to add a new bucket policy or edit an existing bucket policy\. A bucket policy is a resource\-based AWS Identity and Access Management \(IAM\) policy\. You add a bucket policy to a bucket to grant other AWS accounts or IAM users access permissions for the bucket and the objects in it\. Object permissions apply only to the objects that the bucket owner creates\. For more information about bucket policies, see [Overview of managing access](access-control-overview.md)\.

Make sure to resolve security warnings, errors, general warnings, and suggestions from AWS Identity and Access Management Access Analyzer before you save your policy\. IAM Access Analyzer runs policy checks to validate your policy against IAM [policy grammar](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_grammar.html) and [best practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)\. These checks generate findings and provide actionable recommendations to help you author policies that are functional and conform to security best practices\. To learn more about validating policies by using IAM Access Analyzer, see [IAM Access Analyzer policy validation](https://docs.aws.amazon.com/IAM/latest/UserGuide/access-analyzer-policy-validation.html) in the *IAM User Guide*\. To view a list of the warnings, errors, and suggestions that are returned by IAM Access Analyzer, see [IAM Access Analyzer policy check reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/access-analyzer-reference-policy-checks.html)\.

**To create or edit a bucket policy**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Buckets**\.

1. In the **Buckets** list, choose the name of the bucket that you want to create a bucket policy for or whose bucket policy you want to edit\.

1. Choose the **Permissions** tab\.

1. Under **Bucket policy**, choose **Edit**\. The **Edit bucket policy** page appears\.

1. On the **Edit bucket policy** page, do one of the following: 
   + To see examples of bucket policies in the *Amazon S3 User Guide*, choose **Policy examples**\.
   + To generate a policy automatically, or edit the JSON in the **Policy** section, choose **Policy generator**\.

   If you choose **Policy generator**, the AWS Policy Generator opens in a new window\.

   1. On the **AWS Policy Generator** page, for **Select Type of Policy**, choose **S3 Bucket Policy**\.

   1. Add a statement by entering the information in the provided fields, and then choose **Add Statement**\. Repeat this step for as many statements as you would like to add\. For more information about these fields, see the [IAM JSON policy elements reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html) in the *IAM User Guide*\. 
**Note**  
For your convenience, the **Edit bucket policy** page displays the **Bucket ARN **\(Amazon Resource Name\) of the current bucket above the **Policy** text field\. You can copy this ARN for use in the statements on the **AWS Policy Generator** page\.  

   1. After you finish adding statements, choose **Generate Policy**\.

   1. Copy the generated policy text, choose **Close**, and return to the **Edit bucket policy** page in the Amazon S3 console\.

1. In the **Policy** box, edit the existing policy or paste the bucket policy from the AWS Policy Generator\. Make sure to resolve security warnings, errors, general warnings, and suggestions before you save your policy\.
**Note**  
Bucket policies are limited to 20 KB in size\.

1. \(Optional\) Choose **Preview external access** in the lower\-right corner to preview how your new policy affects public and cross\-account access to your resource\. Before you save your policy, you can check whether it introduces new IAM Access Analyzer findings or resolves existing findings\. If you don’t see an active analyzer, choose **Go to Access Analyzer** to [ create an account analyzer](https://docs.aws.amazon.com/IAM/latest/UserGuide/access-analyzer-getting-started.html#access-analyzer-enabling) in IAM Access Analyzer\. For more information, see [Preview access](https://docs.aws.amazon.com/IAM/latest/UserGuide/access-analyzer-access-preview.html) in the *IAM User Guide*\. 

1. Choose **Save changes**, which returns you to the **Permissions** tab\. 