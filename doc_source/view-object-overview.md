# Viewing an object overview in the Amazon S3 console<a name="view-object-overview"></a>

You can use the Amazon S3 console to view an overview of an object\. The object overview in the console provides all the essential information for an object in one place\.

**To open the overview pane for an object**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the name of the bucket that contains the object\.

1. In the **Objects** list, choose the name of the object for which you want an overview\.

   The object overview opens\.

1. To download the object, choose **Object actions**, and then choose **Download**\. To copy the path of the object to the clipboard, under **Object URL**, choose the URL\.

1. If versioning is enabled on the bucket, choose **Versions** to list the versions of the object\.
   + To download an object version, select the check box next to the version ID, choose **Actions**, and then choose **Download**\.
   + To delete an object version, select the check box next to the version ID, and choose **Delete**\.
**Important**  
You can undelete an object only if it was deleted as the latest \(current\) version\. You can't undelete a previous version of an object that was deleted\.