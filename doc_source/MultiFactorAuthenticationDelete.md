--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Configuring MFA delete<a name="MultiFactorAuthenticationDelete"></a>

When working with S3 Versioning in Amazon S3 buckets, you can optionally add another layer of security by configuring a bucket to enable *MFA \(multi\-factor authentication\) delete*\. When you do this, the bucket owner must include two forms of authentication in any request to delete a version or change the versioning state of the bucket\.

MFA delete requires additional authentication for either of the following operations:
+ Changing the versioning state of your bucket
+ Permanently deleting an object version

MFA delete requires two forms of authentication together:
+ Your security credentials
+ The concatenation of a valid serial number, a space, and the six\-digit code displayed on an approved authentication device

MFA delete thus provides added security if, for example, your security credentials are compromised\. MFA delete can help prevent accidental bucket deletions by requiring the user who initiates the delete action to prove physical possession of an MFA device with an MFA code and adding an extra layer of friction and security to the delete action\.

The bucket owner, the AWS account that created the bucket \(root account\), and all authorized IAM users can enable versioning\. However, only the bucket owner \(root account\) can enable MFA delete\. For more information, see [Securing Access to AWS Using MFA](http://aws.amazon.com/blogs/security/securing-access-to-aws-using-mfa-part-3/) on the AWS Security Blog\.

**Note**  
To use MFA delete with versioning, you enable `MFA Delete`\. However, you cannot enable `MFA Delete` using the AWS Management Console\. You must use the AWS Command Line Interface \(AWS CLI\) or the API\.   
For examples of using MFA delete with versioning, see the examples section in the topic [Enabling versioning on buckets](manage-versioning-examples.md)\.

To enable or disable MFA delete, you use the same API that you use to configure versioning on a bucket\. Amazon S3 stores the MFA delete configuration in the same *versioning* subresource that stores the bucket's versioning status\.

```
<VersioningConfiguration xmlns="http://s3.amazonaws.com/doc/2006-03-01/"> 
  <Status>VersioningState</Status>
  <MfaDelete>MfaDeleteState</MfaDelete>  
</VersioningConfiguration>
```

To use MFA delete, you can use either a hardware or virtual MFA device to generate an authentication code\. The following example shows a generated authentication code displayed on a hardware device\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/MFADevice.png)

MFA delete and MFA\-protected API access are features intended to provide protection for different scenarios\. You configure MFA delete on a bucket to help ensure that the data in your bucket cannot be accidentally deleted\. MFA\-protected API access is used to enforce another authentication factor \(MFA code\) when accessing sensitive Amazon S3 resources\. You can require any operations against these Amazon S3 resources to be done with temporary credentials created using MFA\. For an example, see [Adding a bucket policy to require MFA](example-bucket-policies.md#example-bucket-policies-use-case-7)\. 

For more information about how to purchase and activate an authentication device, see [Multi\-factor authentication](http://aws.amazon.com/iam/details/mfa/)\.