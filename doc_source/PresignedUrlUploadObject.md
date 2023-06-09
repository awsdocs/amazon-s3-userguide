# Uploading objects with presigned URLs<a name="PresignedUrlUploadObject"></a>



You may use presigned URLs to allow someone to upload an object to your Amazon S3 bucket\. Using a presigned URL will allow an upload without requiring another party to have AWS security credentials or permissions\. A presigned URL is limited by the permissions of the user who creates it\. That is, if you receive a presigned URL to upload an object, you can upload an object only if the creator of the URL has the necessary permissions to upload that object\.

When someone uses the URL to upload an object, Amazon S3 creates the object in the specified bucket\. If an object with the same key that is specified in the presigned URL already exists in the bucket, Amazon S3 replaces the existing object with the uploaded object\. After upload, the bucket owner will own the object\.

For general information about presigned URLs, see [Working with presigned URLs](using-presigned-url.md)\.

You can create a presigned URL for uploading an object without writing any code by using AWS Explorer for Visual Studio\. You can also generate a presigned URL programmatically by using the AWS SDKs\.

## Using AWS Explorer for Visual Studio<a name="upload-object-presignedurl-vsexplorer"></a>

If you're using Visual Studio, you can generate a presigned URL without writing any code by using AWS Explorer in the AWS Toolkit for Visual Studio\. For more information, see [Using Amazon S3 from AWS Explorer](https://docs.aws.amazon.com/toolkit-for-visual-studio/latest/user-guide/tkv-s3.html) in the *AWS Toolkit for Visual Studio User Guide*\. 

For instructions on installing AWS Explorer, see [Installing and setting up the AWS Toolkit for Visual Studio](https://docs.aws.amazon.com/toolkit-for-visual-studio/latest/user-guide/setup.html) in the *AWS Toolkit for Visual Studio User Guide*\.

1. Choose a bucket in the Amazon S3 view and open the context menu \(right\-click\)\.

1. Choose **Create presigned URL**, and then set the expiration date and time\.

1. Choose **PUT** to specify that this presigned URL will be used for uploading an object\.

1. Choose the **Generate** button\.

1. To copy the URL to the clipboard, choose **Copy**\.

## Using the AWS SDKs<a name="presigned-urls-upload-sdk"></a>

For examples of using the AWS SDKs to generate a presigned URL for uploading an object, see [Create a presigned URL for Amazon S3 by using an AWS SDK](https://docs.aws.amazon.com/AmazonS3/latest/userguide/example_s3_Scenario_PresignedUrl_section.html)\.

When you use the AWS SDKs to generate a presigned URL, the maximum expiration time is 7 days from the time of creation\.

**Note**  
For all AWS Regions launched after March 20, 2019 you need to specify the `endpoint-url` and `AWS Region` with the request\. For a list of all the Amazon S3 Regions and endpoints, see [Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html#s3_region) in the *AWS General Reference*\.