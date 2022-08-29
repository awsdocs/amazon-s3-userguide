# Code examples for Amazon S3 using AWS SDKs<a name="service_code_examples"></a>

The following code examples show how to use Amazon S3 with an AWS software development kit \(SDK\)\. 

The examples are divided into the following categories:

**Actions**  
Code excerpts that show you how to call individual service functions\.

**Scenarios**  
Code examples that show you how to accomplish a specific task by calling multiple functions within the same service\.

**Cross\-service examples**  
Sample applications that work across multiple AWS services\.

For a complete list of AWS SDK developer guides and code examples, see [Using this service with an AWS SDK](UsingAWSSDK.md#sdk-general-information-section)\. This topic also includes information about getting started and details about previous SDK versions\.

**Contents**
+ [Actions](service_code_examples_actions.md)
  + [Add CORS rules to a bucket](example_s3_PutBucketCors_section.md)
  + [Add a lifecycle configuration to a bucket](example_s3_PutBucketLifecycleConfiguration_section.md)
  + [Add a policy to a bucket](example_s3_PutBucketPolicy_section.md)
  + [Copy an object from one bucket to another](example_s3_CopyObject_section.md)
  + [Create a bucket](example_s3_CreateBucket_section.md)
  + [Delete CORS rules from a bucket](example_s3_DeleteBucketCors_section.md)
  + [Delete a policy from a bucket](example_s3_DeleteBucketPolicy_section.md)
  + [Delete an empty bucket](example_s3_DeleteBucket_section.md)
  + [Delete an object](example_s3_DeleteObject_section.md)
  + [Delete multiple objects](example_s3_DeleteObjects_section.md)
  + [Delete the lifecycle configuration of a bucket](example_s3_DeleteBucketLifecycle_section.md)
  + [Delete the website configuration from a bucket](example_s3_DeleteBucketWebsite_section.md)
  + [Determine the existence and content type of an object](example_s3_HeadObject_section.md)
  + [Determine the existence of a bucket](example_s3_HeadBucket_section.md)
  + [Get CORS rules for a bucket](example_s3_GetBucketCors_section.md)
  + [Get an object from a bucket](example_s3_GetObject_section.md)
  + [Get the ACL of a bucket](example_s3_GetBucketAcl_section.md)
  + [Get the ACL of an object](example_s3_GetObjectAcl_section.md)
  + [Get the Region location for a bucket](example_s3_GetBucketLocation_section.md)
  + [Get the lifecycle configuration of a bucket](example_s3_GetBucketLifecycleConfiguration_section.md)
  + [Get the policy for a bucket](example_s3_GetBucketPolicy_section.md)
  + [Get the website configuration for a bucket](example_s3_GetBucketWebsite_section.md)
  + [List buckets](example_s3_ListBuckets_section.md)
  + [List in\-progress multipart uploads](example_s3_ListMultipartUploads_section.md)
  + [List object versions in a bucket](example_s3_ListObjectVersions_section.md)
  + [List objects in a bucket](example_s3_ListObjects_section.md)
  + [Restore an archived copy of an object](example_s3_RestoreObject_section.md)
  + [Set a new ACL for a bucket](example_s3_PutBucketAcl_section.md)
  + [Set the ACL of an object](example_s3_PutObjectAcl_section.md)
  + [Set the website configuration for a bucket](example_s3_PutBucketWebsite_section.md)
  + [Upload an object to a bucket](example_s3_PutObject_section.md)
+ [Scenarios](service_code_examples_scenarios.md)
  + [Create a presigned URL](example_s3_Scenario_PresignedUrl_section.md)
  + [Getting started with buckets and objects](example_s3_Scenario_GettingStarted_section.md)
  + [Manage versioned objects in batches with a Lambda function](example_s3_Scenario_BatchObjectVersioning_section.md)
  + [Use a transfer manager to upload and download files](example_s3_Scenario_TransferManager_section.md)
  + [Work with versioned objects](example_s3_Scenario_ObjectVersioningUsage_section.md)
+ [Cross\-service examples](service_code_examples_cross-service_examples.md)
  + [Build an Amazon Transcribe app](example_cross_TranscriptionApp_section.md)
  + [Convert text to speech and back to text](example_cross_Telephone_section.md)
  + [Create a long\-lived Amazon EMR cluster and run several steps](example_cross_LongLivedEmrCluster_section.md)
  + [Create a short\-lived Amazon EMR cluster and run a step](example_cross_ShortLivedEmrCluster_section.md)
  + [Create an Amazon Textract explorer application](example_cross_TextractExplorer_section.md)
  + [Detect PPE in images](example_cross_RekognitionPhotoAnalyzerPPE_section.md)
  + [Detect entities in text extracted from an image](example_cross_TextractComprehendDetectEntities_section.md)
  + [Detect faces in an image](example_cross_DetectFaces_section.md)
  + [Detect objects in images](example_cross_RekognitionPhotoAnalyzer_section.md)
  + [Detect people and objects in a video](example_cross_RekognitionVideoDetection_section.md)
  + [Save EXIF and other image information](example_cross_DetectLabels_section.md)