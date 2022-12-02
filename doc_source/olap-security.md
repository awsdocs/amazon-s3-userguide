# Security considerations for S3 Object Lambda access points<a name="olap-security"></a>

With Amazon S3 Object Lambda, you can perform custom transformations on data as it leaves Amazon S3 by using the scale and flexibility of AWS Lambda as a compute platform\. S3 and Lambda remain secure by default, but to maintain this security, special consideration by the Lambda function author is required\. S3 Object Lambda requires that all access be made by authenticated principals \(no anonymous access\) and over HTTPS\.

To mitigate security risks, we recommend the following: 
+ Scope the Lambda execution role to the smallest set of permissions possible\.
+ Whenever possible, make sure your Lambda function accesses Amazon S3 through the provided presigned URL\. 

## Configuring IAM policies<a name="olap-iam-policies"></a>

S3 access points support AWS Identity and Access Management \(IAM\) resource policies that allow you to control the use of the access point by resource, user, or other conditions\. For more information, see [Configuring IAM policies for Object Lambda access points](olap-policies.md)\.

## Encryption behavior<a name="olap-encryption"></a>

Because Object Lambda access points use both Amazon S3 and AWS Lambda, there are differences in encryption behavior\. For more information about default S3 encryption behavior, see [Setting default server\-side encryption behavior for Amazon S3 buckets](bucket-encryption.md)\.
+ When you're using S3 server\-side encryption with Object Lambda access points, the object is decrypted before being sent to Lambda\. After the object is sent to Lambda, it is processed unencrypted \(in the case of a `GET` or `HEAD` request\)\.
+ To prevent the encryption key from being logged, S3 rejects `GET` and `HEAD` requests for objects that are encrypted by using server\-side encryption with customer\-provided keys \(SSE\-C\)\. However, the Lambda function might still retrieve these objects if it has access to the client\-provided key\.
+ When using S3 client\-side encryption with Object Lambda access points, make sure that Lambda has access to the encryption key so that it can decrypt and re\-encrypt the object\.

## Access points security<a name="olap-access-points-security"></a>

S3 Object Lambda uses two access points, an Object Lambda access point and a standard S3 access point, which is referred to as the *supporting access point*\. When you make a request to an Object Lambda access point, S3 either invokes Lambda on your behalf, or it delegates the request to the supporting access point, depending upon the S3 Object Lambda configuration\. When Lambda is invoked for a request S3 generates a presigned URL to your object on your behalf through the supporting access point\. Your Lambda function receives this URL as input when the function is invoked\.

You can set your Lambda function to use this presigned URL to retrieve the original object, instead of invoking S3 directly\. By using this model, you can apply better security boundaries to your objects\. You can limit direct object access through S3 buckets or S3 access points to a limited set of IAM roles or users\. This approach also protects your Lambda functions from being subject to the [confused deputy problem](https://docs.aws.amazon.com/IAM/latest/UserGuide/confused-deputy.html), where a misconfigured function with different permissions than the invoker could allow or deny access to objects when it should not\.

## Object Lambda access point public access<a name="olap-public-access"></a>

S3 Object Lambda does not allow anonymous or public access because Amazon S3 must authorize your identity to complete any S3 Object Lambda request\. When invoking requests through an Object Lambda access point, you must have the `lambda:InvokeFunction` permission for the configured Lambda function\. Similarly, when invoking other API operations through an Object Lambda access point, you must have the required `s3:*` permissions\. 

Without these permissions, requests to invoke Lambda or delegate to S3 will fail with HTTP 403 \(Forbidden\) errors\. All access must be made by authenticated principals\. If you require public access, you can use Lambda@Edge as a possible alternative\. For more information, see [Customizing at the edge with Lambda@Edge](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/lambda-at-the-edge.html) in the *Amazon CloudFront Developer Guide*\.

## Object Lambda access point IP addresses<a name="olap-ips"></a>

The `describe-managed-prefix-lists` subnets support gateway virtual private cloud \(VPC\) endpoints and are related to the routing table of VPC endpoints\. Since Object Lambda access point does not support gateway VPC it's IP ranges are missing\. The missing ranges belong to Amazon S3, but are not supported by gateway VPC endpoints\. For more information about `describe-managed-prefix-lists`, see [DescribeManagedPrefixLists](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeManagedPrefixLists.html) in the *Amazon EC2 API Reference* and [AWS IP address ranges](https://docs.aws.amazon.com/general/latest/gr/aws-ip-ranges.html) in the *AWS General Reference*\.