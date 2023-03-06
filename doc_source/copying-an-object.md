# Step 4: Copy your object to a folder<a name="copying-an-object"></a>

You've already added an object to a bucket and downloaded the object\. Now, you create a folder and copy the object and paste it into the folder\.

**To copy an object to a folder**

1. In the **Buckets** list, choose your bucket name\.

1. Choose **Create folder** and configure a new folder: 

   1. Enter a folder name \(for example, `favorite-pics`\)\.

   1. For the folder encryption setting, choose **Disable**\.

   1. Choose **Save**\.

1. Navigate to the Amazon S3 bucket or folder that contains the objects that you want to copy\.

1. Select the check box to the left of the names of the objects that you want to copy\.

1. Choose **Actions** and choose **Copy** from the list of options that appears\.

   Alternatively, choose **Copy** from the options in the upper right\. 

1. Choose the destination folder:

   1. Choose **Browse S3**\.

   1. Choose the option button to the left of the folder name\.

      To navigate into a folder and choose a subfolder as your destination, choose the folder name\.

   1. Choose **Choose destination**\.

   The path to your destination folder appears in the **Destination** box\. In **Destination**, you can alternately enter your destination path, for example, s3://*bucket\-name*/*folder\-name*/\.

1. In the bottom right, choose **Copy**\.

   Amazon S3 copies your objects to the destination folder\.

**Next step**  
To delete an object and a bucket in Amazon S3, see [Step 5: Delete your objects and bucket](deleting-object-bucket.md)\.