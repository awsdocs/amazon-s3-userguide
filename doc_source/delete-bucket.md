# Deleting a bucket<a name="delete-bucket"></a>

You can delete an empty Amazon S3 bucket\. Before deleting a bucket, consider the following:
+ Bucket names are unique\. If you delete a bucket, another AWS user can use the name\. 
+ If the bucket hosts a static website, and you created and configured an Amazon Route 53 hosted zone as described in [Configuring a static website using a custom domain registered with Route 53](website-hosting-custom-domain-walkthrough.md), you must clean up the Route 53 hosted zone settings that are related to the bucket\. For more information, see [Step 2: Delete the Route 53 hosted zone](getting-started-cleanup.md#getting-started-cleanup-route53)\.
+ If the bucket receives log data from Elastic Load Balancing \(ELB\): We recommend that you stop the delivery of ELB logs to the bucket before deleting it\. After you delete the bucket, if another user creates a bucket using the same name, your log data could potentially be delivered to that bucket\. For information about ELB access logs, see [Access logs](https://docs.aws.amazon.com/elasticloadbalancing/latest/classic/access-log-collection.html) in the *User Guide for Classic Load Balancers* and [Access logs](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-access-logs.html) in the *User Guide for Application Load Balancers*\.

**Troubleshooting**  
If you are unable to delete an Amazon S3 bucket, consider the following:
+ **Make sure the bucket is empty** – You can only delete buckets that don't have any objects in them\. Make sure the bucket is empty\.
+ **s3:DeleteBucket permissions** – If you cannot delete a bucket, work with your IAM administrator to confirm that you have `s3:DeleteBucket` permissions in your IAM user policy\.
+ **s3:DeleteBucket deny statement** – If you have `s3:DeleteBucket` permissions in your IAM policy and you cannot delete a bucket, the bucket policy might include a deny statement for `s3:DeleteBucket`\. Buckets created by ElasticBeanstalk have a policy containing this statement by default\. Before you can delete the bucket, you must delete this statement or the bucket policy\.

**Important**  
Bucket names are unique\. If you delete a bucket, another AWS user can use the name\. If you want to continue to use the same bucket name, don't delete the bucket\. We recommend that you empty the bucket and keep it\.

## Using the S3 console<a name="delete-bucket-console"></a>

**To delete an S3 bucket**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, select the option next to the name of the bucket that you want to delete, and then choose **Delete** at the top of the page\.

1. On the **Delete bucket** page, confirm that you want to delete the bucket by entering the bucket name into the text field, and then choose **Delete bucket**\.
**Note**  
If the bucket contains any objects, empty the bucket before deleting it by selecting the *empty bucket configuration* link in the **This bucket is not empty** error alert and following the instructions on the **Empty bucket** page\. Then return to the **Delete bucket** page and delete the bucket\.

   

## Using the AWS SDK for Java<a name="delete-empty-bucket"></a>

The following example shows you how to delete a bucket using the AWS SDK for Java\. First, the code deletes objects in the bucket and then it deletes the bucket\. For information about other AWS SDKs, see [Tools for Amazon Web Services](https://aws.amazon.com/tools/)\.

------
#### [ Java ]

The following Java example deletes a bucket that contains objects\. The example deletes all objects, and then it deletes the bucket\. The example works for buckets with or without versioning enabled\.

**Note**  
For buckets without versioning enabled, you can delete all objects directly and then delete the bucket\. For buckets with versioning enabled, you must delete all object versions before deleting the bucket\.

For instructions on creating and testing a working sample, see [Testing the Amazon S3 Java Code Examples](UsingTheMPJavaAPI.md#TestingJavaSamples)\. 

```
import com.amazonaws.AmazonServiceException;
import com.amazonaws.SdkClientException;
import com.amazonaws.auth.profile.ProfileCredentialsProvider;
import com.amazonaws.regions.Regions;
import com.amazonaws.services.s3.AmazonS3;
import com.amazonaws.services.s3.AmazonS3ClientBuilder;
import com.amazonaws.services.s3.model.*;

import java.util.Iterator;

public class DeleteBucket2 {

    public static void main(String[] args) {
        Regions clientRegion = Regions.DEFAULT_REGION;
        String bucketName = "*** Bucket name ***";

        try {
            AmazonS3 s3Client = AmazonS3ClientBuilder.standard()
                    .withCredentials(new ProfileCredentialsProvider())
                    .withRegion(clientRegion)
                    .build();

            // Delete all objects from the bucket. This is sufficient
            // for unversioned buckets. For versioned buckets, when you attempt to delete objects, Amazon S3 inserts
            // delete markers for all objects, but doesn't delete the object versions.
            // To delete objects from versioned buckets, delete all of the object versions before deleting
            // the bucket (see below for an example).
            ObjectListing objectListing = s3Client.listObjects(bucketName);
            while (true) {
                Iterator<S3ObjectSummary> objIter = objectListing.getObjectSummaries().iterator();
                while (objIter.hasNext()) {
                    s3Client.deleteObject(bucketName, objIter.next().getKey());
                }

                // If the bucket contains many objects, the listObjects() call
                // might not return all of the objects in the first listing. Check to
                // see whether the listing was truncated. If so, retrieve the next page of objects 
                // and delete them.
                if (objectListing.isTruncated()) {
                    objectListing = s3Client.listNextBatchOfObjects(objectListing);
                } else {
                    break;
                }
            }

            // Delete all object versions (required for versioned buckets).
            VersionListing versionList = s3Client.listVersions(new ListVersionsRequest().withBucketName(bucketName));
            while (true) {
                Iterator<S3VersionSummary> versionIter = versionList.getVersionSummaries().iterator();
                while (versionIter.hasNext()) {
                    S3VersionSummary vs = versionIter.next();
                    s3Client.deleteVersion(bucketName, vs.getKey(), vs.getVersionId());
                }

                if (versionList.isTruncated()) {
                    versionList = s3Client.listNextBatchOfVersions(versionList);
                } else {
                    break;
                }
            }

            // After all objects and object versions are deleted, delete the bucket.
            s3Client.deleteBucket(bucketName);
        } catch (AmazonServiceException e) {
            // The call was transmitted successfully, but Amazon S3 couldn't process 
            // it, so it returned an error response.
            e.printStackTrace();
        } catch (SdkClientException e) {
            // Amazon S3 couldn't be contacted for a response, or the client couldn't
            // parse the response from Amazon S3.
            e.printStackTrace();
        }
    }
}
```

------

## Using the AWS CLI<a name="delete-bucket-awscli"></a>

You can delete a bucket that contains objects with the AWS CLI if it doesn't have versioning enabled\. When you delete a bucket that contains objects, all the objects in the bucket are permanently deleted, including objects that are transitioned to the `S3 Glacier` storage class\.

If your bucket does not have versioning enabled, you can use the `rb` \(remove bucket\) AWS CLI command with the `--force` parameter to delete the bucket and all the objects in it\. This command deletes all objects first and then deletes the bucket\.

If versioning is enabled versioned objects will not be deleted in this process which would cause the bucket deletion to fail because the bucket would not be empty\. For more information about deleting versioned objects, see [Deleting object versions](https://docs.aws.amazon.com/AmazonS3/latest/userguide/DeletingObjectVersions.html)\.

```
$ aws s3 rb s3://bucket-name --force  
```

For more information, see [Using High\-Level S3 Commands with the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/using-s3-commands.html) in the AWS Command Line Interface User Guide\.