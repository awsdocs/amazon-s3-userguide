# Checking object integrity<a name="checking-object-integrity"></a>

Amazon S3 uses checksum values to verify the integrity of data that you upload to or download from Amazon S3\. In addition, you can request that another checksum value be calculated for any object that you store in Amazon S3\. You can select from one of several checksum algorithms to use when uploading or copying your data\. Amazon S3 uses this algorithm to compute an additional checksum value and store it as part of the object metadata\. To learn more about how to use additional checksums to verify data integrity, see [Tutorial: Checking the integrity of data in Amazon S3 with additional checksums](http://aws.amazon.com/getting-started/hands-on/amazon-s3-with-additional-checksums/?ref=docs_gateway/amazons3/checking-object-integrity.html)\.

When you upload an object, you can optionally include a precalculated checksum as part of your request\. Amazon S3 compares the provided checksum to the checksum that it calculates by using your specified algorithm\. If the two values don't match, Amazon S3 reports an error\.

## Using supported checksum algorithms<a name="using-additional-checksums"></a>

Amazon S3 offers you the option to choose the checksum algorithm that is used to validate your data during upload or download\. You can select one of the following Secure Hash Algorithms \(SHA\) or Cyclic Redundancy Check \(CRC\) data\-integrity check algorithms:
+ CRC32
+ CRC32C
+ SHA\-1
+ SHA\-256

When you upload an object, you can specify the algorithm that you want to use: 
+ **When you're using the AWS Management Console**, you select the checksum algorithm that you want to use\. When you do, you can optionally specify the checksum value of the object\. When Amazon S3 receives the object, it calculates the checksum by using the algorithm that you specified\. If the two checksum values don't match, Amazon S3 generates an error\.
+ **When you're using an SDK**, you can set the value of the `x-amz-sdk-checksum-algorithm` parameter to the algorithm that you want Amazon S3 to use when calculating the checksum\. Amazon S3 automatically calculates the checksum value\. 
+ **When you're using the REST API**, you don't use the `x-amz-sdk-checksum-algorithm` parameter\. Instead, you use one of the algorithm\-specific headers \(for example, `x-amz-checksum-crc32`\)\.

For more information about uploading objects, see [Uploading objects](upload-objects.md)\.

To apply any of these checksum values to objects that are already uploaded to Amazon S3, you can copy the object\. When you copy an object, you can specify whether you want to use the existing checksum algorithm or use a new one\. You can specify a checksum algorithm when using any supported mechanism for copying objects, including S3 Batch Operations\. For more information about S3 Batch Operations, see [Performing large\-scale batch operations on Amazon S3 objects](batch-ops.md)\.

**Important**  
If you're using a multipart upload with additional checksums, the multipart part numbers must use consecutive part numbers\. When using additional checksums, if you try to complete a multipart upload request with nonconsecutive part numbers, Amazon S3 generates an HTTP `500 Internal Server Error` error\.

After uploading objects, you can get the checksum value and compare it to a precalculated or previously stored checksum value calculated using the same algorithm\.

### Using the S3 console<a name="CheckObjectIntegrityConsole"></a>

To learn more about using the console and specifying checksum algorithms to use when uploading objects, see [Uploading objects](upload-objects.md)\.

### Using the AWS SDKs<a name="CheckObjectIntegritySDK"></a>

The following example shows how you can use the AWS SDKs to upload a large file with multipart upload, download a large file, and validate a multipart upload file, all with using SHA\-256 for file validation\.

------
#### [ Java ]

**Example: Uploading, downloading, and verifying a large file with SHA\-256**  
For instructions on creating and testing a working sample, see [Testing the Amazon S3 Java Code Examples](UsingTheMPJavaAPI.md#TestingJavaSamples)\.   

```
    import software.amazon.awssdk.auth.credentials.AwsCredentials;
    import software.amazon.awssdk.auth.credentials.AwsCredentialsProvider;
    import software.amazon.awssdk.core.ResponseInputStream;
    import software.amazon.awssdk.core.sync.RequestBody;
    import software.amazon.awssdk.regions.Region;
    import software.amazon.awssdk.services.s3.S3Client;
    import software.amazon.awssdk.services.s3.model.AbortMultipartUploadRequest;
    import software.amazon.awssdk.services.s3.model.ChecksumAlgorithm;
    import software.amazon.awssdk.services.s3.model.ChecksumMode;
    import software.amazon.awssdk.services.s3.model.CompleteMultipartUploadRequest;
    import software.amazon.awssdk.services.s3.model.CompleteMultipartUploadResponse;
    import software.amazon.awssdk.services.s3.model.CompletedMultipartUpload;
    import software.amazon.awssdk.services.s3.model.CompletedPart;
    import software.amazon.awssdk.services.s3.model.CreateMultipartUploadRequest;
    import software.amazon.awssdk.services.s3.model.CreateMultipartUploadResponse;
    import software.amazon.awssdk.services.s3.model.GetObjectAttributesRequest;
    import software.amazon.awssdk.services.s3.model.GetObjectAttributesResponse;
    import software.amazon.awssdk.services.s3.model.GetObjectRequest;
    import software.amazon.awssdk.services.s3.model.GetObjectResponse;
    import software.amazon.awssdk.services.s3.model.GetObjectTaggingRequest;
    import software.amazon.awssdk.services.s3.model.ObjectAttributes;
    import software.amazon.awssdk.services.s3.model.PutObjectTaggingRequest;
    import software.amazon.awssdk.services.s3.model.Tag;
    import software.amazon.awssdk.services.s3.model.Tagging;
    import software.amazon.awssdk.services.s3.model.UploadPartRequest;
    import software.amazon.awssdk.services.s3.model.UploadPartResponse;
     
    import java.io.File;
    import java.io.FileInputStream;
    import java.io.FileOutputStream;
    import java.io.IOException;
    import java.io.InputStream;
    import java.io.OutputStream;
    import java.nio.ByteBuffer;
    import java.security.MessageDigest;
    import java.security.NoSuchAlgorithmException;
    import java.util.ArrayList;
    import java.util.Base64;
    import java.util.List;
     
    public class LargeObjectValidation {
        private static String FILE_NAME = "sample.file";
        private static String BUCKET = "sample-bucket";
        //Optional, if you want a method of storing the full multipart object checksum in S3.
        private static String CHECKSUM_TAG_KEYNAME = "fullObjectChecksum";
        //If you have existing full-object checksums that you need to validate against, you can do the full object validation on a sequential upload.
        private static String SHA256_FILE_BYTES = "htCM5g7ZNdoSw8bN/mkgiAhXt5MFoVowVg+LE9aIQmI=";
        //Example Chunk Size - this must be greater than or equal to 5MB.
        private static int CHUNK_SIZE = 5 * 1024 * 1024;
     
        public static void main(String[] args) {
            S3Client s3Client = S3Client.builder()
                    .region(Region.US_EAST_1)
                    .credentialsProvider(new AwsCredentialsProvider() {
                        @Override
                        public AwsCredentials resolveCredentials() {
                            return new AwsCredentials() {
                                @Override
                                public String accessKeyId() {
                                    return Constants.ACCESS_KEY;
                                }
     
                                @Override
                                public String secretAccessKey() {
                                    return Constants.SECRET;
                                }
                            };
                        }
                    })
                    .build();
            uploadLargeFileBracketedByChecksum(s3Client);
            downloadLargeFileBracketedByChecksum(s3Client);
            validateExistingFileAgainstS3Checksum(s3Client);
        }
     
        public static void uploadLargeFileBracketedByChecksum(S3Client s3Client) {
            System.out.println("Starting uploading file validation");
            File file = new File(FILE_NAME);
            try (InputStream in = new FileInputStream(file)) {
                MessageDigest sha256 = MessageDigest.getInstance("SHA-256");
                CreateMultipartUploadRequest createMultipartUploadRequest = CreateMultipartUploadRequest.builder()
                        .bucket(BUCKET)
                        .key(FILE_NAME)
                        .checksumAlgorithm(ChecksumAlgorithm.SHA256)
                        .build();
                CreateMultipartUploadResponse createdUpload = s3Client.createMultipartUpload(createMultipartUploadRequest);
                List<CompletedPart> completedParts = new ArrayList<CompletedPart>();
                int partNumber = 1;
                byte[] buffer = new byte[CHUNK_SIZE];
                int read = in.read(buffer);
                while (read != -1) {
                    UploadPartRequest uploadPartRequest = UploadPartRequest.builder()
                            .partNumber(partNumber).uploadId(createdUpload.uploadId()).key(FILE_NAME).bucket(BUCKET).checksumAlgorithm(ChecksumAlgorithm.SHA256).build();
                    UploadPartResponse uploadedPart = s3Client.uploadPart(uploadPartRequest, RequestBody.fromByteBuffer(ByteBuffer.wrap(buffer, 0, read)));
                    CompletedPart part = CompletedPart.builder().partNumber(partNumber).checksumSHA256(uploadedPart.checksumSHA256()).eTag(uploadedPart.eTag()).build();
                    completedParts.add(part);
                    sha256.update(buffer, 0, read);
                    read = in.read(buffer);
                    partNumber++;
                }
                String fullObjectChecksum = Base64.getEncoder().encodeToString(sha256.digest());
                if (!fullObjectChecksum.equals(SHA256_FILE_BYTES)) {
                    //Because the SHA256 is uploaded after the part is uploaded; the upload is bracketed and the full object can be fully validated.
                    s3Client.abortMultipartUpload(AbortMultipartUploadRequest.builder().bucket(BUCKET).key(FILE_NAME).uploadId(createdUpload.uploadId()).build());
                    throw new IOException("Byte mismatch between stored checksum and upload, do not proceed with upload and cleanup");
                }
                CompletedMultipartUpload completedMultipartUpload = CompletedMultipartUpload.builder().parts(completedParts).build();
                CompleteMultipartUploadResponse completedUploadResponse = s3Client.completeMultipartUpload(
                        CompleteMultipartUploadRequest.builder().bucket(BUCKET).key(FILE_NAME).uploadId(createdUpload.uploadId()).multipartUpload(completedMultipartUpload).build());
                Tag checksumTag = Tag.builder().key(CHECKSUM_TAG_KEYNAME).value(fullObjectChecksum).build();
                //Optionally, if you need the full object checksum stored with the file; you could add it as a tag after completion.
                s3Client.putObjectTagging(PutObjectTaggingRequest.builder().bucket(BUCKET).key(FILE_NAME).tagging(Tagging.builder().tagSet(checksumTag).build()).build());
            } catch (IOException | NoSuchAlgorithmException e) {
                e.printStackTrace();
            }
            GetObjectAttributesResponse
                    objectAttributes = s3Client.getObjectAttributes(GetObjectAttributesRequest.builder().bucket(BUCKET).key(FILE_NAME)
                    .objectAttributes(ObjectAttributes.OBJECT_PARTS, ObjectAttributes.CHECKSUM).build());
            System.out.println(objectAttributes.objectParts().parts());
            System.out.println(objectAttributes.checksum().checksumSHA256());
        }
     
        public static void downloadLargeFileBracketedByChecksum(S3Client s3Client) {
            System.out.println("Starting downloading file validation");
            File file = new File("DOWNLOADED_" + FILE_NAME);
            try (OutputStream out = new FileOutputStream(file)) {
                GetObjectAttributesResponse
                        objectAttributes = s3Client.getObjectAttributes(GetObjectAttributesRequest.builder().bucket(BUCKET).key(FILE_NAME)
                        .objectAttributes(ObjectAttributes.OBJECT_PARTS, ObjectAttributes.CHECKSUM).build());
                //Optionally if you need the full object checksum, you can grab a tag you added on the upload
                List<Tag> objectTags = s3Client.getObjectTagging(GetObjectTaggingRequest.builder().bucket(BUCKET).key(FILE_NAME).build()).tagSet();
                String fullObjectChecksum = null;
                for (Tag objectTag : objectTags) {
                    if (objectTag.key().equals(CHECKSUM_TAG_KEYNAME)) {
                        fullObjectChecksum = objectTag.value();
                        break;
                    }
                }
                MessageDigest sha256FullObject = MessageDigest.getInstance("SHA-256");
                MessageDigest sha256ChecksumOfChecksums = MessageDigest.getInstance("SHA-256");
     
                //If you retrieve the object in parts, and set the ChecksumMode to enabled, the SDK will automatically validate the part checksum
                for (int partNumber = 1; partNumber <= objectAttributes.objectParts().totalPartsCount(); partNumber++) {
                    MessageDigest sha256Part = MessageDigest.getInstance("SHA-256");
                    ResponseInputStream<GetObjectResponse> response = s3Client.getObject(GetObjectRequest.builder().bucket(BUCKET).key(FILE_NAME).partNumber(partNumber).checksumMode(ChecksumMode.ENABLED).build());
                    GetObjectResponse getObjectResponse = response.response();
                    byte[] buffer = new byte[CHUNK_SIZE];
                    int read = response.read(buffer);
                    while (read != -1) {
                        out.write(buffer, 0, read);
                        sha256FullObject.update(buffer, 0, read);
                        sha256Part.update(buffer, 0, read);
                        read = response.read(buffer);
                    }
                    byte[] sha256PartBytes = sha256Part.digest();
                    sha256ChecksumOfChecksums.update(sha256PartBytes);
                    //Optionally, you can do an additional manual validation again the part checksum if needed in addition to the SDK check
                    String base64PartChecksum = Base64.getEncoder().encodeToString(sha256PartBytes);
                    String base64PartChecksumFromObjectAttributes = objectAttributes.objectParts().parts().get(partNumber - 1).checksumSHA256();
                    if (!base64PartChecksum.equals(getObjectResponse.checksumSHA256()) || !base64PartChecksum.equals(base64PartChecksumFromObjectAttributes)) {
                        throw new IOException("Part checksum didn't match for the part");
                    }
                    System.out.println(partNumber + " " + base64PartChecksum);
                }
                //Before finalizing, do the final checksum validation.
                String base64FullObject = Base64.getEncoder().encodeToString(sha256FullObject.digest());
                String base64ChecksumOfChecksums = Base64.getEncoder().encodeToString(sha256ChecksumOfChecksums.digest());
                if (fullObjectChecksum != null && !fullObjectChecksum.equals(base64FullObject)) {
                    throw new IOException("Failed checksum validation for full object");
                }
                System.out.println(fullObjectChecksum);
                String base64ChecksumOfChecksumFromAttributes = objectAttributes.checksum().checksumSHA256();
                if (base64ChecksumOfChecksumFromAttributes != null && !base64ChecksumOfChecksums.equals(base64ChecksumOfChecksumFromAttributes)) {
                    throw new IOException("Failed checksum validation for full object checksum of checksums");
                }
                System.out.println(base64ChecksumOfChecksumFromAttributes);
                out.flush();
            } catch (IOException | NoSuchAlgorithmException e) {
                //Cleanup bad file
                file.delete();
                e.printStackTrace();
            }
        }
     
        public static void validateExistingFileAgainstS3Checksum(S3Client s3Client) {
            System.out.println("Starting existing file validation");
            File file = new File("DOWNLOADED_" + FILE_NAME);
            GetObjectAttributesResponse
                    objectAttributes = s3Client.getObjectAttributes(GetObjectAttributesRequest.builder().bucket(BUCKET).key(FILE_NAME)
                    .objectAttributes(ObjectAttributes.OBJECT_PARTS, ObjectAttributes.CHECKSUM).build());
            try (InputStream in = new FileInputStream(file)) {
                MessageDigest sha256ChecksumOfChecksums = MessageDigest.getInstance("SHA-256");
                MessageDigest sha256Part = MessageDigest.getInstance("SHA-256");
                byte[] buffer = new byte[CHUNK_SIZE];
                int currentPart = 0;
                int partBreak = objectAttributes.objectParts().parts().get(currentPart).size();
                int totalRead = 0;
                int read = in.read(buffer);
                while (read != -1) {
                    totalRead += read;
                    if (totalRead >= partBreak) {
                        int difference = totalRead - partBreak;
                        byte[] partChecksum;
                        if (totalRead != partBreak) {
                            sha256Part.update(buffer, 0, read - difference);
                            partChecksum = sha256Part.digest();
                            sha256ChecksumOfChecksums.update(partChecksum);
                            sha256Part.reset();
                            sha256Part.update(buffer, read - difference, difference);
                        } else {
                            sha256Part.update(buffer, 0, read);
                            partChecksum = sha256Part.digest();
                            sha256ChecksumOfChecksums.update(partChecksum);
                            sha256Part.reset();
                        }
                        String base64PartChecksum = Base64.getEncoder().encodeToString(partChecksum);
                        if (!base64PartChecksum.equals(objectAttributes.objectParts().parts().get(currentPart).checksumSHA256())) {
                            throw new IOException("Part checksum didn't match S3");
                        }
                        currentPart++;
                        System.out.println(currentPart + " " + base64PartChecksum);
                        if (currentPart < objectAttributes.objectParts().totalPartsCount()) {
                            partBreak += objectAttributes.objectParts().parts().get(currentPart - 1).size();
                        }
                    } else {
                        sha256Part.update(buffer, 0, read);
                    }
                    read = in.read(buffer);
                }
                if (currentPart != objectAttributes.objectParts().totalPartsCount()) {
                    currentPart++;
                    byte[] partChecksum = sha256Part.digest();
                    sha256ChecksumOfChecksums.update(partChecksum);
                    String base64PartChecksum = Base64.getEncoder().encodeToString(partChecksum);
                    System.out.println(currentPart + " " + base64PartChecksum);
                }
     
                String base64CalculatedChecksumOfChecksums = Base64.getEncoder().encodeToString(sha256ChecksumOfChecksums.digest());
                System.out.println(base64CalculatedChecksumOfChecksums);
                System.out.println(objectAttributes.checksum().checksumSHA256());
                if (!base64CalculatedChecksumOfChecksums.equals(objectAttributes.checksum().checksumSHA256())) {
                    throw new IOException("Full object checksum of checksums don't match S3");
                }
     
            } catch (IOException | NoSuchAlgorithmException e) {
                e.printStackTrace();
            }
        }
    }
```

------

### Using the REST API<a name="CheckObjectIntegrityREST"></a>

You can send REST requests to upload an object with a checksum value to verify the integrity of the data with [PutObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObject.html)\. You can also retrieve the checksum value for objects using [GetObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObject.html) or [HeadObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_HeadObject.html)\.

### Using the AWS CLI<a name="CheckObjectIntegrityCLI"></a>

You can send a `PUT` request to upload an object of up to 5 GB in a single operation\. For more information, see the [https://docs.aws.amazon.com/cli/latest/reference/s3api/put-object.html#examples](https://docs.aws.amazon.com/cli/latest/reference/s3api/put-object.html#examples) in the *AWS CLI Command Reference*\. You can also use [https://docs.aws.amazon.com/cli/latest/reference/s3api/get-object.html](https://docs.aws.amazon.com/cli/latest/reference/s3api/get-object.html) and [https://docs.aws.amazon.com/cli/latest/reference/s3api/get-object.html](https://docs.aws.amazon.com/cli/latest/reference/s3api/get-object.html) to retrieve the checksum of an already\-uploaded object to verify the integrity of the data\.

## Using Content\-MD5 when uploading objects<a name="checking-object-integrity-md5"></a>

Another way to verify the integrity of your object after uploading is to provide an MD5 digest of the object when you upload it\. If you calculate the MD5 digest for your object, you can provide the digest with the `PUT` command by using the `Content-MD5` header\. 

After uploading the object, Amazon S3 calculates the MD5 digest of the object and compares it to the value that you provided\. The request succeeds only if the two digests match\. 

Supplying an MD5 digest isn't required, but you can use it to verify the integrity of the object as part of the upload process\.

## Using Content\-MD5 and the ETag to verify uploaded objects<a name="checking-object-integrity-etag-and-md5"></a>

The entity tag \(ETag\) for an object represents a specific version of that object\. Keep in mind that the ETag reflects changes only to the content of an object, not to its metadata\. If only the metadata of an object changes, the ETag remains the same\. 

Depending on the object, the ETag of the object might be an MD5 digest of the object data:
+ If an object is created by the `PUT Object`, `POST Object`, or `Copy` operation, or through the AWS Management Console, and that object is also plaintext or encrypted by server\-side encryption with Amazon S3 managed keys \(SSE\-S3\), that object has an ETag that is an MD5 digest of its object data\.
+ If an object is created by the `PUT Object`, `POST Object`, or `Copy` operation, or through the AWS Management Console, and that object is encrypted by server\-side encryption with customer\-provided keys \(SSE\-C\) or server\-side encryption with AWS Key Management Service \(AWS KMS\) keys \(SSE\-KMS\), that object has an ETag that is not an MD5 digest of its object data\.
+ If an object is created by either the `Multipart Upload` or `Part Copy` operation, the object's ETag is not an MD5 digest, regardless of the method of encryption\. If an object is larger than 16 MB, the AWS Management Console uploads or copies that object as a multipart upload, and therefore the ETag isn't an MD5 digest\.

For objects where the ETag is the `Content-MD5` digest of the object, you can compare the ETag value of the object with a calculated or previously stored `Content-MD5` digest\.

## Using trailing checksums<a name="trailing-checksums"></a>

When uploading objects to Amazon S3, you can either provide a precalculated checksum for the object or use an AWS SDK to automatically create trailing checksums on your behalf\. If you decide to use a trailing checksum, Amazon S3 automatically generates the checksum by using your specified algorithm and uses it to validate the integrity of the object during upload\. 

To create a trailing checksum when using an AWS SDK, populate the `ChecksumAlgorithm` parameter with your preferred algorithm\. The SDK uses that algorithm to calculate the checksum for your object \(or object parts\) and automatically appends it to the end of your upload request\. This behavior saves you time because Amazon S3 performs both the verification and upload of your data in a single pass\. 

**Important**  
If you're using S3 Object Lambda, all requests to S3 Object Lambda are signed using `s3-object-lambda` instead of `s3`\. This behavior affects the signature of trailing checksum values\. For more information about S3 Object Lambda, see [Transforming objects with S3 Object Lambda](transforming-objects.md)\.

## Using part\-level checksums for multipart uploads<a name="large-object-checksums"></a>

When objects are uploaded to Amazon S3, they can either be uploaded as a single object or through the multipart upload process\. Objects that are larger than 16 MB and uploaded through the console are automatically uploaded using multipart uploads\. For more information about multipart uploads, see [Uploading and copying objects using multipart upload](mpuoverview.md)\.

When an object is uploaded as a multipart upload, the ETag for the object is not an MD5 digest of the entire object\. Amazon S3 calculates the MD5 digest of each individual part as it is uploaded\. The MD5 digests are used to determine the ETag for the final object\. Amazon S3 concatenates the bytes for the MD5 digests together and then calculates the MD5 digest of these concatenated values\. The final step for creating the ETag is when Amazon S3 adds a dash with the total number of parts to the end\. 

For example, consider an object uploaded with a multipart upload that has an ETag of `C9A5A6878D97B48CC965C1E41859F034-14`\. In this case, `C9A5A6878D97B48CC965C1E41859F034` is the MD5 digest of all the digests concatenated together\. The `-14` indicates that there are 14 parts associated with this object's multipart upload\.

If you've enabled additional checksum values for your multipart object, Amazon S3 calculates the checksum for each individual part by using the specified checksum algorithm\. The checksum for the completed object is calculated in the same way that Amazon S3 calculates the MD5 digest for the multipart upload\. You can use this checksum to verify the integrity of the object\. 

To retrieve information about the object, including how many parts make up the entire object, you can use the [GetObjectAttributes](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObjectAttributes.html) operation\. With additional checksums, you can also recover information for each individual part that includes each part's checksum value\. 

Alternatively, you can get an individual part's checksum by using the [GetObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObject.html) or [HeadObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_HeadObject.html) operation and specifying a part number or byte range that aligns to a single part\. With this method, you can use that checksum to validate the individual part without needing to wait for all of the parts to finish uploading before verifying the data integrity\. When you use this method, you can also request only the individual parts that failed the integrity test\.

Because of how Amazon S3 calculates the checksum for multipart objects, the checksum value for the object might change if you copy it\. If you're using an SDK or the REST API and you call [CopyObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_CopyObject.html), Amazon S3 copies any object up to the size limitations of the `CopyObject` API operation\. Amazon S3 does this copy as a single action, regardless of whether the object was uploaded in a single request or as part of a multipart upload\. With a copy command, the checksum of the object is a direct checksum of the full object\. If the object was originally uploaded using a multipart upload, then the checksum value changes even though the data has not\.

**Note**  
Objects that are larger than the size limitations of the `CopyObject` API operation must use multipart copy commands\.

**Important**  
When you perform some operations using the AWS Management Console, Amazon S3 uses a multipart upload if the object is greater than 16 MB in size\. In this case, the checksum is not a direct checksum of the full object, but rather a calculation based on the checksum values of each individual part\.   
For example, consider an object 100 MB in size that you uploaded as a single\-part direct upload using the REST API\. The checksum in this case is a checksum of the entire object\. If you later use the console to rename that object, copy it, change the storage class, or edit the metadata, Amazon S3 uses the multipart upload functionality to update the object\. As a result, Amazon S3 creates a new checksum value for the object that is calculated based on the checksum values of the individual parts\.  
**The preceding list of console operations is not a complete list of all the possible actions that you can take in the AWS Management Console that result in Amazon S3 updating the object using the multipart upload functionality\.** Keep in mind that whenever you use the console to act on objects over 16 MB in size, the checksum value might not be the checksum of the entire object\.