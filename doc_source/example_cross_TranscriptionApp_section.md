# Build an Amazon Transcribe app<a name="example_cross_TranscriptionApp_section"></a>

The following code example shows how to use Amazon Transcribe to transcribe and display voice recordings in the browser\.

**Note**  
The source code for these examples is in the [AWS Code Examples GitHub repository](https://github.com/awsdocs/aws-doc-sdk-examples)\. Have feedback on a code example? [Create an Issue](https://github.com/awsdocs/aws-doc-sdk-examples/issues/new/choose) in the code examples repo\. 

------
#### [ JavaScript ]

**SDK for JavaScript V3**  
 Create an app that uses Amazon Transcribe to transcribe and display voice recordings in the browser\. The app uses two Amazon Simple Storage Service \(Amazon S3\) buckets, one to host the application code, and another to store transcriptions\. The app uses an Amazon Cognito user pool to authenticate your users\. Authenticated users have AWS Identity and Access Management \(IAM\) permissions to access the required AWS services\.   
 For complete source code and instructions on how to set up and run, see the full example on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/javascriptv3/example_code/cross-services/transcription-app)\.   
This example is also available in the [AWS SDK for JavaScript v3 developer guide](https://docs.aws.amazon.com/sdk-for-javascript/v3/developer-guide/transcribe-app.html)\.  

**Services used in this example**
+ Amazon Cognito Identity
+ Amazon S3
+ Amazon Transcribe

------

For a complete list of AWS SDK developer guides and code examples, see [Using this service with an AWS SDK](UsingAWSSDK.md#sdk-general-information-section)\. This topic also includes information about getting started and details about previous SDK versions\.