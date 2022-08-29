# Manage versioned Amazon S3 objects in batches with a Lambda function using an AWS SDK<a name="example_s3_Scenario_BatchObjectVersioning_section"></a>

The following code example shows how to manage versioned S3 objects in batches with a Lambda function\.

**Note**  
The source code for these examples is in the [AWS Code Examples GitHub repository](https://github.com/awsdocs/aws-doc-sdk-examples)\. Have feedback on a code example? [Create an Issue](https://github.com/awsdocs/aws-doc-sdk-examples/issues/new/choose) in the code examples repo\. 

------
#### [ Python ]

**SDK for Python \(Boto3\)**  
 Shows how to manipulate Amazon Simple Storage Service \(Amazon S3\) versioned objects in batches by creating jobs that call AWS Lambda functions to perform processing\. This example creates a version\-enabled bucket, uploads the stanzas from the poem *You Are Old, Father William* by Lewis Carroll, and uses Amazon S3 batch jobs to twist the poem in various ways\.   

**Learn how to:**
+ Create Lambda functions that operate on versioned objects\.
+ Create a manifest of objects to update\.
+ Create batch jobs that invoke Lambda functions to update objects\.
+ Delete Lambda functions\.
+ Empty and delete a versioned bucket\.
 This example is best viewed on GitHub\. For complete source code and instructions on how to set up and run, see the full example on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/python/example_code/s3/s3_versioning#batch-operation-demo)\.   

**Services used in this example**
+ Amazon S3

------

For a complete list of AWS SDK developer guides and code examples, see [Using this service with an AWS SDK](UsingAWSSDK.md#sdk-general-information-section)\. This topic also includes information about getting started and details about previous SDK versions\.