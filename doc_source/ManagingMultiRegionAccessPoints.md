# Managing Multi\-Region Access Points<a name="ManagingMultiRegionAccessPoints"></a>

 Amazon S3 provides a set of operations to manage Multi\-Region Access Points\. Amazon S3 processes some of these operations synchronously and some asynchronously\. When you invoke an asynchronous operation, Amazon S3 first synchronously authorizes the requested operation\. If authorization is successful, Amazon S3 returns a token that you can use to track the progress and results of the requested operation\. 

**Note**  
 Requests that are made through the AWS Management Console are always synchronous\. The console waits until the request is completed before enabling you to submit another request\. 

 You can view the current status and results of the asynchronous operations using the console, or you can use `DescribeMultiRegionAccessPointOperation` in the AWS CLI, AWS SDKs, or REST API\. Amazon S3 provides a tracking token in the response to an asynchronous operation\. You include that tracking token as an argument to `DescribeMultiRegionAccessPointOperation`\. Amazon S3 then returns the current status and results of the specified operation, including any errors or relevant resource information\. Amazon S3 performs `DescribeMultiRegionAccessPointOperation` operations synchronously\. 

All requests to create or maintain Multi\-Region Access Points are routed to the US West \(Oregon\) Region\. This is true regardless of which Region you are in when making the request, or what Regions the Multi\-Region Access Point supports\. In addition, you must grant the `s3:ListAllMyBuckets` permission to the user, role, or other IAM entity that makes a request to manage a Multi\-Region Access Point\. 