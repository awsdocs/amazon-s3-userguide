# Tracking a multipart upload<a name="track-mpu"></a>

The high\-level multipart upload API provides a listen interface, `ProgressListener`, to track the upload progress when uploading an object to Amazon S3\. Progress events occur periodically and notify the listener that bytes have been transferred\. 

------
#### [ Java ]

**Example**  

```
 1. TransferManager tm = new TransferManager(new ProfileCredentialsProvider());        
 2. 
 3. PutObjectRequest request = new PutObjectRequest(
 4.   		existingBucketName, keyName, new File(filePath));
 5. 
 6. // Subscribe to the event and provide event handler.        
 7. request.setProgressListener(new ProgressListener() {
 8. 			public void progressChanged(ProgressEvent event) {
 9. 				System.out.println("Transferred bytes: " + 
10. 						event.getBytesTransfered());
11.              }
12. });
```

**Example**  
The following Java code uploads a file and uses the `ProgressListener` to track the upload progress\. For instructions on how to create and test a working sample, see [Testing the Amazon S3 Java Code Examples](UsingTheMPJavaAPI.md#TestingJavaSamples)\.   

```
import java.io.File;

import com.amazonaws.AmazonClientException;
import com.amazonaws.auth.profile.ProfileCredentialsProvider;
import com.amazonaws.event.ProgressEvent;
import com.amazonaws.event.ProgressListener;
import com.amazonaws.services.s3.model.PutObjectRequest;
import com.amazonaws.services.s3.transfer.TransferManager;
import com.amazonaws.services.s3.transfer.Upload;

public class TrackMPUProgressUsingHighLevelAPI {

    public static void main(String[] args) throws Exception {
        String existingBucketName = "*** Provide bucket name ***";
        String keyName            = "*** Provide object key ***";
        String filePath           = "*** file to upload ***";  
        
        TransferManager tm = new TransferManager(new ProfileCredentialsProvider());        

        // For more advanced uploads, you can create a request object 
        // and supply additional request parameters (ex: progress listeners,
        // canned ACLs, etc.)
        PutObjectRequest request = new PutObjectRequest(
        		existingBucketName, keyName, new File(filePath));
        
        // You can ask the upload for its progress, or you can 
        // add a ProgressListener to your request to receive notifications 
        // when bytes are transferred.
        request.setGeneralProgressListener(new ProgressListener() {
			@Override
			public void progressChanged(ProgressEvent progressEvent) {
				System.out.println("Transferred bytes: " + 
						progressEvent.getBytesTransferred());
			}
		});

        // TransferManager processes all transfers asynchronously, 
        // so this call will return immediately.
        Upload upload = tm.upload(request);
        
        try {
        	// You can block and wait for the upload to finish
        	upload.waitForCompletion();
        } catch (AmazonClientException amazonClientException) {
        	System.out.println("Unable to upload file, upload aborted.");
        	amazonClientException.printStackTrace();
        }
    }
}
```

------
#### [ \.NET ]

The following C\# example uploads a file to an S3 bucket using the `TransferUtility` class, and tracks the progress of the upload\. For information about the example's compatibility with a specific version of the AWS SDK for \.NET and instructions for creating and testing a working sample, see [Running the Amazon S3 \.NET Code Examples](UsingTheMPDotNetAPI.md#TestingDotNetApiSamples)\.

```
using Amazon;
using Amazon.S3;
using Amazon.S3.Transfer;
using System;
using System.Threading.Tasks;

namespace Amazon.DocSamples.S3
{
    class TrackMPUUsingHighLevelAPITest
    {
        private const string bucketName = "*** provide the bucket name ***";
        private const string keyName = "*** provide the name for the uploaded object ***";
        private const string filePath = " *** provide the full path name of the file to upload **";
        // Specify your bucket region (an example region is shown).
        private static readonly RegionEndpoint bucketRegion = RegionEndpoint.USWest2;
        private static IAmazonS3 s3Client;


        public static void Main()
        {
            s3Client = new AmazonS3Client(bucketRegion);
            TrackMPUAsync().Wait();
        }

        private static async Task TrackMPUAsync()
        {
            try
            {
                var fileTransferUtility = new TransferUtility(s3Client);

                // Use TransferUtilityUploadRequest to configure options.
                // In this example we subscribe to an event.
                var uploadRequest =
                    new TransferUtilityUploadRequest
                    {
                        BucketName = bucketName,
                        FilePath = filePath,
                        Key = keyName
                    };

                uploadRequest.UploadProgressEvent +=
                    new EventHandler<UploadProgressArgs>
                        (uploadRequest_UploadPartProgressEvent);

                await fileTransferUtility.UploadAsync(uploadRequest);
                Console.WriteLine("Upload completed");
            }
            catch (AmazonS3Exception e)
            {
                Console.WriteLine("Error encountered on server. Message:'{0}' when writing an object", e.Message);
            }
            catch (Exception e)
            {
                Console.WriteLine("Unknown encountered on server. Message:'{0}' when writing an object", e.Message);
            }
        }

        static void uploadRequest_UploadPartProgressEvent(object sender, UploadProgressArgs e)
        {
            // Process event.
            Console.WriteLine("{0}/{1}", e.TransferredBytes, e.TotalBytes);
        }
    }
}
```

------