# Adding a bucket policy using the Amazon S3 console<a name="add-bucket-policy"></a>

You can use the Amazon S3 console to add a new bucket policy or edit an existing bucket policy\. A bucket policy is a resource\-based AWS Identity and Access Management \(IAM\) policy\. You add a bucket policy to a bucket to grant other AWS accounts or IAM users access permissions for the bucket and the objects in it\. Object permissions apply only to the objects that the bucket owner creates\. For more information about bucket policies, see [Overview of managing access](access-control-overview.md)\.

By default, when another AWS account uploads an object to your S3 bucket, that account \(the object writer\) owns the object, has access to it, and can grant other users access to it through access control lists \(ACLs\)\. You can use Object Ownership to change this default behavior so that ACLs are disabled and you, as the bucket owner, automatically own every object in your bucket\. As a result, access control for your data is based on policies, such as IAM policies, S3 bucket policies, virtual private cloud \(VPC\) endpoint policies, and AWS Organizations service control policies \(SCPs\)\. For more information, see [Controlling ownership of objects and disabling ACLs for your bucket](about-object-ownership.md)\.

Make sure to resolve security warnings, errors, general warnings, and suggestions from AWS Identity and Access Management Access Analyzer before you save your policy\. IAM Access Analyzer runs policy checks to validate your policy against IAM [policy grammar](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_grammar.html) and [best practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)\. These checks generate findings and provide actionable recommendations to help you author policies that are functional and conform to security best practices\. To learn more about validating policies using IAM Access Analyzer, see [IAM Access Analyzer policy validation](https://docs.aws.amazon.com/IAM/latest/UserGuide/access-analyzer-policy-validation.html) in the *IAM User Guide*\. To view a list of the warnings, errors, and suggestions that are returned by IAM Access Analyzer, see [IAM Access Analyzer policy check reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/access-analyzer-reference-policy-checks.html)\.

**To create or edit a bucket policy**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the name of the bucket that you want to create a bucket policy for or whose bucket policy you want to edit\.

1. Choose **Permissions**\.

1. Under **Bucket policy**, choose **Edit**\. This opens the Edit bucket policy page\.

1. On the **Edit bucket policy **page, explore **Policy examples** in the *Amazon S3 User Guide*, choose **Policy generator** to generate a policy automatically, or edit the JSON in the **Policy** section\. 

   If you choose **Policy generator**, the AWS Policy Generator opens in a new window:

   1. On the **AWS Policy Generator** page, in **Select Type of Policy**, choose **S3 Bucket Policy**\.

   1. Add a statement by entering the information in the provided fields, and then choose **Add Statement**\. Repeat for as many statements as you would like to add\. For more information about these fields, see the [IAM JSON policy elements reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html) in the *IAM User Guide*\. 
**Note**  
For convenience, the **Edit bucket policy** page displays the **Bucket ARN **\(Amazon Resource Name\) of the current bucket above the **Policy** text field\. You can copy this ARN for use in the statements on the **AWS Policy Generator** page\.  

   1. After you finish adding statements, choose **Generate Policy**\.

   1. Copy the generated policy text, choose **Close**, and return to the **Edit bucket policy** page in the Amazon S3 console\.

1. In the **Policy** box, edit the existing policy or paste the bucket policy from the Policy generator\. Make sure to resolve security warnings, errors, general warnings, and suggestions before you save your policy\.

1. \(Optional\) Preview how your new policy affects public and cross\-account access to your resource\. Before you save your policy, you can check whether it introduces new IAM Access Analyzer findings or resolves existing findings\. If you don’t see an active analyzer, [create an account analyzer](https://docs.aws.amazon.com/IAM/latest/UserGuide/access-analyzer-getting-started.html#access-analyzer-enabling) in IAM Access Analyzer\. For more information, see [Preview access](https://docs.aws.amazon.com/IAM/latest/UserGuide/access-analyzer-access-preview.html) in the *IAM User Guide*\. 

1. Choose **Save changes**, which returns you to the Bucket Permissions page\. 