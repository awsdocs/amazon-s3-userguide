# Creating Multi\-Region Access Points<a name="CreatingMultiRegionAccessPoints"></a>

To create a Multi\-Region Access Point in Amazon S3, you do the following: 
+ Specify the name for the Multi\-Region Access Point\.
+ Choose one bucket in each AWS Region that you want to serve requests for the Multi\-Region Access Point\.
+ Configure the Amazon S3 Block Public Access settings for the Multi\-Region Access Point\.

You provide all of this information in a create request, which Amazon S3 processes asynchronously\. Amazon S3 provides a token that you can use to monitor the status of the asynchronous creation request\. 

Make sure to resolve security warnings, errors, general warnings, and suggestions from AWS Identity and Access Management Access Analyzer before you save your policy\. IAM Access Analyzer runs policy checks to validate your policy against IAM [policy grammar](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_grammar.html) and [best practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)\. These checks generate findings and provide actionable recommendations to help you author policies that are functional and conform to security best practices\. To learn more about validating policies using IAM Access Analyzer, see [IAM Access Analyzer policy validation](https://docs.aws.amazon.com/IAM/latest/UserGuide/access-analyzer-policy-validation.html) in the *IAM User Guide*\. To view a list of the warnings, errors, and suggestions that are returned by IAM Access Analyzer, see [IAM Access Analyzer policy check reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/access-analyzer-reference-policy-checks.html)\.

When you use the API, the request to create a Multi\-Region Access Point is asynchronous\. When you submit a request to create a Multi\-Region Access Point, Amazon S3 synchronously authorizes the request\. It then immediately returns a token that you can use to track the progress of the creation request\. For more information about tracking asynchronous requests to create and manage Multi\-Region Access Points, see [Using Multi\-Region Access Points with supported API operations](MrapOperations.md)\. 

After you create the Multi\-Region Access Point, you can create an access control policy for it\. Each Multi\-Region Access Point can have an associated policy\. A Multi\-Region Access Point policy is a resource\-based policy that you can use to limit the use of the Multi\-Region Access Point by resource, user, or other conditions\.

**Note**  
For an application or user to be able to access an object through a Multi\-Region Access Point, both of the following policies must permit the request:   
The access policy for the Multi\-Region Access Point
The access policy for the underlying bucket that contains the object
When the two policies are different, the more restrictive policy takes precedence\.   
To simplify permissions management for Multi\-Region Access Points, you can delegate access control from the bucket to the Multi\-Region Access Point\. For more information, see [Multi\-Region Access Point policy examples](MultiRegionAccessPointPermissions.md#MultiRegionAccessPointPolicyExamples)\.

Using a bucket with a Multi\-Region Access Point doesn't change the bucket's behavior when the bucket is accessed through the existing bucket name or an Amazon Resource Name \(ARN\)\. All existing operations against the bucket continue to work as before\. Restrictions that you include in a Multi\-Region Access Point policy apply only to requests that are made through the Multi\-Region Access Point\. 

You can update the policy for a Multi\-Region Access Point after creating it, but you can't delete the policy\. However, you can update the Multi\-Region Access Point policy to deny all permissions\. 

**Topics**
+ [Rules for naming Amazon S3 Multi\-Region Access Points](multi-region-access-point-naming.md)
+ [Rules for choosing buckets for Amazon S3 Multi\-Region Access Points](multi-region-access-point-buckets.md)
+ [Create an Amazon S3 Multi\-Region Access Point](multi-region-access-point-create-examples.md)
+ [Blocking public access with Amazon S3 Multi\-Region Access Points](multi-region-access-point-block-public-access.md)
+ [Viewing Amazon S3 Multi\-Region Access Points configuration details](multi-region-access-point-view-examples.md)
+ [Deleting a Multi\-Region Access Point](multi-region-access-point-delete-examples.md)