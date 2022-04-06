# Convert text to speech and back to text using an AWS SDK<a name="example_cross_Telephone_section"></a>

The following code example shows how to:
+ Use Amazon Polly to synthesize a plain text \(UTF\-8\) input file to an audio file\.
+ Upload the audio file to an Amazon Simple Storage Service \(Amazon S3\) bucket\.
+ Use Amazon Transcribe to convert the audio file to text\.
+ Display the text\.

**Note**  
The source code for these examples is in the [AWS Code Examples GitHub repository](https://github.com/awsdocs/aws-doc-sdk-examples)\. Have feedback on a code example? [Create an Issue](https://github.com/awsdocs/aws-doc-sdk-examples/issues/new/choose) in the code examples repo\. 

------
#### [ Rust ]

**SDK for Rust**  
This documentation is for an SDK in preview release\. The SDK is subject to change and should not be used in production\.
 Use Amazon Polly to synthesize a plain text \(UTF\-8\) input file to an audio file, upload the audio file to an Amazon Simple Storage Service bucket, use Amazon Transcribe to convert that audio file to text, and display the text\.   
 For complete source code and instructions on how to set up and run, see the full example on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/blob/main/rust_dev_preview/cross_service#code-examples)\.   

**Services used in this example**
+ Amazon Polly
+ Amazon S3
+ Amazon Transcribe

------

For a complete list of AWS SDK developer guides and code examples, see [Using this service with an AWS SDK](UsingAWSSDK.md#sdk-general-information-section)\. This topic also includes information about getting started and details about previous SDK versions\.