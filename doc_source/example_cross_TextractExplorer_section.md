# Create an Amazon Textract explorer application<a name="example_cross_TextractExplorer_section"></a>

The following code examples show how to explore Amazon Textract output through an interactive application\.

**Note**  
The source code for these examples is in the [AWS Code Examples GitHub repository](https://github.com/awsdocs/aws-doc-sdk-examples)\. Have feedback on a code example? [Create an Issue](https://github.com/awsdocs/aws-doc-sdk-examples/issues/new/choose) in the code examples repo\. 

------
#### [ JavaScript ]

**SDK for JavaScript V3**  
 Shows how to use the AWS SDK for JavaScript to build a React application that uses Amazon Textract to extract data from a document image and display it in an interactive web page\. This example runs in a web browser and requires an authenticated Amazon Cognito identity for credentials\. It uses Amazon Simple Storage Service \(Amazon S3\) for storage, and for notifications it polls an Amazon Simple Queue Service \(Amazon SQS\) queue that is subscribed to an Amazon Simple Notification Service \(Amazon SNS\) topic\.   
 For complete source code and instructions on how to set up and run, see the full example on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/javascriptv3/example_code/cross-services/textract-react)\.   

**Services used in this example**
+ Amazon Cognito Identity
+ Amazon S3
+ Amazon SNS
+ Amazon SQS
+ Amazon Textract

------
#### [ Python ]

**SDK for Python \(Boto3\)**  
 Shows how to use the AWS SDK for Python \(Boto3\) with Amazon Textract to detect text, form, and table elements in a document image\. The input image and Amazon Textract output are shown in a Tkinter application that lets you explore the detected elements\.   
+ Submit a document image to Amazon Textract and explore the output of detected elements\.
+ Submit images directly to Amazon Textract or through an Amazon Simple Storage Service \(Amazon S3\) bucket\.
+ Use asynchronous APIs to start a job that publishes a notification to an Amazon Simple Notification Service \(Amazon SNS\) topic when the job completes\.
+ Poll an Amazon Simple Queue Service \(Amazon SQS\) queue for a job completion message and display the results\.
 For complete source code and instructions on how to set up and run, see the full example on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/python/cross_service/textract_explorer)\.   

**Services used in this example**
+ Amazon S3
+ Amazon SNS
+ Amazon SQS
+ Amazon Textract

------

For a complete list of AWS SDK developer guides and code examples, see [Using this service with an AWS SDK](UsingAWSSDK.md#sdk-general-information-section)\. This topic also includes information about getting started and details about previous SDK versions\.