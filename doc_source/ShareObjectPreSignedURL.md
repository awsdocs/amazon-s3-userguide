# Sharing objects with presigned URLs<a name="ShareObjectPreSignedURL"></a>

By default, all Amazon S3 objects are private, only the object owner has permission to access them\. However, the object owner may share objects with others by creating a presigned URL\. A presigned URL uses security credentials to grant time\-limited permission to download objects\. The URL can be entered in a browser or used by a program to download the object\. The credentials used by the presigned URL are those of the AWS user who generated the URL\.

For general information about presigned URLs, see [Working with presigned URLs](using-presigned-url.md)\.

You can create a presigned URL for sharing an object without writing any code by using the Amazon S3 console, AWS Explorer for Visual Studio \(Windows\), or AWS Toolkit for Visual Studio Code\. You can also generate a presigned URL programmatically by using the AWS Command Line Interface \(AWS CLI\) or the AWS SDKs\.

## Using the S3 console<a name="generating-presigned-url"></a>

 You can use the Amazon S3 console to generate a presigned URL for sharing an object by following these steps\. When using the console the maximum expiration time for a presigned URL is 12 hours from the time of creation\.

**To generate a presigned URL by using the Amazon S3 console**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Buckets**\.

1. In the **Buckets** list, choose the name of the bucket that contains the object that you want a presigned URL for\.

1. In the **Objects** list, select the object that you want to create a presigned URL for\.

1. On the **Object actions** menu, choose **Share with a presigned URL**\.

1. Specify how long you want the presigned URL to be valid\.

1. Choose **Create presigned URL**\.

1. When a confirmation appears, the URL is automatically copied to your clipboard\. You will see a button to copy the presigned URL if you need to copy it again\.

## Using the AWS CLI<a name="ShareObjectPresignedCLI"></a>

The following example AWS CLI command generates a presigned URL for sharing an object from an Amazon S3 bucket\. When you use the AWS CLI, the maximum expiration time for a presigned URL is 7 days from the time of creation\. To use this example, replace the *`user input placeholders`* with your own information\.

```
aws s3 presign s3://arn:aws:s3:::DOC-EXAMPLE-BUCKET1/mydoc.txt --expires-in 604800
```



**Note**  
For all AWS Regions launched after March 20, 2019 you need to specify the `endpoint-url` and `AWS Region` with the request\. For a list of all the Amazon S3 Regions and endpoints, see [Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html#s3_region) in the *AWS General Reference*\.

```
aws s3 presign s3://arn:aws:s3:::DOC-EXAMPLE-BUCKET1/mydoc.txt --expires-in 604800 --region af-south-1 --endpoint-url https://s3.af-south-1.amazonaws.com
```



For more information, see [https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3/presign.html](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3/presign.html) in the *AWS CLI Command Reference*\.

## Using the AWS SDKs<a name="ShareObjectPreSignedURLSDK"></a>

For examples of using the AWS SDKs to generate a presigned URL for sharing an object, see [Create a presigned URL for Amazon S3 by using an AWS SDK](https://docs.aws.amazon.com/AmazonS3/latest/userguide/example_s3_Scenario_PresignedUrl_section.html)\.

When you use the AWS SDKs to generate a presigned URL, the maximum expiration time is 7 days from the time of creation\.

**Note**  
For all AWS Regions launched after March 20, 2019 you need to specify the `endpoint-url` and `AWS Region` with the request\. For a list of all the Amazon S3 Regions and endpoints, see [Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html#s3_region) in the *AWS General Reference*\.

## Using AWS Explorer for Visual Studio \(Windows\)<a name="ShareObjectPreSignedURLVSExplorer"></a>

If you're using Visual Studio, you can generate a presigned URL to share an object without writing any code by using AWS Explorer for Visual Studio\. For general information, see [Using Amazon S3 from AWS Explorer](https://docs.aws.amazon.com/AWSToolkitVS/latest/UserGuide/tkv-s3.html) in the *AWS Toolkit for Visual Studio Developer Guide*\. 

For instructions on how to install AWS Explorer, see [Installing and setting up the AWS Toolkit for Visual Studio](https://docs.aws.amazon.com/AWSToolkitVS/latest/UserGuide/setup.html) in the *AWS Toolkit for Visual Studio User Guide*\.

**Note**  
At this time, the AWS Toolkit for Visual Studio does not support Visual Studio for Mac\.

1. Connect to AWS using the following steps, [Providing AWS credentials](https://docs.aws.amazon.com/AWSToolkitVS/latest/UserGuide/credentials.html) in the *AWS Toolkit for Visual Studio User Guide*\.

1. Choose a file in the Amazon S3 view and open the context menu \(right\-click\)\.

1. Choose **Create presigned URL**, and then set the expiration date and time for the URL\.

1. Choose **GET** to specify that this presigned URL will be used for downloading an object\.

1. Choose the **Generate** button\.

1. To copy the URL to the clipboard, choose **Copy**\.

## Using AWS Toolkit for Visual Studio Code<a name="ShareObjectPreSignedURLVSCode"></a>

If you're using Visual Studio Code, you can generate a presigned URL to share an object without writing any code by using AWS Toolkit for Visual Studio Code\. For general information, see [AWS Toolkit for Visual Studio Code](https://docs.aws.amazon.com/toolkit-for-vscode/latest/userguide/welcome.html) in the *AWS Toolkit for Visual Studio Code User Guide*\. 

For instructions on how to install the AWS Toolkit for Visual Studio Code, see [Installing the AWS Toolkit for Visual Studio Code](https://docs.aws.amazon.com/toolkit-for-vscode/latest/userguide/setup-toolkit.html) in the *AWS Toolkit for Visual Studio Code User Guide*\.

1. Connect to AWS using the following steps, [Connecting to AWS Toolkit for Visual Studio Code](https://docs.aws.amazon.com/toolkit-for-vscode/latest/userguide/connect.html) in the *AWS Toolkit for Visual Studio Code User Guide*\.

1. Select the AWS logo on the left panel in Visual Studio Code\.

1. Under **EXPLORER**, select **S3**\.

1. Choose a bucket and file and open the context menu \(right\-click\)\.

1. Choose **Generate presigned URL**, and then set the expiration time \(in minutes\)\.

1. Press Enter, and the presigned URL will be copied to your clipboard\.