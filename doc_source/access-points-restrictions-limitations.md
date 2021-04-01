--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Access points restrictions and limitations<a name="access-points-restrictions-limitations"></a>

Amazon S3 access points have the following restrictions and limitations: 
+ You can only create access points for buckets that you own\.
+ Each access point is associated with exactly one bucket, which you must specify when you create the access point\. After you create an access point, you can't associate it with a different bucket\. However, you can delete an access point and then create another one with the same name associated with a different bucket\.
+ Access point names must meet certain conditions\. For more information about naming access points, see [Rules for naming Amazon S3 access points](creating-access-points.md#access-points-names)\.
+ After you create an access point, you can't change its virtual private cloud \(VPC\) configuration\.
+ Access point policies are limited to 20 KB in size\.
+ You can create a maximum of 1,000 access points per AWS account per Region\. If you need more than 1,000 access points for a single account in a single Region, you can request a service quota increase\. For more information about service quotas and requesting an increase, see [AWS Service Quotas](https://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html) in the *AWS General Reference*\.
+ You can't use an access point as a destination for S3 Cross\-Region Replication\. For more information about replication, see [Replicating objects](replication.md)\.
+ You can only address access points using virtual\-host\-style URLs\. For more information about virtual\-host\-style addressing, see [Accessing a bucket](access-bucket-intro.md)\.
+ APIs that control access point functionality \(for example, `PutAccessPoint` and `GetAccessPointPolicy`\) don't support cross\-account calls\.
+ You must use AWS Signature Version 4 when making requests to an access point using the REST APIs\. For more information about authenticating requests, see [Authenticating Requests \(AWS Signature Version 4\)](https://docs.aws.amazon.com/AmazonS3/latest/API/sig-v4-authenticating-requests.html) in the *Amazon Simple Storage Service API Reference*\.
+ Access points only support access over HTTPS\.
+ Access points don't support anonymous access\.