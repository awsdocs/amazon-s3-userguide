--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Security considerations for S3 Object Lambda access points<a name="olap-security"></a>

S3 Object Lambda allows customers the ability to perform custom transformations on data as it leaves S3 using the scale and flexibility of AWS Lambda as a compute platform\. S3 and Lambda remain secure by default, but special consideration by the Lambda author is required in order to maintain this security\. S3 Object Lambda requires that all access be made by authenticated principals \(no anonymous access\) and over HTTPS\.

To mitigate this risk we recommend that the Lambda execution role be carefully scoped to the smallest set of privileges possible\. Additionally, the Lambda should make its S3 accesses via the provided pre\-signed URL whenever possible\.

## Configuring IAM policies<a name="olap-iam-policies"></a>

S3 Access Points support AWS Identity and Access Management \(IAM\) resource policies that allow you to control the use of the access point by resource, user, or other conditions\. For more information, see [Configuring IAM policies for Object Lambda access points](olap-policies.md)\.

## Encryption behavior<a name="olap-encryption"></a>

Since Object Lambda access point use both Amazon S3 and AWS Lambda there are differences in encryption behavior\. For more information about default S3 encryption behavior, see [ Setting default server\-side encryption behavior for Amazon S3 buckets](bucket-encryption.md)\.
+ When using S3 server\-side encryption with Object Lambda access points the object will be decrypted before being sent to AWS Lambda where it will be processed unencrypted up to the original caller \(in case of a GET\)\.
+ To prevent the key being logged, S3 will reject GET requests for objects encrypted via server\-side encryption using customer provided keys\. The Lambda function may still retrieve these objects provided it has access to the client provided key\.
+ When using S3 client\-side encryption with Object Lambda access points make sure Lambda has access to the key to decrypt and reencrypt the object\.