# Tutorial: Batch\-transcoding videos with S3 Batch Operations, AWS Lambda, and AWS Elemental MediaConvert<a name="tutorial-s3-batchops-lambda-mediaconvert-video"></a>

Video consumers use devices of all shapes, sizes, and vintages to enjoy media content\. This wide array of devices presents a challenge for content creators and distributors\. Instead of being in a one\-size\-fits\-all format, videos must be converted so that they can span a broad range of sizes, formats, and bitrates\. This conversion task is even more challenging when you have a large number of videos that must be converted\.

AWS offers you a method to build a scalable, distributed architecture that does the following: 
+ Ingests input videos
+ Processes the videos for playback on a wide range of devices
+ Stores the transcoded media files
+ Delivers the output media files to meet demand

When you have extensive video repositories stored in Amazon S3, you can transcode these videos from their source formats into multiple file types in the size, resolution, and format needed by a particular video player or device\. Specifically, [S3 Batch Operations](http://aws.amazon.com/s3/features/batch-operations) provides you with a solution to invoke AWS Lambda functions for existing input videos in an S3 source bucket\. Then, the Lambda functions call [AWS Elemental MediaConvert](http://aws.amazon.com/mediaconvert/) to perform large\-scale video transcoding tasks\. The converted output media files are stored in an S3 destination bucket\. 

![\[A batch-transcoding workflow diagram, showing the flow from the input videos in the S3 source bucket to the output media files in the S3 destination bucket.\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/batchops-example-image-global.png)

**Objective**  
In this tutorial, you learn how to set up S3 Batch Operations to invoke a Lambda function for batch\-transcoding of videos stored in an S3 source bucket\. The Lambda function calls MediaConvert to transcode the videos\. The outputs for each video in the S3 source bucket are as follows:
+ An [HTTP Live Streaming \(HLS\)](http://wikipedia.org/wiki/HTTP_Live_Streaming) adaptive bitrate stream for playback on devices of multiple sizes and varying bandwidths
+ An MP4 video file
+ Thumbnail images collected at intervals

**Topics**
+ [Prerequisites](#batchops-s3-prerequisites)
+ [Step 1: Create an S3 bucket for the output media files](#batchops-s3-step1)
+ [Step 2: Create an IAM role for MediaConvert](#batchops-s3-step2)
+ [Step 3: Create an IAM role for your Lambda function](#batchops-s3-step3)
+ [Step 4: Create a Lambda function for video transcoding](#batchops-s3-step4)
+ [Step 5: Configure Amazon S3 Inventory for your S3 source bucket](#batchops-s3-step5)
+ [Step 6: Create an IAM role for S3 Batch Operations](#batchops-s3-step6)
+ [Step 7: Create and run an S3 Batch Operations job](#batchops-s3-step7)
+ [Step 8: Check the output media files from your S3 destination bucket](#batchops-s3-step8)
+ [Step 9: Clean up](#batchops-s3-step9)
+ [Next steps](#batchops-s3-next-steps)

## Prerequisites<a name="batchops-s3-prerequisites"></a>

Before you start this tutorial, you must have an Amazon S3 source bucket \(for example, **tutorial\-bucket\-1**\) with videos to be transcoded already stored in it\.

You can give the bucket another name if you want\. For more information about bucket names in Amazon S3, see [Bucket naming rules](bucketnamingrules.md)\.

For the S3 source bucket, keep the settings related to **Block Public Access settings for this bucket** set to the defaults \(**Block *all* public access** is enabled\)\. For more information, see [Creating a bucket](create-bucket-overview.md)\. 

For more information about uploading videos to the S3 source bucket, see [Uploading objects](upload-objects.md)\. If you're uploading many large video files to S3, you might want to use [Amazon S3 Transfer Acceleration](http://aws.amazon.com/s3/transfer-acceleration) to configure fast and secure file transfers\. Transfer Acceleration can speed up video uploading to your S3 bucket for long\-distance transfer of larger videos\. For more information, see [Configuring fast, secure file transfers using Amazon S3 Transfer Acceleration](transfer-acceleration.md)\. 

## Step 1: Create an S3 bucket for the output media files<a name="batchops-s3-step1"></a>

In this step, you create an S3 destination bucket to store the converted output media files\. You also create a Cross Origin Resource Sharing \(CORS\) configuration to allow cross\-origin access to the transcoded media files stored in your S3 destination bucket\. 

**Topics**
+ [Create a bucket for the output media files](#batchops-s3-step1-create-bucket)
+ [Add a CORS configuration to the S3 output bucket](#batchops-s3-step1-cors)

### Create a bucket for the output media files<a name="batchops-s3-step1-create-bucket"></a>



1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Buckets**\.

1. Choose **Create bucket**\. 

1. For **Bucket name**, enter a name for your bucket \(for example, **tutorial\-bucket\-2**\)\. 

1. For **Region**, choose the AWS Region where you want the bucket to reside\. 

1. To ensure public access to your output media files, in **Block Public Access settings for this bucket**, clear **Block *all* public access**\. 
**Warning**  
Before you complete this step, review [Blocking public access to your Amazon S3 storage](access-control-block-public-access.md) to ensure that you understand and accept the risks involved with allowing public access\. When you turn off Block Public Access settings to make your bucket public, anyone on the internet can access your bucket\. We recommend that you block all public access to your buckets\.  
If you don’t want to clear the Block Public Access settings, you can use Amazon CloudFront to deliver the transcoded media files to viewers \(end users\)\. For more information, see [Tutorial: Hosting on\-demand streaming video with Amazon S3, Amazon CloudFront, and Amazon Route 53](tutorial-s3-cloudfront-route53-video-streaming.md)\. 

1. Select the check box next to **I acknowledge that the current settings might result in this bucket and the objects within becoming public\.**

1. Keep the remaining settings set to the defaults\. 

1. Choose **Create bucket**\.

### Add a CORS configuration to the S3 output bucket<a name="batchops-s3-step1-cors"></a>

A JSON CORS configuration defines a way for client web applications \(video players in this context\) that are loaded in one domain to play transcoded output media files in a different domain\. 

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Buckets**\.

1. In the **Buckets** list, choose the name of the bucket that you created earlier \(for example, **tutorial\-bucket\-2**\)\.

1. Choose the **Permissions** tab\.

1. In the **Cross\-origin resource sharing \(CORS\)** section, choose **Edit**\.

1. In the CORS configuration text box, copy and paste the following CORS configuration\.

   The CORS configuration must be in JSON format\. In this example, the `AllowedOrigins` attribute uses the wildcard character \(`*`\) to specify all origins\. If you know your specific origin, you can restrict the `AllowedOrigins` attribute to your specific player URL\. For more information about configuring this and other attributes, see [CORS configuration](ManageCorsUsing.md)\.

   ```
   [
       {
           "AllowedOrigins": [
               "*"
           ],
           "AllowedMethods": [
               "GET"
           ],
           "AllowedHeaders": [
               "*"
           ],
           "ExposeHeaders": []
   
       }
   ]
   ```

1. Choose **Save changes**\.

## Step 2: Create an IAM role for MediaConvert<a name="batchops-s3-step2"></a>

To use AWS Elemental MediaConvert to transcode input videos stored in your S3 bucket, you must have an AWS Identity and Access Management \(IAM\) service role to grant MediaConvert permissions to read and write video files from and to your S3 source and destination buckets\. When you run transcoding jobs, the MediaConvert console uses this role\. 

**To create an IAM role for MediaConvert**

1. Create an IAM role with a role name that you choose \(for example, **tutorial\-mediaconvert\-role**\)\. To create this role, follow the steps in [Create your MediaConvert role in IAM \(console\)](https://docs.aws.amazon.com/mediaconvert/latest/ug/creating-the-iam-role-in-iam.html) in the *AWS Elemental MediaConvert User Guide*\.

1. After you create the IAM role for MediaConvert, in the list of **Roles**, choose the name of the role for MediaConvert that you created \(for example, **tutorial\-mediaconvert\-role**\)\.

1. On the **Summary** page, copy the **Role ARN** \(which starts with `arn:aws:iam::`\), and save the ARN for use later\. 

   For more information about ARNs, see [Amazon Resource Names \(ARNs\)](https://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html) in the *AWS General Reference*\. 

## Step 3: Create an IAM role for your Lambda function<a name="batchops-s3-step3"></a>

To batch\-transcode videos with MediaConvert and S3 Batch Operations, you use a Lambda function to connect these two services to convert videos\. This Lambda function must have an IAM role that grants the Lambda function permissions to access MediaConvert and S3 Batch Operations\. 

**Topics**
+ [Create an IAM role for your Lambda function](#batchops-s3-step3-role)
+ [Embed an inline policy for the IAM role of your Lambda function](#batchops-s3-step3-inline-policy)

### Create an IAM role for your Lambda function<a name="batchops-s3-step3-role"></a>

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the left navigation pane, choose **Roles**, and then choose **Create role**\. 

1. Choose the **AWS service** role type, and then under **Common use cases**, choose **Lambda**\. 

1. Choose **Next: Permissions**\. 

1. On the **Attach permissions policies** page, enter **AWSLambdaBasicExecutionRole** in the **Filter policies** box\. To attach the managed policy **AWSLambdaBasicExecutionRole** to this role to grant write permissions to Amazon CloudWatch Logs, select the check box next to **AWSLambdaBasicExecutionRole**\.

1. Choose **Next: Tags**\.

1. \(Optional\) Add tags to the managed policy\.

1. Choose **Next: Review**\.

1. For **Role name**, enter **tutorial\-lambda\-transcode\-role**\.

1. Choose **Create role**\.

### Embed an inline policy for the IAM role of your Lambda function<a name="batchops-s3-step3-inline-policy"></a>

To grant permissions to the MediaConvert resource that's needed for the Lambda function to execute, you must use an inline policy\.

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the left navigation pane, choose **Roles**\. 

1. In the **Roles** list, choose the name of the IAM role that you created earlier for your Lambda function \(for example, **tutorial\-lambda\-transcode\-role**\)\.

1. Choose the **Permissions** tab\.

1. Choose **Add inline policy**\.

1. Choose the **JSON** tab, and then copy and paste the following JSON policy\.

   In the JSON policy, replace the example ARN value of `Resource` with the role ARN of the IAM role for MediaConvert that you created in [Step 2](#batchops-s3-step2) \(for example, **tutorial\-mediaconvert\-role**\)\.

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Action": [
                   "logs:CreateLogGroup",
                   "logs:CreateLogStream",
                   "logs:PutLogEvents"
               ],
               "Resource": "*",
               "Effect": "Allow",
               "Sid": "Logging"
           },
           {
               "Action": [
                   "iam:PassRole"
               ],
               "Resource": [
                   "arn:aws:iam::111122223333:role/tutorial-mediaconvert-role"
               ],
               "Effect": "Allow",
               "Sid": "PassRole"
           },
           {
               "Action": [
                   "mediaconvert:*"
               ],
               "Resource": [
                   "*"
               ],
               "Effect": "Allow",
               "Sid": "MediaConvertService"
           },
           {
               "Action": [
                   "s3:*"
               ],
               "Resource": [
                   "*"
               ],
               "Effect": "Allow",
               "Sid": "S3Service"
           }
       ]
   }
   ```

1. Choose **Review Policy**\.

1. For **Name**, enter **tutorial\-lambda\-policy**\.

1. Choose **Create Policy**\.

   After you create an inline policy, it is automatically embedded in the IAM role of your Lambda function\.

## Step 4: Create a Lambda function for video transcoding<a name="batchops-s3-step4"></a>

In this section of the tutorial, you build a Lambda function using the SDK for Python to integrate with S3 Batch Operations and MediaConvert\. To start transcoding the videos already stored in your S3 source bucket, you run an S3 Batch Operations job that directly invokes the Lambda function for each video in the S3 source bucket\. Then, the Lambda function submits a transcoding job for each video to MediaConvert\.

**Topics**
+ [Write Lambda function code and create a deployment package](#batchops-s3-step4-write-function)
+ [Create a Lambda function with an execution role \(console\)](#batchops-s3-step4-create-function)
+ [Deploy your Lambda function with \.zip file archives and configure the Lambda function \(console\)](#batchops-s3-step4-deploy-function)

### Write Lambda function code and create a deployment package<a name="batchops-s3-step4-write-function"></a>

1. On your local machine, create a folder named `batch-transcode`\.

1. In the `batch-transcode` folder, create a file with JSON job settings\. For example, you can use the settings provided in this section, and name the file `job.json`\. 

   A `job.json` file specifies the following: 
   + Which files to transcode
   + How you want to transcode your input videos
   + What output media files you want to create
   + What to name the transcoded files
   + Where to save the transcoded files
   + Which advanced features to apply, and so on

   In this tutorial, we use the following `job.json` file to create the following outputs for each video in the S3 source bucket:
   + An HTTP Live Streaming \(HLS\) adaptive bitrate stream for playback on multiple devices of differing sizes and varying bandwidths
   + An MP4 video file
   + Thumbnail images collected at intervals

   This example `job.json` file uses Quality\-Defined Variable Bitrate \(QVBR\) to optimize video quality\. The HLS output is Apple\-compliant \(audio unmixed from video, segment duration of 6 seconds, and optimized video quality through auto QVBR\)\. 

   If you don't want to use the example settings provided here, you can generate a `job.json` specification based on your use case\. To ensure consistency across your outputs, make sure that your input files have similar video and audio configurations\. For any input files with different video and audio configurations, create separate automations \(unique `job.json` settings\)\. For more information, see [Example AWS Elemental MediaConvert job settings in JSON](https://docs.aws.amazon.com/mediaconvert/latest/ug/example-job-settings.html) in the *AWS Elemental MediaConvert User Guide*\. 

   ```
   {
     "OutputGroups": [
       {
         "CustomName": "HLS",
         "Name": "Apple HLS",
         "Outputs": [
           {
             "ContainerSettings": {
               "Container": "M3U8",
               "M3u8Settings": {
                 "AudioFramesPerPes": 4,
                 "PcrControl": "PCR_EVERY_PES_PACKET",
                 "PmtPid": 480,
                 "PrivateMetadataPid": 503,
                 "ProgramNumber": 1,
                 "PatInterval": 0,
                 "PmtInterval": 0,
                 "TimedMetadata": "NONE",
                 "VideoPid": 481,
                 "AudioPids": [
                   482,
                   483,
                   484,
                   485,
                   486,
                   487,
                   488,
                   489,
                   490,
                   491,
                   492
                 ]
               }
             },
             "VideoDescription": {
               "Width": 640,
               "ScalingBehavior": "DEFAULT",
               "Height": 360,
               "TimecodeInsertion": "DISABLED",
               "AntiAlias": "ENABLED",
               "Sharpness": 50,
               "CodecSettings": {
                 "Codec": "H_264",
                 "H264Settings": {
                   "InterlaceMode": "PROGRESSIVE",
                   "NumberReferenceFrames": 3,
                   "Syntax": "DEFAULT",
                   "Softness": 0,
                   "GopClosedCadence": 1,
                   "GopSize": 2,
                   "Slices": 1,
                   "GopBReference": "DISABLED",
                   "MaxBitrate": 1200000,
                   "SlowPal": "DISABLED",
                   "SpatialAdaptiveQuantization": "ENABLED",
                   "TemporalAdaptiveQuantization": "ENABLED",
                   "FlickerAdaptiveQuantization": "DISABLED",
                   "EntropyEncoding": "CABAC",
                   "FramerateControl": "INITIALIZE_FROM_SOURCE",
                   "RateControlMode": "QVBR",
                   "CodecProfile": "MAIN",
                   "Telecine": "NONE",
                   "MinIInterval": 0,
                   "AdaptiveQuantization": "HIGH",
                   "CodecLevel": "AUTO",
                   "FieldEncoding": "PAFF",
                   "SceneChangeDetect": "TRANSITION_DETECTION",
                   "QualityTuningLevel": "SINGLE_PASS_HQ",
                   "FramerateConversionAlgorithm": "DUPLICATE_DROP",
                   "UnregisteredSeiTimecode": "DISABLED",
                   "GopSizeUnits": "SECONDS",
                   "ParControl": "INITIALIZE_FROM_SOURCE",
                   "NumberBFramesBetweenReferenceFrames": 2,
                   "RepeatPps": "DISABLED"
                 }
               },
               "AfdSignaling": "NONE",
               "DropFrameTimecode": "ENABLED",
               "RespondToAfd": "NONE",
               "ColorMetadata": "INSERT"
             },
             "OutputSettings": {
               "HlsSettings": {
                 "AudioGroupId": "program_audio",
                 "AudioRenditionSets": "program_audio",
                 "SegmentModifier": "$dt$",
                 "IFrameOnlyManifest": "EXCLUDE"
               }
             },
             "NameModifier": "_360"
           },
           {
             "ContainerSettings": {
               "Container": "M3U8",
               "M3u8Settings": {
                 "AudioFramesPerPes": 4,
                 "PcrControl": "PCR_EVERY_PES_PACKET",
                 "PmtPid": 480,
                 "PrivateMetadataPid": 503,
                 "ProgramNumber": 1,
                 "PatInterval": 0,
                 "PmtInterval": 0,
                 "TimedMetadata": "NONE",
                 "TimedMetadataPid": 502,
                 "VideoPid": 481,
                 "AudioPids": [
                   482,
                   483,
                   484,
                   485,
                   486,
                   487,
                   488,
                   489,
                   490,
                   491,
                   492
                 ]
               }
             },
             "VideoDescription": {
               "Width": 960,
               "ScalingBehavior": "DEFAULT",
               "Height": 540,
               "TimecodeInsertion": "DISABLED",
               "AntiAlias": "ENABLED",
               "Sharpness": 50,
               "CodecSettings": {
                 "Codec": "H_264",
                 "H264Settings": {
                   "InterlaceMode": "PROGRESSIVE",
                   "NumberReferenceFrames": 3,
                   "Syntax": "DEFAULT",
                   "Softness": 0,
                   "GopClosedCadence": 1,
                   "GopSize": 2,
                   "Slices": 1,
                   "GopBReference": "DISABLED",
                   "MaxBitrate": 3500000,
                   "SlowPal": "DISABLED",
                   "SpatialAdaptiveQuantization": "ENABLED",
                   "TemporalAdaptiveQuantization": "ENABLED",
                   "FlickerAdaptiveQuantization": "DISABLED",
                   "EntropyEncoding": "CABAC",
                   "FramerateControl": "INITIALIZE_FROM_SOURCE",
                   "RateControlMode": "QVBR",
                   "CodecProfile": "MAIN",
                   "Telecine": "NONE",
                   "MinIInterval": 0,
                   "AdaptiveQuantization": "HIGH",
                   "CodecLevel": "AUTO",
                   "FieldEncoding": "PAFF",
                   "SceneChangeDetect": "TRANSITION_DETECTION",
                   "QualityTuningLevel": "SINGLE_PASS_HQ",
                   "FramerateConversionAlgorithm": "DUPLICATE_DROP",
                   "UnregisteredSeiTimecode": "DISABLED",
                   "GopSizeUnits": "SECONDS",
                   "ParControl": "INITIALIZE_FROM_SOURCE",
                   "NumberBFramesBetweenReferenceFrames": 2,
                   "RepeatPps": "DISABLED"
                 }
               },
               "AfdSignaling": "NONE",
               "DropFrameTimecode": "ENABLED",
               "RespondToAfd": "NONE",
               "ColorMetadata": "INSERT"
             },
             "OutputSettings": {
               "HlsSettings": {
                 "AudioGroupId": "program_audio",
                 "AudioRenditionSets": "program_audio",
                 "SegmentModifier": "$dt$",
                 "IFrameOnlyManifest": "EXCLUDE"
               }
             },
             "NameModifier": "_540"
           },
           {
             "ContainerSettings": {
               "Container": "M3U8",
               "M3u8Settings": {
                 "AudioFramesPerPes": 4,
                 "PcrControl": "PCR_EVERY_PES_PACKET",
                 "PmtPid": 480,
                 "PrivateMetadataPid": 503,
                 "ProgramNumber": 1,
                 "PatInterval": 0,
                 "PmtInterval": 0,
                 "TimedMetadata": "NONE",
                 "VideoPid": 481,
                 "AudioPids": [
                   482,
                   483,
                   484,
                   485,
                   486,
                   487,
                   488,
                   489,
                   490,
                   491,
                   492
                 ]
               }
             },
             "VideoDescription": {
               "Width": 1280,
               "ScalingBehavior": "DEFAULT",
               "Height": 720,
               "TimecodeInsertion": "DISABLED",
               "AntiAlias": "ENABLED",
               "Sharpness": 50,
               "CodecSettings": {
                 "Codec": "H_264",
                 "H264Settings": {
                   "InterlaceMode": "PROGRESSIVE",
                   "NumberReferenceFrames": 3,
                   "Syntax": "DEFAULT",
                   "Softness": 0,
                   "GopClosedCadence": 1,
                   "GopSize": 2,
                   "Slices": 1,
                   "GopBReference": "DISABLED",
                   "MaxBitrate": 5000000,
                   "SlowPal": "DISABLED",
                   "SpatialAdaptiveQuantization": "ENABLED",
                   "TemporalAdaptiveQuantization": "ENABLED",
                   "FlickerAdaptiveQuantization": "DISABLED",
                   "EntropyEncoding": "CABAC",
                   "FramerateControl": "INITIALIZE_FROM_SOURCE",
                   "RateControlMode": "QVBR",
                   "CodecProfile": "MAIN",
                   "Telecine": "NONE",
                   "MinIInterval": 0,
                   "AdaptiveQuantization": "HIGH",
                   "CodecLevel": "AUTO",
                   "FieldEncoding": "PAFF",
                   "SceneChangeDetect": "TRANSITION_DETECTION",
                   "QualityTuningLevel": "SINGLE_PASS_HQ",
                   "FramerateConversionAlgorithm": "DUPLICATE_DROP",
                   "UnregisteredSeiTimecode": "DISABLED",
                   "GopSizeUnits": "SECONDS",
                   "ParControl": "INITIALIZE_FROM_SOURCE",
                   "NumberBFramesBetweenReferenceFrames": 2,
                   "RepeatPps": "DISABLED"
                 }
               },
               "AfdSignaling": "NONE",
               "DropFrameTimecode": "ENABLED",
               "RespondToAfd": "NONE",
               "ColorMetadata": "INSERT"
             },
             "OutputSettings": {
               "HlsSettings": {
                 "AudioGroupId": "program_audio",
                 "AudioRenditionSets": "program_audio",
                 "SegmentModifier": "$dt$",
                 "IFrameOnlyManifest": "EXCLUDE"
               }
             },
             "NameModifier": "_720"
           },
           {
             "ContainerSettings": {
               "Container": "M3U8",
               "M3u8Settings": {}
             },
             "AudioDescriptions": [
               {
                 "AudioSourceName": "Audio Selector 1",
                 "CodecSettings": {
                   "Codec": "AAC",
                   "AacSettings": {
                     "Bitrate": 96000,
                     "CodingMode": "CODING_MODE_2_0",
                     "SampleRate": 48000
                   }
                 }
               }
             ],
             "OutputSettings": {
               "HlsSettings": {
                 "AudioGroupId": "program_audio",
                 "AudioTrackType": "ALTERNATE_AUDIO_AUTO_SELECT_DEFAULT"
               }
             },
             "NameModifier": "_audio"
           }
         ],
         "OutputGroupSettings": {
           "Type": "HLS_GROUP_SETTINGS",
           "HlsGroupSettings": {
             "ManifestDurationFormat": "INTEGER",
             "SegmentLength": 6,
             "TimedMetadataId3Period": 10,
             "CaptionLanguageSetting": "OMIT",
             "Destination": "s3://EXAMPLE-BUCKET/HLS/",
             "DestinationSettings": {
               "S3Settings": {
                 "AccessControl": {
                   "CannedAcl": "PUBLIC_READ"
                 }
               }
             },
             "TimedMetadataId3Frame": "PRIV",
             "CodecSpecification": "RFC_4281",
             "OutputSelection": "MANIFESTS_AND_SEGMENTS",
             "ProgramDateTimePeriod": 600,
             "MinSegmentLength": 0,
             "DirectoryStructure": "SINGLE_DIRECTORY",
             "ProgramDateTime": "EXCLUDE",
             "SegmentControl": "SEGMENTED_FILES",
             "ManifestCompression": "NONE",
             "ClientCache": "ENABLED",
             "StreamInfResolution": "INCLUDE"
           }
         }
       },
       {
         "CustomName": "MP4",
         "Name": "File Group",
         "Outputs": [
           {
             "ContainerSettings": {
               "Container": "MP4",
               "Mp4Settings": {
                 "CslgAtom": "INCLUDE",
                 "FreeSpaceBox": "EXCLUDE",
                 "MoovPlacement": "PROGRESSIVE_DOWNLOAD"
               }
             },
             "VideoDescription": {
               "Width": 1280,
               "ScalingBehavior": "DEFAULT",
               "Height": 720,
               "TimecodeInsertion": "DISABLED",
               "AntiAlias": "ENABLED",
               "Sharpness": 100,
               "CodecSettings": {
                 "Codec": "H_264",
                 "H264Settings": {
                   "InterlaceMode": "PROGRESSIVE",
                   "ParNumerator": 1,
                   "NumberReferenceFrames": 3,
                   "Syntax": "DEFAULT",
                   "Softness": 0,
                   "GopClosedCadence": 1,
                   "HrdBufferInitialFillPercentage": 90,
                   "GopSize": 2,
                   "Slices": 2,
                   "GopBReference": "ENABLED",
                   "HrdBufferSize": 10000000,
                   "MaxBitrate": 5000000,
                   "ParDenominator": 1,
                   "EntropyEncoding": "CABAC",
                   "RateControlMode": "QVBR",
                   "CodecProfile": "HIGH",
                   "MinIInterval": 0,
                   "AdaptiveQuantization": "AUTO",
                   "CodecLevel": "AUTO",
                   "FieldEncoding": "PAFF",
                   "SceneChangeDetect": "ENABLED",
                   "QualityTuningLevel": "SINGLE_PASS_HQ",
                   "UnregisteredSeiTimecode": "DISABLED",
                   "GopSizeUnits": "SECONDS",
                   "ParControl": "SPECIFIED",
                   "NumberBFramesBetweenReferenceFrames": 3,
                   "RepeatPps": "DISABLED",
                   "DynamicSubGop": "ADAPTIVE"
                 }
               },
               "AfdSignaling": "NONE",
               "DropFrameTimecode": "ENABLED",
               "RespondToAfd": "NONE",
               "ColorMetadata": "INSERT"
             },
             "AudioDescriptions": [
               {
                 "AudioTypeControl": "FOLLOW_INPUT",
                 "AudioSourceName": "Audio Selector 1",
                 "CodecSettings": {
                   "Codec": "AAC",
                   "AacSettings": {
                     "AudioDescriptionBroadcasterMix": "NORMAL",
                     "Bitrate": 160000,
                     "RateControlMode": "CBR",
                     "CodecProfile": "LC",
                     "CodingMode": "CODING_MODE_2_0",
                     "RawFormat": "NONE",
                     "SampleRate": 48000,
                     "Specification": "MPEG4"
                   }
                 },
                 "LanguageCodeControl": "FOLLOW_INPUT",
                 "AudioType": 0
               }
             ]
           }
         ],
         "OutputGroupSettings": {
           "Type": "FILE_GROUP_SETTINGS",
           "FileGroupSettings": {
             "Destination": "s3://EXAMPLE-BUCKET/MP4/",
             "DestinationSettings": {
               "S3Settings": {
                 "AccessControl": {
                   "CannedAcl": "PUBLIC_READ"
                 }
               }
             }
           }
         }
       },
       {
         "CustomName": "Thumbnails",
         "Name": "File Group",
         "Outputs": [
           {
             "ContainerSettings": {
               "Container": "RAW"
             },
             "VideoDescription": {
               "Width": 1280,
               "ScalingBehavior": "DEFAULT",
               "Height": 720,
               "TimecodeInsertion": "DISABLED",
               "AntiAlias": "ENABLED",
               "Sharpness": 50,
               "CodecSettings": {
                 "Codec": "FRAME_CAPTURE",
                 "FrameCaptureSettings": {
                   "FramerateNumerator": 1,
                   "FramerateDenominator": 5,
                   "MaxCaptures": 500,
                   "Quality": 80
                 }
               },
               "AfdSignaling": "NONE",
               "DropFrameTimecode": "ENABLED",
               "RespondToAfd": "NONE",
               "ColorMetadata": "INSERT"
             }
           }
         ],
         "OutputGroupSettings": {
           "Type": "FILE_GROUP_SETTINGS",
           "FileGroupSettings": {
             "Destination": "s3://EXAMPLE-BUCKET/Thumbnails/",
             "DestinationSettings": {
               "S3Settings": {
                 "AccessControl": {
                   "CannedAcl": "PUBLIC_READ"
                 }
               }
             }
           }
         }
       }
     ],
     "AdAvailOffset": 0,
     "Inputs": [
       {
         "AudioSelectors": {
           "Audio Selector 1": {
             "Offset": 0,
             "DefaultSelection": "DEFAULT",
             "ProgramSelection": 1
           }
         },
         "VideoSelector": {
           "ColorSpace": "FOLLOW"
         },
         "FilterEnable": "AUTO",
         "PsiControl": "USE_PSI",
         "FilterStrength": 0,
         "DeblockFilter": "DISABLED",
         "DenoiseFilter": "DISABLED",
         "TimecodeSource": "EMBEDDED",
         "FileInput": "s3://EXAMPLE-INPUT-BUCKET/input.mp4"
       }
     ]
   }
   ```

1. In the `batch-transcode` folder, create a file with a Lambda function\. You can use the following Python example and name the file `convert.py`\.

   S3 Batch Operations sends specific task data to a Lambda function and requires result data back\. For request and response examples for the Lambda function, information about response and result codes, and example Lambda functions for S3 Batch Operations, see [Invoke AWS Lambda function](batch-ops-invoke-lambda.md)\. 

   ```
   import json
   import os
   from urllib.parse import urlparse
   import uuid
   import boto3
   
   """
   When you run an S3 Batch Operations job, your job  
   invokes this Lambda function. Specifically, the Lambda function is 
   invoked on each video object listed in the manifest that you specify 
   for the S3 Batch Operations job in Step 5.  
   
   Input parameter "event": The S3 Batch Operations event as a request
                            for the Lambda function.
   
   Input parameter "context": Context about the event.
   
   Output: A result structure that Amazon S3 uses to interpret the result 
           of the operation. It is a job response returned back to S3 Batch Operations.
   """
   def handler(event, context):
   
       invocation_schema_version = event['invocationSchemaVersion']
       invocation_id = event['invocationId']
       task_id = event['tasks'][0]['taskId']
   
       source_s3_key = event['tasks'][0]['s3Key']
       source_s3_bucket = event['tasks'][0]['s3BucketArn'].split(':::')[-1]
       source_s3 = 's3://' + source_s3_bucket + '/' + source_s3_key
   
       result_list = []
       result_code = 'Succeeded'
       result_string = 'The input video object was converted successfully.'
   
       # The type of output group determines which media players can play 
       # the files transcoded by MediaConvert.
       # For more information, see [Creating outputs with AWS Elemental MediaConvert](https://docs.aws.amazon.com/mediaconvert/latest/ug/creating-streaming-and-file-outputs.html).
       output_group_type_dict = {
           'HLS_GROUP_SETTINGS': 'HlsGroupSettings',
           'FILE_GROUP_SETTINGS': 'FileGroupSettings',
           'CMAF_GROUP_SETTINGS': 'CmafGroupSettings',
           'DASH_ISO_GROUP_SETTINGS': 'DashIsoGroupSettings',
           'MS_SMOOTH_GROUP_SETTINGS': 'MsSmoothGroupSettings'
       }
   
       try:
           job_name = 'Default'
           with open('job.json') as file:
               job_settings = json.load(file)
   
           job_settings['Inputs'][0]['FileInput'] = source_s3
   
           # The path of each output video is constructed based on the values of 
           # the attributes in each object of OutputGroups in the job.json file. 
           destination_s3 = 's3://{0}/{1}/{2}' \
               .format(os.environ['DestinationBucket'],
                       os.path.splitext(os.path.basename(source_s3_key))[0],
                       os.path.splitext(os.path.basename(job_name))[0])
   
           for output_group in job_settings['OutputGroups']:
               output_group_type = output_group['OutputGroupSettings']['Type']
               if output_group_type in output_group_type_dict.keys():
                   output_group_type = output_group_type_dict[output_group_type]
                   output_group['OutputGroupSettings'][output_group_type]['Destination'] = \
                       "{0}{1}".format(destination_s3,
                                       urlparse(output_group['OutputGroupSettings'][output_group_type]['Destination']).path)
               else:
                   raise ValueError("Exception: Unknown Output Group Type {}."
                                    .format(output_group_type))
   
           job_metadata_dict = {
               'assetID': str(uuid.uuid4()),
               'application': os.environ['Application'],
               'input': source_s3,
               'settings': job_name
           }
   
           region = os.environ['AWS_DEFAULT_REGION']
           endpoints = boto3.client('mediaconvert', region_name=region) \
               .describe_endpoints()
           client = boto3.client('mediaconvert', region_name=region, 
                                  endpoint_url=endpoints['Endpoints'][0]['Url'], 
                                  verify=False)
   
           try:
               client.create_job(Role=os.environ['MediaConvertRole'], 
                                 UserMetadata=job_metadata_dict, 
                                 Settings=job_settings)
           # You can customize error handling based on different error codes that 
           # MediaConvert can return.
           # For more information, see [MediaConvert error codes](https://docs.aws.amazon.com/mediaconvert/latest/ug/mediaconvert_error_codes.html). 
           # When the result_code is TemporaryFailure, S3 Batch Operations retries 
           # the task before the job is completed. If this is the final retry, 
           # the error message is included in the final report.
           except Exception as error:
               result_code = 'TemporaryFailure'
               raise
       
       except Exception as error:
           if result_code != 'TemporaryFailure':
               result_code = 'PermanentFailure'
           result_string = str(error)
   
       finally:
           result_list.append({
               'taskId': task_id,
               'resultCode': result_code,
               'resultString': result_string,
           })
   
       return {
           'invocationSchemaVersion': invocation_schema_version,
           'treatMissingKeyAs': 'PermanentFailure',
           'invocationId': invocation_id,
           'results': result_list
       }
   ```

1. To create a deployment package with `convert.py` and `job.json` as a `.zip` file named `lambda.zip`, in your local terminal, open the `batch-transcode` folder that you created earlier, and run the following command\.

   For **macOS users**, run the following command:

   ```
   zip -r lambda.zip convert.py job.json                
   ```

   For **Windows users**, run the following commands:

   ```
   powershell Compress-Archive convert.py lambda.zip
   ```

   ```
   powershell Compress-Archive -update job.json lambda.zip                
   ```

### Create a Lambda function with an execution role \(console\)<a name="batchops-s3-step4-create-function"></a>

1. 

   Open the AWS Lambda console at [https://console\.aws\.amazon\.com/lambda/](https://console.aws.amazon.com/lambda/)\.

1. In the left navigation pane, choose **Functions**\.

1. Choose **Create function**\.

1. Choose **Author from scratch**\.

1. Under **Basic information**, do the following:

   1. For **Function name**, enter **tutorial\-lambda\-convert**\.

   1. For **Runtime**, choose **Python 3\.8** or a later version of Python\.

1. Choose **Change default execution role**, and under **Execution role**, choose **Use an existing role**\.

1. Under **Existing role**, choose the name of the IAM role that you created for your Lambda function in [Step 3](#batchops-s3-step3) \(for example, **tutorial\-lambda\-transcode\-role**\)\.

1. For the remaining settings, keep the defaults\.

1. Choose **Create function**\.

### Deploy your Lambda function with \.zip file archives and configure the Lambda function \(console\)<a name="batchops-s3-step4-deploy-function"></a>

1. In the **Code Source** section of the page for the Lambda function that you created \(for example, **tutorial\-lambda\-convert**\), choose **Upload from** and then **\.zip file**\.

1. Choose **Upload** to select your local `.zip` file\.

1. Choose the `lambda.zip` file that you created earlier, and choose **Open**\.

1. Choose **Save**\.

1. In the **Runtime settings** section, choose **Edit**\. 

1. To tell the Lambda runtime which handler method in your Lambda function code to invoke, enter **convert\.handler** in the **Handler** field\.

   When you configure a function in Python, the value of the handler setting is the file name and the name of the handler module, separated by a dot \(`.`\)\. For example, `convert.handler` calls the `handler` method defined in the `convert.py` file\.

1. Choose **Save**\.

1. On your Lambda function page, choose the **Configuration** tab\. In the left navigation pane on the **Configuration** tab, choose **Environment variables**, and then choose **Edit**\.

1. Choose **Add environment variable**\. Then, enter the specified **Key** and **Value** for each of the following environment variables:
   + **Key**: **DestinationBucket** **Value**: **tutorial\-bucket\-2** 

     This value is the S3 bucket for output media files that you created in [Step 1](#batchops-s3-step1)\.
   + **Key**: **MediaConvertRole** **Value**: **arn:aws:iam::*111122223333*:role/tutorial\-mediaconvert\-role** 

     This value is the ARN of the IAM role for MediaConvert that you created in [Step 2](#batchops-s3-step2)\. Make sure to replace this ARN with the actual ARN of your IAM role\.
   + **Key**: **Application** **Value**: **Batch\-Transcoding** 

     This value is the name of the application\.

1. Choose **Save**\.

1. \(Optional\) On the **Configuration** tab, in the **General configuration** section of the left navigation pane, choose **Edit**\. In the **Timeout** field, enter **2** min **0** sec\. Then, choose **Save**\.

   **Timeout** is the amount of time that Lambda allows a function to run for an invocation before stopping it\. The default is 3 seconds\. Pricing is based on the amount of memory configured and the amount of time that your code runs\. For more information, see [AWS Lambda pricing](http://aws.amazon.com/lambda/pricing/?icmpid=docs_console_unmapped)\.

## Step 5: Configure Amazon S3 Inventory for your S3 source bucket<a name="batchops-s3-step5"></a>

After setting up the transcoding Lambda function, create an S3 Batch Operations job to transcode a set of videos\. First, you need a list of input video objects that you want S3 Batch Operations to run the specified transcoding action on\. To get a list of input video objects, you can generate an S3 Inventory report for your S3 source bucket \(for example, **tutorial\-bucket\-1**\)\. 

**Topics**
+ [Create and configure a bucket for S3 Inventory reports for input videos](#batchops-s3-step5-bucket)
+ [Configure Amazon S3 Inventory for your S3 video source bucket](#batchops-s3-step5-inventory)
+ [Check the inventory report for your S3 video source bucket](#batchops-s3-step5-manifest)

### Create and configure a bucket for S3 Inventory reports for input videos<a name="batchops-s3-step5-bucket"></a>

To store an S3 Inventory report that lists the objects of the S3 source bucket, create an S3 Inventory destination bucket, and then configure a bucket policy for the bucket to write inventory files to the S3 source bucket\.

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Buckets**\.

1. Choose **Create bucket**\. 

1. For **Bucket name**, enter a name for your bucket \(for example, **tutorial\-bucket\-3**\)\. 

1. For **AWS Region**, choose the AWS Region where you want the bucket to reside\. 

   The inventory destination bucket must be in the same AWS Region as the source bucket where you are setting up S3 Inventory\. The inventory destination bucket can be in a different AWS account\. 

1. In **Block Public Access settings for this bucket**, keep the default settings \(**Block *all *public access** is enabled\)\. 

1. For the remaining settings, keep the defaults\. 

1. Choose **Create bucket**\.

1. In the **Buckets** list, choose the name of the bucket that you just created \(for example, **tutorial\-bucket\-3**\)\.

1. To grant Amazon S3 permission to write data for the inventory reports to the S3 Inventory destination bucket, choose the **Permissions** tab\.

1. Scroll down to the **Bucket policy** section, and choose **Edit**\. The **Bucket policy** page opens\.

1. To grant permissions for S3 Inventory, in the **Policy** field, paste the following bucket policy\.

   Replace the three example values with the following values: 
   + The name of the bucket that you created to store the inventory reports \(for example, `tutorial-bucket-3`\)\.
   + The name of the source bucket that stores the input videos \(for example, `tutorial-bucket-1`\)\. 
   + The AWS account ID that you used to create the S3 video source bucket \(for example, `111122223333`\.

   ```
   {
     "Version":"2012-10-17",
     "Statement":[
       {
         "Sid":"InventoryAndAnalyticsExamplePolicy",
         "Effect":"Allow",
         "Principal": {"Service": "s3.amazonaws.com"},
         "Action":"s3:PutObject",
         "Resource":["arn:aws:s3:::tutorial-bucket-3/*"],
         "Condition": {
             "ArnLike": {
                 "aws:SourceArn": "arn:aws:s3:::tutorial-bucket-1"
              },
            "StringEquals": {
                "aws:SourceAccount": "111122223333",
                "s3:x-amz-acl": "bucket-owner-full-control"
             }
          }
       }
     ]
   }
   ```

1. Choose **Save changes**\.

### Configure Amazon S3 Inventory for your S3 video source bucket<a name="batchops-s3-step5-inventory"></a>

To generate a flat file list of video objects and metadata, you must configure S3 Inventory for your S3 video source bucket\. These scheduled inventory reports can include all the objects in the bucket or objects grouped by a shared prefix\. In this tutorial, the S3 Inventory report includes all the video objects in your S3 source bucket\.

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Buckets**\.

1. To configure an S3 Inventory report of the input videos in your S3 source bucket, in the **Buckets** list, choose the name of the S3 source bucket \(for example, **tutorial\-bucket\-1**\)\.

1. Choose the **Management** tab\.

1. Scroll down to the **Inventory configurations** section, and choose **Create inventory configuration**\. 

1. For **Inventory configuration name**, enter a name \(for example, **tutorial\-inventory\-config**\)\. 

1. Under **Inventory scope**, choose **Current version only** for **Object versions** and keep the other **Inventory scope** settings set to the defaults for this tutorial\. 

1. In the **Report details** section, for **Destination bucket**, choose **This account**\. 

1. For **Destination**, choose **Browse S3**, and choose the destination bucket that you created earlier to save the inventory reports to \(for example, **tutorial\-bucket\-3**\)\. Then choose **Choose path**\. 

   The inventory destination bucket must be in the same AWS Region as the source bucket where you are setting up S3 Inventory\. The inventory destination bucket can be in a different AWS account\. 

   Under the **Destination** bucket field, the **Destination bucket permission** is added to the inventory destination bucket policy, allowing Amazon S3 to place data in the inventory destination bucket\. For more information, see [Creating a destination bucket policy](configure-inventory.md#configure-inventory-destination-bucket-policy)\.

1. For **Frequency**, choose **Daily**\.

1. For **Output format**, choose **CSV**\. 

1. For **Status**, choose **Enable**\. 

1. In the **Server\-side encryption** section, choose **Disable** for this tutorial\. 

   For more information, see [Configuring inventory by using the S3 console](configure-inventory.md#configure-inventory-console) and [Granting Amazon S3 permission to use your customer managed key for encryption](configure-inventory.md#configure-inventory-kms-key-policy)\. 

1. In the **Additional fields \- *optional*** section, select **Size**, **Last modified**, and **Storage class**\. 

1. Choose **Create**\.

For more information, see [Configuring inventory by using the S3 console](configure-inventory.md#configure-inventory-console)\.

### Check the inventory report for your S3 video source bucket<a name="batchops-s3-step5-manifest"></a>

When an inventory report is published, the manifest files are sent to the S3 Inventory destination bucket\.

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Buckets**\.

1. In the **Buckets** list, choose the name of the video source bucket \(for example, **tutorial\-bucket\-1**\)\.

1. Choose **Management**\.

1. To see if your S3 Inventory report is ready so that you can create an S3 Batch Operations job in [Step 7](#batchops-s3-step7), under **Inventory configurations**, check whether the **Create job from manifest** button is enabled\.
**Note**  
It can take up to 48 hours to deliver the first inventory report\. If the **Create job from manifest** button is disabled, the first inventory report has not been delivered\. Wait until the first inventory report is delivered and the **Create job from manifest** button is enabled before you create an S3 Batch Operations job in [Step 7](#batchops-s3-step7)\. 

1. To check an S3 Inventory report \(`manifest.json`\), in the **Destination** column, choose the name of the inventory destination bucket that you created earlier for storing inventory reports \(for example, **tutorial\-bucket\-3**\)\.

1. On the **Objects** tab, choose the existing folder with the name of your S3 source bucket \(for example, **tutorial\-bucket\-1**\)\. Then choose the name that you entered in **Inventory configuration name** when you created the inventory configuration earlier \(for example, **tutorial\-inventory\-config**\)\.

   You can see a list of folders with the generation dates of the reports as their names\. 

1. To check the daily S3 Inventory report for a particular date, choose the folder with the corresponding generation date name, and then choose `manifest.json`\. 

1. To check the details of the inventory report on a specific date, on the **manifest\.json** page, choose **Download** or **Open**\.

## Step 6: Create an IAM role for S3 Batch Operations<a name="batchops-s3-step6"></a>

To use S3 Batch Operations to do batch\-transcoding, you must first create an IAM role to give Amazon S3 permissions to perform S3 Batch Operations\. 

**Topics**
+ [Create an IAM policy for S3 Batch Operations](#batchops-s3-step6-policy)
+ [Create an S3 Batch Operations IAM role and attach permissions policies](#batchops-s3-step6-role)

### Create an IAM policy for S3 Batch Operations<a name="batchops-s3-step6-policy"></a>

You must create an IAM policy that gives S3 Batch Operations permission to read the input manifest, invoke the Lambda function, and write the S3 Batch Operations job completion report\. 

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the left navigation pane, choose **Policies**\.

1. Choose **Create policy**\.

1. Choose the **JSON** tab\.

1. In the **JSON** text field, paste the following JSON policy\.

   In the JSON policy, replace the four example values with the following values:
   + The name of the source bucket that stores your input videos \(for example, `tutorial-bucket-1`\)\.
   + The name of the inventory destination bucket that you created in [Step 5](#batchops-s3-step5) to store `manifest.json` files \(for example, `tutorial-bucket-3`\)\.
   + The name of the bucket that you created in [Step 1](#batchops-s3-step1) to store output media files \(for example, `tutorial-bucket-2`\)\. In this tutorial, we put job completion reports in the destination bucket for output media files\. 
   + The role ARN of the Lambda function that you created in [Step 4](#batchops-s3-step4)\. To find and copy the role ARN of the Lambda function, do the following: 
     + In a new browser tab, open the **Functions** page on the Lambda console at [https://console.aws.amazon.com/lambda/home#/functions](https://console.aws.amazon.com/lambda/home#/functions)\.
     + In **Functions** list, choose the name of the Lambda function that you created in [Step 4](#batchops-s3-step4) \(for example, **tutorial\-lambda\-convert**\)\.
     + Choose **Copy ARN**\.

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Sid": "S3Get",
               "Effect": "Allow",
               "Action": [
                   "s3:GetObject",
                   "s3:GetObjectVersion"
               ],
               "Resource": [
                   "arn:aws:s3:::tutorial-bucket-1/*",
                   "arn:aws:s3:::tutorial-bucket-3/*"
               ]
           },
           {
               "Sid": "S3PutJobCompletionReport",
               "Effect": "Allow",
               "Action": "s3:PutObject",
               "Resource": "arn:aws:s3:::tutorial-bucket-2/*"
           },
           {
               "Sid": "S3BatchOperationsInvokeLambda",
               "Effect": "Allow",
               "Action": [
                   "lambda:InvokeFunction"
               ],
               "Resource": [
                   "arn:aws:lambda:us-west-2:111122223333:function:tutorial-lambda-convert"
               ]
           }
       ]
   }
   ```

1. Choose **Next: Tags**\.

1. Choose **Next: Review**\.

1. In the **Name** field, enter **tutorial\-s3batch\-policy**\.

1. Choose **Create policy**\.

### Create an S3 Batch Operations IAM role and attach permissions policies<a name="batchops-s3-step6-role"></a>

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the left navigation pane, choose **Roles**, and then choose **Create role**\.

1. Choose the **AWS service** role type, and then choose the **S3** service\.

1. Under **Select your use case**, choose **S3 Batch Operations**\.

1. Choose **Next: Permissions**\.

1. Under **Attach permissions policies**, enter the name of the IAM policy that you created earlier \(for example, **tutorial\-s3batch\-policy**\) in the search box to filter the list of policies\. Select the check box next to the name of the policy \(for example, **tutorial\-s3batch\-policy**\)\. 

1. Choose **Next: Tags**\.

1. Choose **Next: Review**\.

1. For **Role name**, enter **tutorial\-s3batch\-role**\.

1. Choose **Create role**\.

   After you create the IAM role for S3 Batch Operations, the following trust policy is automatically attached to the role\. This trust policy allows the S3 Batch Operations service principal to assume the IAM role\.

   ```
   {
      "Version":"2012-10-17",
      "Statement":[
         {
            "Effect":"Allow",
            "Principal":{
               "Service":"batchoperations.s3.amazonaws.com"
            },
            "Action":"sts:AssumeRole"
         }
      ]
   }
   ```

## Step 7: Create and run an S3 Batch Operations job<a name="batchops-s3-step7"></a>

To create an S3 Batch Operations job to process the input videos in your S3 source bucket, you must specify parameters for this particular job\.

**Note**  
Before you start creating an S3 Batch Operations job, make sure that the **Create job from manifest** button is enabled\. For more information, see [Check the inventory report for your S3 video source bucket](#batchops-s3-step5-manifest)\. If the **Create job from manifest** button is disabled, the first inventory report has not been delivered and you must wait until the button is enabled\. After you configure Amazon S3 Inventory for your S3 source bucket in [Step 5](#batchops-s3-step5), it can take up to 48 hours to deliver the first inventory report\.

**Topics**
+ [Create an S3 Batch Operations job](#batchops-s3-step7-create-job)
+ [Run the S3 Batch Operations job to invoke your Lambda function](#batchops-s3-step7-run-job)
+ [\(Optional\) Check your completion report](#batchops-s3-step7-check-report)
+ [\(Optional\) Monitor each Lambda invocation in the Lambda console](#batchops-s3-step7-monitor-lambda)
+ [\(Optional\) Monitor each MediaConvert video\-transcoding job in the MediaConvert console](#batchops-s3-step7-monitor-mediaconvert)

### Create an S3 Batch Operations job<a name="batchops-s3-step7-create-job"></a>

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Batch Operations**\.

1. Choose **Create job**\.

1. For **AWS Region**, choose the Region where you want to create your job\.

   In this tutorial, to use the S3 Batch Operations job to invoke a Lambda function, you must create the job in the same Region as the S3 video source bucket where the objects referenced in the manifest are located\.

1. In the **Manifest** section, do the following:

   1. For **Manifest format**, choose **S3 Inventory report \(manifest\.json\)**\.

   1. For **Manifest object**, choose **Browse S3** to find the bucket that you created in [Step 5](#batchops-s3-step5) for storing inventory reports \(for example, **tutorial\-bucket\-3**\)\. On the **Manifest object ** page, navigate through the object names until you find a `manifest.json` file for a specific date\. This file lists the information about all the videos that you want to batch\-transcode\. When you've found the `manifest.json` file that you want to use, choose the option button next to it\. Then choose **Choose path**\.

   1. \(Optional\) For **Manifest object version ID \- *optional***, enter the version ID for the manifest object if you want to use a version other than the most recent\.

1. Choose **Next**\.

1. To use the Lambda function to transcode all the objects listed in the selected `manifest.json` file, under **Operation type**, choose **Invoke AWS Lambda function**\.

1. In the **Invoke Lambda function** section, do the following:

   1. Choose **Choose from functions in your account**\.

   1. For **Lambda function**, choose the Lambda function that you created in [Step 4](#batchops-s3-step4) \(for example, **tutorial\-lambda\-convert**\)\.

   1. For **Lambda function version**, keep the default value **$LATEST**\.

1. Choose **Next**\. The **Configure additional options** page opens\.

1. In the **Additional options** section, keep the default settings\.

   For more information about these options, see [Batch Operations job request elements](batch-ops-create-job.md#batch-ops-create-job-request-elements)\.

1. In the **Completion report** section, for **Path to completion report destination**, choose **Browse S3**\. Find the bucket that you created for output media files in [Step 1](#batchops-s3-step1) \(for example, **tutorial\-bucket\-2**\)\. Choose the option button next to that bucket's name\. Then choose **Choose path**\.

   For the remaining **Completion report** settings, keep the defaults\. For more information about completion report settings, see [Batch Operations job request elements](batch-ops-create-job.md#batch-ops-create-job-request-elements)\. A completion report maintains a record of the job's details and the operations performed\.

1. In the **Permissions** section, choose **Choose from existing IAM roles**\. For **IAM role**, choose the IAM role for your S3 Batch Operations job that you created in [Step 6](#batchops-s3-step6) \(for example, **tutorial\-s3batch\-role**\)\.

1. Choose **Next**\.

1. On the **Review** page, review the settings\. Then choose **Create job**\.

   After S3 finishes reading your S3 Batch Operations job's manifest, it sets the **Status** of the job to **Awaiting your confirmation to run**\. To see updates to the job's status, refresh the page\. You can't run your job until its status is **Awaiting your confirmation to run**\.

### Run the S3 Batch Operations job to invoke your Lambda function<a name="batchops-s3-step7-run-job"></a>

Run your Batch Operations job to invoke your Lambda function for video transcoding\. If your job fails, you can check your completion report to identify the cause\.

**To run the S3 Batch Operations job**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Batch Operations**\.

1. In the **Jobs** list, choose the **Job ID** of the job on the first row, which is the S3 Batch Operations job that you created earlier\.

1. Choose **Run job**\. 

1. Review your job parameters again, and confirm that the value for **Total objects listed in manifest** is the same as the number of objects in the manifest\. Then choose **Run job**\.

   Your S3 Batch Operations job page opens\.

1. After the job starts running, on your job page, under **Status**, check the progress of your S3 Batch Operations job, such as **Status**, **% Complete**, **Total succeeded \(rate\)**, **Total failed \(rate\)**, **Date terminated**, and **Reason for termination**\.

   When the S3 Batch Operations job completes, view the data on your job page to confirm that the job finished as expected\. 

   If more than 50 percent of an S3 Batch Operations job's object operations fail after more than 1,000 operations have been attempted, the job automatically fails\. To check your completion report to identify the cause of the failures, use the following optional procedure\.

### \(Optional\) Check your completion report<a name="batchops-s3-step7-check-report"></a>

You can use your completion report to determine which objects failed and the cause of the failures\.

**To check your completion report for details about failed objects**

1. On the page of your S3 Batch Operations job, scroll down to the **Completion report** section, and choose the link under **Completion report destination**\.

   The S3 output destination bucket's page opens\.

1. On the **Objects** tab, choose the folder that has a name ending with the job ID of the S3 Batch Operations job that you created earlier\.

1. Choose **results/**\.

1. Select the check box next to the `.csv` file\.

1. To view the job report, choose **Open** or **Download**\.

### \(Optional\) Monitor each Lambda invocation in the Lambda console<a name="batchops-s3-step7-monitor-lambda"></a>

After the S3 Batch Operations job starts running, the job invokes the Lambda function for each input video object\. S3 writes logs of each Lambda invocation to CloudWatch Logs\. You can use the Lambda console's monitoring dashboard to monitor your Lambda function\. 

1. ****

   Open the AWS Lambda console at [https://console\.aws\.amazon\.com/lambda/](https://console.aws.amazon.com/lambda/)\.

1. In the left navigation pane, choose **Functions**\.

1. In the **Functions** list, choose the name of the Lambda function that you created in [Step 4](#batchops-s3-step4) \(for example, **tutorial\-lambda\-convert**\)\.

1. Choose the **Monitor** tab\.

1. Under **Metrics**, see the runtime metrics for your Lambda function\.

1. Under **Logs**, view log data for each Lambda invocation through CloudWatch Logs Insights\.
**Note**  
When you use S3 Batch Operations with a Lambda function, the Lambda function is invoked on each object\. If your S3 Batch Operations job is large, it can invoke multiple Lambda functions at the same time, causing a spike in Lambda concurrency\.   
Each AWS account has a Lambda concurrency quota per Region\. For more information, see [AWS Lambda Function Scaling](https://docs.aws.amazon.com/lambda/latest/dg/invocation-scaling.html) in the *AWS Lambda Developer Guide*\. A best practice for using Lambda functions with S3 Batch Operations is to set a concurrency limit on the Lambda function itself\. Setting a concurrency limit keeps your job from consuming most of your Lambda concurrency and potentially throttling other functions in your account\. For more information, see [Managing Lambda reserved concurrency](https://docs.aws.amazon.com/lambda/latest/dg/configuration-concurrency.html) in the *AWS Lambda Developer Guide*\.

### \(Optional\) Monitor each MediaConvert video\-transcoding job in the MediaConvert console<a name="batchops-s3-step7-monitor-mediaconvert"></a>

A MediaConvert job does the work of transcoding a media file\. When your S3 Batch Operations job invokes your Lambda function for each video, each Lambda function invocation creates a MediaConvert transcoding job for each input video\. 

1. Sign in to the AWS Management Console and open the MediaConvert console at [https://console\.aws\.amazon\.com/mediaconvert/](https://console.aws.amazon.com/mediaconvert/)\.

1. If the MediaConvert introductory page appears, choose **Get started**\.

1. From the list of **Jobs**, view each row to monitor the transcoding task for each input video\. 

1. Identify the row of a job that you want to check, and choose the **Job ID** link to open the job details page\.

1. On the **Job summary** page, under **Outputs**, choose the link for the HLS, MP4, or Thumbnails output, depending on what is supported by your browser, to go to the S3 destination bucket for the output media files\.

1. In the corresponding folder \(HLS, MP4, or Thumbnails\) of your S3 output destination bucket, choose the name of the output media file object\.

   The object's detail page opens\.

1. On the object's detail page, under **Object overview**, choose the link under **Object URL** to watch the transcoded output media file\.

## Step 8: Check the output media files from your S3 destination bucket<a name="batchops-s3-step8"></a>

**To check the output media files from your S3 destination bucket**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Buckets**\.

1. In the **Buckets** list, choose the name of the S3 destination bucket for output media files that you created in [Step 1](#batchops-s3-step1) \(for example, **tutorial\-bucket\-2**\)\.

1. On the **Objects** tab, each input video has a folder that has the name of the input video\. Each folder contains the transcoded output media files for an input video\.

   To check the output media files for an input video, do the following:

   1. Choose the folder with the name of the input video that you want to check\.

   1. Choose the **Default/** folder\.

   1. Choose the folder for a transcoded format \(HLS, MP4, or thumbnails in this tutorial\)\. 

   1. Choose the name of the output media file\.

   1. To watch the transcoded file, on the object's details page, choose the link under **Object URL**\. 

      Output media files in the HLS format are split into short segments\. To play these videos, embed the object URL of the `.m3u8` file in a compatible player\.

## Step 9: Clean up<a name="batchops-s3-step9"></a>

If you transcoded videos using S3 Batch Operations, Lambda, and MediaConvert only as a learning exercise, delete the AWS resources that you allocated so that you no longer accrue charges\. 

**Topics**
+ [Delete the S3 Inventory configuration for your S3 source bucket](#batchops-s3-step9-delete-inventory)
+ [Delete the Lambda function](#batchops-s3-step9-delete-lambda)
+ [Delete the CloudWatch log group](#batchops-s3-step9-delete-cw)
+ [Delete the IAM roles together with the inline policies for the IAM roles](#batchops-s3-step9-delete-role)
+ [Delete the customer\-managed IAM policy](#batchops-s3-step9-delete-policy)
+ [Empty the S3 buckets](#batchops-s3-step9-empty-bucket)
+ [Delete the S3 buckets](#batchops-s3-step9-delete-bucket)

### Delete the S3 Inventory configuration for your S3 source bucket<a name="batchops-s3-step9-delete-inventory"></a>

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Buckets**\.

1. In the **Buckets** list, choose the name of your source bucket \(for example, **tutorial\-bucket\-1**\)\.

1. Choose the **Management** tab\.

1. In the **Inventory configurations** section, choose the option button next to the inventory configuration that you created in [Step 5](#batchops-s3-step5) \(for example, **tutorial\-inventory\-config**\)\.

1. Choose **Delete**, and then choose **Confirm**\.

### Delete the Lambda function<a name="batchops-s3-step9-delete-lambda"></a>

1. 

   Open the AWS Lambda console at [https://console\.aws\.amazon\.com/lambda/](https://console.aws.amazon.com/lambda/)\.

1. In the left navigation pane, choose **Functions**\.

1. Select the check box next to the function that you created in [Step 4](#batchops-s3-step4) \(for example, **tutorial\-lambda\-convert**\)\.

1. Choose **Actions**, and then choose **Delete**\.

1. In the **Delete function** dialog box, choose **Delete**\.

### Delete the CloudWatch log group<a name="batchops-s3-step9-delete-cw"></a>

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the left navigation pane, choose **Logs**, and then choose **Log groups**\.

1. Select the check box next to the log group that has a name ending with the Lambda function that you created in [Step 4](#batchops-s3-step4) \(for example, **tutorial\-lambda\-convert**\)\.

1. Choose **Actions**, and then choose **Delete log group\(s\)**\.

1. In the **Delete log group\(s\)** dialog box, choose **Delete**\.

### Delete the IAM roles together with the inline policies for the IAM roles<a name="batchops-s3-step9-delete-role"></a>

To delete the IAM roles that you created in [Step 2](#batchops-s3-step2), [Step 3](#batchops-s3-step3), and [Step 6](#batchops-s3-step6), do the following: 

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the left navigation pane, choose **Roles**, and then select the check boxes next to the role names that you want to delete\.

1. At the top of the page, choose **Delete**\.

1. In the confirmation dialog box, enter the required response in the text input field based on the prompt, and choose **Delete**\. 

### Delete the customer\-managed IAM policy<a name="batchops-s3-step9-delete-policy"></a>

To delete the customer\-managed IAM policy that you created in [Step 6](#batchops-s3-step6), do the following:

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the left navigation pane, choose **Policies**\.

1. Choose the option button next to the policy that you created in [Step 6](#batchops-s3-step6) \(for example, **tutorial\-s3batch\-policy**\)\. You can use the search box to filter the list of policies\.

1. Choose **Actions**, and then choose **Delete**\. 

1. Confirm that you want to delete this policy by entering its name in the text field, and then choose **Delete**\. 

### Empty the S3 buckets<a name="batchops-s3-step9-empty-bucket"></a>

To empty the S3 buckets that you created in [Prerequisites](#batchops-s3-prerequisites), [Step 1](#batchops-s3-step1), and [Step 5](#batchops-s3-step5), do the following: 

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Buckets**\.

1. In the **Buckets** list, choose the option button next to the name of the bucket that you want to empty, and then choose **Empty**\.

1. On the **Empty bucket** page, confirm that you want to empty the bucket by entering **permanently delete** in the text field, and then choose **Empty**\.

### Delete the S3 buckets<a name="batchops-s3-step9-delete-bucket"></a>

To delete the S3 buckets that you created in [Prerequisites](#batchops-s3-prerequisites), [Step 1](#batchops-s3-step1), and [Step 5](#batchops-s3-step5), do the following: 

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Buckets**\.

1. In the **Buckets** list, choose the option button next to the name of the bucket that you want to delete\.

1. Choose **Delete**\.

1. On the **Delete bucket** page, confirm that you want to delete the bucket by entering the bucket name in the text field, and then choose **Delete bucket**\.

## Next steps<a name="batchops-s3-next-steps"></a>

After completing this tutorial, you can further explore other relevant use cases:
+ You can use Amazon CloudFront to stream the transcoded media files to viewers across the globe\. For more information, see [Tutorial: Hosting on\-demand streaming video with Amazon S3, Amazon CloudFront, and Amazon Route 53](tutorial-s3-cloudfront-route53-video-streaming.md)\.
+ You can transcode videos at the moment when you upload them to the S3 source bucket\. To do so, you can configure an Amazon S3 event trigger that automatically invokes the Lambda function to transcode new objects in S3 with MediaConvert\. For more information, see [Tutorial: Using an Amazon S3 trigger to invoke a Lambda function](https://docs.aws.amazon.com/lambda/latest/dg/with-s3-example.html) in the *AWS Lambda Developer Guide*\. 