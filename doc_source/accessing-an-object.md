# Step 3: Download an object<a name="accessing-an-object"></a>

After you upload an object to a bucket, you can view information about your object and download the object to your local computer\.

## Using the S3 console<a name="download-objects-console"></a>

This section explains how to use the Amazon S3 console to download an object from an S3 bucket using a presigned URL\.

**Note**  
You can only download one object at a time\.
Objects with key names ending with period\(s\) "\." downloaded using the Amazon S3 console will have the period\(s\) "\." removed from the key name of the downloaded object\. To download an object with the key name ending in period\(s\) "\." retained in the downloaded object, you must use the AWS Command Line Interface \(AWS CLI\), AWS SDKs, or REST API\.

**To download an object from an S3 bucket**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the name of the bucket that you want to download an object from\.

    

1. You can download an object from an S3 bucket in any of the following ways:
   + Select the object and choose **Download** or choose **Download as** from the **Actions** menu if you want to download the object to a specific folder\.
   + If you want to download a specific version of the object, select the **Show versions** button\. Select the version of the object that you want and choose **Download** or choose **Download as** from the **Actions** menu if you want to download the object to a specific folder\.

You've successfully downloaded your object\.

**Next step**  
To copy and paste your object within Amazon S3, see [Step 4: Copy your object to a folder](copying-an-object.md)\.