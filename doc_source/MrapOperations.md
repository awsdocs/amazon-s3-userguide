# Using Multi\-Region Access Points with supported API operations<a name="MrapOperations"></a>

 Amazon S3 provides a set of operations to manage Multi\-Region Access Points\. Amazon S3 processes some of these operations synchronously and some asynchronously\. When you invoke an asynchronous operation, Amazon S3 first synchronously authorizes the requested operation\. If authorization is successful, Amazon S3 returns a token that you can use to track the progress and results of the requested operation\. 

**Note**  
 Requests that are made through the AWS Management Console are always synchronous\. The console waits until the request is completed before enabling you to submit another request\. 

 You can view the current status and results of the asynchronous operations using the console, or you can use `DescribeMultiRegionAccessPointOperation` in the AWS CLI, AWS SDKs, or REST API\. Amazon S3 provides a tracking token in the response to an asynchronous operation\. You include that tracking token as an argument to `DescribeMultiRegionAccessPointOperation`\. Amazon S3 then returns the current status and results of the specified operation, including any errors or relevant resource information\. Amazon S3 performs `DescribeMultiRegionAccessPointOperation` operations synchronously\. 

All requests to create or maintain Multi\-Region Access Points are routed to the US West \(Oregon\) Region\. This is true regardless of which Region that you are in when making the request, or what Regions the Multi\-Region Access Point supports\. In addition, you must grant the `s3:ListAllMyBuckets` permission to the user, role, or other AWS Identity and Access Management \(IAM\) entity that makes a request to manage a Multi\-Region Access Point\. 

The following examples demonstrate how to use Multi\-Region Access Points with compatible operations in Amazon S3\.

**Topics**
+ [Multi\-Region Access Point compatibility with AWS services](#mrap-api-support)
+ [Multi\-Region Access Point compatibility with S3 operations](#mrap-operations-support)
+ [View your Multi\-Region Access Point routing configuration](#query-mrap-routing-configuration)
+ [Update your underlying Amazon S3 bucket policy](#update-underlying-bucket-policy)
+ [Update a Multi\-Region Access Point route configuration](#update-mrap-route-configuration)
+ [Use a presigned URL with Multi\-Region Access Points](#use-presigned-url-mrap)

## Multi\-Region Access Point compatibility with AWS services<a name="mrap-api-support"></a>

Amazon S3 Multi\-Region Access Point Amazon Resource Names \(ARNs\) allow applications \(using an AWS SDK\) that require an Amazon S3 bucket name to use a Multi\-Region Access Point\.

## Multi\-Region Access Point compatibility with S3 operations<a name="mrap-operations-support"></a>

You can use Multi\-Region Access Points to access buckets by using the following subset of Amazon S3 API operations\. The following S3 operations can accept Multi\-Region Access Point ARNs:
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_AbortMultipartUpload.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_AbortMultipartUpload.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_CompleteMultipartUpload.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_CompleteMultipartUpload.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_CreateMultipartUpload.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_CreateMultipartUpload.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_DeleteObject.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_DeleteObject.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_DeleteObjectTagging.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_DeleteObjectTagging.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObject.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObject.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObjectAcl.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObjectAcl.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObjectLegalHold.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObjectLegalHold.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObjectRetention.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObjectRetention.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObjectTagging.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObjectTagging.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_HeadObject.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_HeadObject.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_ListMultipartUploads.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_ListMultipartUploads.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_ListObjectsV2.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_ListObjectsV2.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_ListParts.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_ListParts.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObject.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObject.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObjectAcl.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObjectAcl.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObjectLegalHold.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObjectLegalHold.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObjectRetention.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObjectRetention.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObjectTagging.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObjectTagging.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_RestoreObject.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_RestoreObject.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_UploadPart.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_UploadPart.html)

**Note**  
Multi\-Region Access Points don't support the [CopyObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_CopyObject.html) API operation\. Instead, you must perform `CopyObject` actions directly between buckets\.

You can use the following Amazon S3 Control operations with Multi\-Region Access Points:
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_CreateMultiRegionAccessPoint.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_CreateMultiRegionAccessPoint.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_DescribeMultiRegionAccessPointOperation.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_DescribeMultiRegionAccessPointOperation.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_GetMultiRegionAccessPoint.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_GetMultiRegionAccessPoint.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_GetMultiRegionAccessPointPolicy.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_GetMultiRegionAccessPointPolicy.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_GetMultiRegionAccessPointPolicyStatus.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_GetMultiRegionAccessPointPolicyStatus.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_GetMultiRegionAccessPointRoutes.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_GetMultiRegionAccessPointRoutes.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_ListMultiRegionAccessPoints.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_ListMultiRegionAccessPoints.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_PutMultiRegionAccessPointPolicy.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_PutMultiRegionAccessPointPolicy.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_SubmitMultiRegionAccessPointRoutes.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_SubmitMultiRegionAccessPointRoutes.html)

## View your Multi\-Region Access Point routing configuration<a name="query-mrap-routing-configuration"></a>

------
#### [ AWS CLI ]

The following example command retrieves your Multi\-Region Access Point route configuration so that you can see the current routing statuses for your buckets\. To use this example command, replace the `user input placeholders` with your own information\.

```
aws s3control get-multi-region-access-point-routes
--region eu-west-1
--account-id 111122223333
--mrap MultiRegionAccessPoint_ARN
```

------
#### [ SDK for Java ]

The following SDK for Java code retrieves your Multi\-Region Access Point route configuration so that you can see the current routing statuses for your buckets\. To use this example syntax, replace the `user input placeholders` with your own information\.

```
S3ControlClient s3ControlClient = S3ControlClient.builder()
    .region(Region.US_EAST_1)
    .credentialsProvider(credentialsProvider)
    .build();
 
GetMultiRegionAccessPointRoutesRequest request = GetMultiRegionAccessPointRoutesRequest.builder()
    .accountId("111122223333")
    .mrap("arn:aws:s3::111122223333:accesspoint/0000000000000.mrap")
    .build();
 
GetMultiRegionAccessPointRoutesResponse response = s3ControlClient.getMultiRegionAccessPointRoutes(request);
```

------
#### [ SDK for JavaScript ]

The following SDK for JavaScript code retrieves your Multi\-Region Access Point route configuration so that you can see the current routing statuses for your buckets\. To use this example syntax, replace the `user input placeholders` with your own information\.

```
const REGION = 'us-east-1'
 
const s3ControlClient = new S3ControlClient({
  region: REGION
})
 
export const run = async () => {
  try {
    const data = await s3ControlClient.send(
      new GetMultiRegionAccessPointRoutesCommand({
        AccountId: '111122223333',
        Mrap: 'arn:aws:s3:: 111122223333:accesspoint/0000000000000.mrap',
      })
    )
    console.log('Success', data)
    return data
  } catch (err) {
    console.log('Error', err)
  }
}
 
run()
```

------
#### [ SDK for Python ]

The following SDK for Python code retrieves your Multi\-Region Access Point route configuration so that you can see the current routing statuses for your buckets\. To use this example syntax, replace the `user input placeholders` with your own information\.

```
s3.get_multi_region_access_point_routes(
        AccountId=111122223333,
        Mrap=MultiRegionAccessPoint_ARN)['Routes']
```

------

## Update your underlying Amazon S3 bucket policy<a name="update-underlying-bucket-policy"></a>

To grant proper access, you must also update the underlying Amazon S3 bucket policy\. The following examples delegate access control to the Multi\-Region Access Point policy\. After you delegate access control to the Multi\-Region Access Point policy, the bucket policy is no longer used for access control when requests are made through the Multi\-Region Access Point\.

Here's an example bucket policy that delegates access control to the Multi\-Region Access Point policy\. To use this example bucket policy, replace the `user input placeholders` with your own information\.

```
{
  "Version": "2012-10-17",
  "Statement": {
    "Principal": { "AWS": "*" },
    "Effect": "Allow",
    "Action": ["s3:*"],
    "Resource": ["arn:aws:s3:::111122223333/*", "arn:aws:s3:::DOC-EXAMPLE-BUCKET"],
    "Condition": {
      "StringEquals": {
        "s3:DataAccessPointAccount": "444455556666"
      }
    }
  }
}
```

The following example command associates the updated S3 bucket policy with your S3 bucket:

```
aws s3api put-bucket-policy
  --bucket DOC-EXAMPLE-BUCKET
  --policy file:///tmp/policy-eu.json
```

## Update a Multi\-Region Access Point route configuration<a name="update-mrap-route-configuration"></a>

The following example command updates the Multi\-Region Access Point route configuration\. Multi\-Region Access Point route commands can be run against the following five Regions:
+ `ap-southeast-2`
+ `ap-northeast-1`
+ `us-east-1`
+ `us-west-2`
+ `eu-west-1`

In a Multi\-Region Access Point routing configuration, you can set buckets to an active or passive routing status\. Active buckets receive traffic, whereas passive buckets do not\. You can set a bucket's routing status by setting the `TrafficDialPercentage` value for the bucket to `100` for active or `0` for passive\. 

------
#### [ AWS CLI ]

The following example command updates your Multi\-Region Access Point routing configuration\. In this example, `DOC-EXAMPLE-BUCKET1` is set to active status and `DOC-EXAMPLE-BUCKET2` is set to passive\. To use this example command, replace the `user input placeholders` with your own information\.

```
aws s3control submit-multi-region-access-point-routes
--region ap-southeast-2 
--account-id 111122223333
--mrap MultiRegionAccessPoint_ARN
--route-updates Bucket=DOC-EXAMPLE-BUCKET1,TrafficDialPercentage=100
                Bucket=DOC-EXAMPLE-BUCKET2,TrafficDialPercentage=0
```

------
#### [ SDK for Java ]

The following SDK for Java code updates your Multi\-Region Access Point routing configuration\. To use this example syntax, replace the `user input placeholders` with your own information\.

```
S3ControlClient s3ControlClient = S3ControlClient.builder()
    .region(Region.AP_SOUTHEAST_2)
    .credentialsProvider(credentialsProvider)
    .build();
 
SubmitMultiRegionAccessPointRoutesRequest request = SubmitMultiRegionAccessPointRoutesRequest.builder()
    .accountId("111122223333")
    .mrap("arn:aws:s3::111122223333:accesspoint/0000000000000.mrap")
    .routeUpdates(
        MultiRegionAccessPointRoute.builder()
            .region("eu-west-1")
            .trafficDialPercentage(100)
            .build(),
        MultiRegionAccessPointRoute.builder()
            .region("ca-central-1")
            .bucket("111122223333")
            .trafficDialPercentage(0)
            .build()
    )
    .build();
 
SubmitMultiRegionAccessPointRoutesResponse response = s3ControlClient.submitMultiRegionAccessPointRoutes(request);
```

------
#### [ SDK for JavaScript ]

The following SDK for JavaScript code updates your Multi\-Region Access Point route configuration\. To use this example syntax, replace the `user input placeholders` with your own information\.

```
const REGION = 'ap-southeast-2'
 
const s3ControlClient = new S3ControlClient({
  region: REGION
})
 
export const run = async () => {
  try {
    const data = await s3ControlClient.send(
      new SubmitMultiRegionAccessPointRoutesCommand({
        AccountId: '111122223333',
        Mrap: 'arn:aws:s3::111122223333:accesspoint/0000000000000.mrap',
        RouteUpdates: [
          {
            Region: 'eu-west-1',
            TrafficDialPercentage: 100,
          },
          {
            Region: 'ca-central-1',
            Bucket: 'DOC-EXAMPLE-BUCKET1',
            TrafficDialPercentage: 0,
          },
        ],
      })
    )
    console.log('Success', data)
    return data
  } catch (err) {
    console.log('Error', err)
  }
}
 
run()
```

------
#### [ SDK for Python ]

The following SDK for Python code updates your Multi\-Region Access Point route configuration\. To use this example syntax, replace the `user input placeholders` with your own information\.

```
s3.submit_multi_region_access_point_routes(
        AccountId=111122223333,
        Mrap=MultiRegionAccessPoint_ARN, 
        RouteUpdates= [{
            'Bucket': DOC-EXAMPLE-BUCKET,
            'Region': ap-southeast-2, 
            'TrafficDialPercentage': 10
        }])
```

------

## Use a presigned URL with Multi\-Region Access Points<a name="use-presigned-url-mrap"></a>

You can use a presigned URL to generate a URL that allows you to access your Amazon S3 buckets through an Amazon S3 Multi\-Region Access Point\. When you create a presigned URL, you associate it with a specific object action such as an S3 upload \(`PutObject`\) or an S3 download \(`GetObject`\)\. You can share the URL, and anyone with access to it can perform the action embedded in the URL as if they were the original signing user\.

Presigned URLs have an expiration date\. When the expiration time is reached, the URL will no longer work\. 

Before you use S3 Multi\-Region Access Points with presigned URLs, check the [AWS SDK compatibility](https://docs.aws.amazon.com/sdkref/latest/guide/feature-s3-mrap.html) with the SigV4A algorithm\. Verify that your SDK version supports SigV4A as the signing implementation that is used to sign the global AWS Region requests\. For more information about using presigned URLs with Amazon S3, see [Sharing objects by using presigned URLs](https://docs.aws.amazon.com/AmazonS3/latest/userguide/ShareObjectPreSignedURL.html)\.

The following examples show how you can use Amazon S3 Multi\-Region Access Points with presigned URLs\. To use these examples, replace the *`user input placeholders`* with your own information\.

------
#### [ AWS CLI ]

```
aws s3 presign arn:aws:s3::123456789012:accesspoint/MultiRegionAccessPoint_alias/example-file.txt
```

------
#### [ SDK for Python ]

```
import logging
import boto3
from botocore.exceptions import ClientError

s3_client = boto3.client('s3',aws_access_key_id='xxx',aws_secret_access_key='xxx')
s3_client.generate_presigned_url(HttpMethod='PUT',ClientMethod="put_object", Params={'Bucket':'arn:aws:s3::123456789012:accesspoint/0000000000000.mrap','Key':'example-file'})
```

------
#### [ SDK for Java ]

```
S3Presigner s3Presigner = S3Presigner.builder()
    .credentialsProvider(StsAssumeRoleCredentialsProvider.builder()
        .refreshRequest(assumeRole)
        .stsClient(stsClient)
        .build())
    .build();

GetObjectRequest getObjectRequest = GetObjectRequest.builder()
    .bucket("arn:aws:s3::123456789012:accesspoint/0000000000000.mrap")
    .key("example-file")
    .build();

GetObjectPresignRequest preSignedReq = GetObjectPresignRequest.builder()
    .getObjectRequest(getObjectRequest)
    .signatureDuration(Duration.ofMinutes(10))
    .build();

PresignedGetObjectRequest presignedGetObjectRequest = s3Presigner.presignGetObject(preSignedReq);
```

------

**Note**  
To use SigV4A with temporary security credentials—for example, when using IAM roles—make sure that you request the temporary credentials from a Regional endpoint in AWS Security Token Service \(AWS STS\), instead of a global endpoint\. If you use the global endpoint for AWS STS \(`sts.amazonaws.com`\), AWS STS will generate temporary credentials from a global endpoint, which isn't supported by Sig4A\. As a result, you'll get an error\. To resolve this issue, use any of the listed [Regional endpoints for AWS STS](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_enable-regions.html#id_credentials_region-endpoints)\.