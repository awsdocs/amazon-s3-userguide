# Security considerations for S3 Object Lambda access points<a name="olap-security"></a>

S3 Object Lambda allows customers the ability to perform custom transformations on data as it leaves S3 using the scale and flexibility of AWS Lambda as a compute platform\. S3 and Lambda remain secure by default, but special consideration by the Lambda author is required in order to maintain this security\. S3 Object Lambda requires that all access be made by authenticated principals \(no anonymous access\) and over HTTPS\.

To mitigate this risk we recommend that the Lambda execution role be carefully scoped to the smallest set of privileges possible\. Additionally, the Lambda should make its S3 accesses via the provided pre\-signed URL whenever possible\.

## Configuring IAM policies<a name="olap-iam-policies"></a>

S3 access points support AWS Identity and Access Management \(IAM\) resource policies that allow you to control the use of the access point by resource, user, or other conditions\. For more information, see [Configuring IAM policies for Object Lambda access points](olap-policies.md)\.

## Encryption behavior<a name="olap-encryption"></a>

Since Object Lambda access point use both Amazon S3 and AWS Lambda there are differences in encryption behavior\. For more information about default S3 encryption behavior, see [ Setting default server\-side encryption behavior for Amazon S3 buckets](bucket-encryption.md)\.
+ When using S3 server\-side encryption with Object Lambda access points the object will be decrypted before being sent to AWS Lambda where it will be processed unencrypted up to the original caller \(in case of a GET\)\.
+ To prevent the key being logged, S3 will reject GET requests for objects encrypted via server\-side encryption using customer provided keys\. The Lambda function may still retrieve these objects provided it has access to the client provided key\.
+ When using S3 client\-side encryption with Object Lambda access points make sure Lambda has access to the key to decrypt and reencrypt the object\.

## Access points security<a name="olap-access-points-security"></a>

S3 Object Lambda uses two access points, an Object Lambda access point and a standard S3 access point, referred to as the supporting access point\. When you make a request to an Object Lambda access point, S3 either invokes Lambda on your behalf or delegates the request to the supporting access point, depending upon the S3 Object Lambda configuration\. When Lambda is invoked for GetObject, S3 will generate a pre\-signed URL to your object on your behalf through the supporting access point\. Your Lambda function will receive this URL as input when invoked\.

You may set your Lambda function to use this URL to retrieve the original object, instead of invoking S3 directly\. This model allows you to apply better security boundaries to your objects\. You can limit direct object access through S3 buckets or S3 access points to a limited set of IAM roles or users\. This also protects your Lambda functions from being subject to the Confused Deputy problem, where a misconfigured function with different permissions than your GetObject invoker could allow or deny access to objects when it should not\.

## Object Lambda Access Point public access<a name="olap-public-access"></a>

S3 Object Lambda does not allow anonymous or public access because Amazon S3 needs to authorize your identity to complete any S3 Object Lambda request\. When invoking GetObject requests through an Object Lambda access point, you need the `lambda:InvokeFunction` permission for the configured Lambda function\. Similarly, when invoking other APIs through an Object Lambda access point, you need to have the required `s3:*` permissions\. 

Without these permissions, requests to invoke Lambda or delegate to S3 will fail as a 403 Forbidden error\. All access must be made by authenticated principals\. If you require public access, Lambda@Edge can be used as a possible alternative\. For more information, see [Customizing at the edge with Lambda@Edge](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/lambda-at-the-edge.html) in the *Amazon CloudFront Developer Guide*\.

## Object Lambda Access Point IPs<a name="olap-ips"></a>

The describe\-managed\-prefix\-lists subnets support Gateway VPC endpoints and are related to the routing table of VPC endpoints\. The missing ranges belong to Amazon S3, but are not supported by Gateway VPC endpoints\. For more information about describe\-managed\-prefix\-lists see, [DescribePrefixLists](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribePrefixLists.html) in the *Amazon EC2 API Reference* and [AWS IP address ranges](https://docs.aws.amazon.com/general/latest/gr/aws-ip-ranges.html) in the *AWS General Reference*\.