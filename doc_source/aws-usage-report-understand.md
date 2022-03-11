# Understanding your AWS billing and usage reports for Amazon S3<a name="aws-usage-report-understand"></a>

Amazon S3 billing and usage reports use codes and abbreviations\. For usage types in the table that follows, replace *region*, *region1*, and *region2* with abbreviations from this list:
+ **APE1:** Asia Pacific \(Hong Kong\)
+ **APN1:** Asia Pacific \(Tokyo\)
+ **APN2:** Asia Pacific \(Seoul\)
+ **APS1:** Asia Pacific \(Singapore\)
+ **APS2:** Asia Pacific \(Sydney\)
+ **APS3:** Asia Pacific \(Mumbai\)
+ **CAN1:** Canada \(Central\)
+ **CPT:** Africa \(Cape Town\)
+ **EUN1:** Europe \(Stockholm\)
+ **EUC1:** Europe \(Frankfurt\)
+ **EU:** Europe \(Ireland\)
+ **EUW2:** Europe \(London\)
+ **EUW3:** Europe \(Paris\)
+ **MES1:** Middle East \(Bahrain\)
+ **SAE1:** South America \(São Paulo\)
+ **UGW1:** AWS GovCloud \(US\-West\)
+ **UGE1:** AWS GovCloud \(US\-East\)
+ **USE1 \(or no prefix\):** US East \(N\. Virginia\)
+ **USE2:** US East \(Ohio\)
+ **USW1:** US West \(N\. California\)
+ **USW2:** US West \(Oregon\)

For information about pricing by AWS Region, see [Amazon S3 Pricing](https://aws.amazon.com/s3/pricing/)\.

The first column in the following table lists usage types that appear in your billing and usage reports\. The typical unit of measurement for data is gigabytes \(GB\)\. However, depending on the service and the report, terabytes \(TB\) might appear instead\.


**Usage Types**  

| Usage Type | Units | Granularity | Description | 
| --- | --- | --- | --- | 
|  `region1-region2-AWS-In-ABytes`  |  GB  |  Hourly  | The amount of accelerated data transferred to AWS Region1 from AWS Region2 | 
|  `region1-region2-AWS-In-ABytes-T1`  |  GB  |  Hourly  | The amount of T1 accelerated data transferred to AWS Region1 from AWS Region2, where T1 refers to CloudFront requests to POPs in the United States, Europe, and Japan | 
|  `region1-region2-AWS-In-ABytes-T2`  |  GB  |  Hourly  | The amount of T2 accelerated data transferred to AWS Region1 from AWS Region2, where T2 refers to CloudFront requests to POPs in all other AWS edge locations | 
|  `region1-region2-AWS-In-Bytes`  |  GB  |  Hourly  |  The amount of data transferred to AWS Region1 from AWS Region2  | 
|  `region1-region2-AWS-Out-ABytes`  |  GB  |  Hourly  | The amount of accelerated data transferred from AWS Region1 to AWS Region2 | 
|  `region1-region2-AWS-Out-ABytes-T1`  |  GB  |  Hourly  | The amount of T1 accelerated data transferred from AWS Region1 from AWS Region2, where T1 refers to CloudFront requests to POPs in the United States, Europe, and Japan | 
|  `region1-region2-AWS-Out-ABytes-T2`  |  GB  |  Hourly  | The amount of T2 accelerated data transferred from AWS Region1 to AWS Region2, where T2 refers to CloudFront requests to POPs in all other AWS edge locations | 
|  `region1-region2-AWS-Out-Bytes`  |  GB  |  Hourly  |  The amount of data transferred from AWS Region1 to AWS Region2  | 
|  `region-BatchOperations-Jobs `  |  Count   |  Hourly  |  The number of S3 Batch Operations jobs performed  | 
|  `region-BatchOperations-Objects `  |  Count   |  Hourly  |  The number of object operations performed by S3 Batch Operations  | 
|  `region-Bulk-Retrieval-Bytes`  |  GB  |  Hourly  |  The amount of data retrieved with Bulk S3 Glacier Flexible Retrieval or S3 Glacier Deep Archive requests  | 
|  `region-BytesDeleted-GDA`  |  GB  |  Monthly  |  The amount of data deleted by a DeleteObject operation from S3 Glacier Deep Archive storage  | 
|  `region-BytesDeleted-GIR`  |  GB  |  Monthly  |  The amount of data deleted by a DeleteObject operation from S3 Glacier Instant Retrieval storage\.  | 
|  `region-BytesDeleted-GLACIER`  |  GB  |  Monthly  |  The amount of data deleted by a DeleteObject operation from S3 Glacier Flexible Retrieval storage  | 
|  `region-BytesDeleted-INT`  |  GB  |  Monthly  |  The amount of data deleted by a DeleteObject operation from S3 Intelligent\-Tiering storage  | 
|  `region-BytesDeleted-RRS`  |  GB  |  Monthly  |  The amount of data deleted by a DeleteObject operation from Reduced Redundancy Storage \(RRS\) storage  | 
|  `region-BytesDeleted-SIA`  |  GB  |  Monthly  |  The amount of data deleted by a DeleteObject operation from S3 Standard\-IA storage  | 
|  `region-BytesDeleted-STANDARD`  |  GB  |  Monthly  |  The amount of data deleted by a DeleteObject operation from S3 Standard storage  | 
|  `region-BytesDeleted-ZIA`  |  GB  |  Monthly  |  The amount of data deleted by a DeleteObject operation from S3 One Zone\-IA storage  | 
|  `region-C3DataTransfer-In-Bytes`  |  GB  |  Hourly  |  The amount of data transferred into Amazon S3 from Amazon EC2 within the same AWS Region  | 
|  `region-C3DataTransfer-Out-Bytes`  |  GB  |  Hourly  |  The amount of data transferred from Amazon S3 to Amazon EC2 within the same AWS Region  | 
|  `region-CloudFront-In-Bytes`  |  GB  |  Hourly  |  The amount of data transferred into an AWS Region from a CloudFront distribution  | 
|  `region-CloudFront-Out-Bytes`  |  GB  |  Hourly  |  The amount of data transferred from an AWS Region to a CloudFront distribution  | 
|  `region-DataTransfer-In-Bytes`  |  GB  |  Hourly  |  The amount of data transferred into Amazon S3 from the internet  | 
|  `region-DataTransfer-Out-Bytes`  |  GB  |  Hourly  |  The amount of data transferred from Amazon S3 to the internet1  | 
|  `region-DataTransfer-Regional-Bytes`  |  GB  |  Hourly  |  The amount of data transferred from Amazon S3 to AWS resources within the same AWS Region  | 
|  `region-EarlyDelete-ByteHrs`  |  GB\-Hours  |  Hourly  |  Prorated storage usage for objects deleted from, S3 Glacier Flexible Retrieval storage before the 90\-day minimum commitment ended2  | 
|  `region-EarlyDelete-GDA`  |  GB\-Hours  |  Hourly  |  Prorated storage usage for objects deleted from S3 Glacier Deep Archive storage before the 180\-day minimum commitment ended 2  | 
|  `region-EarlyDelete-GIR`  |  GB\-Hours  |  Hourly  |  Prorated storage usage for objects deleted from S3 Glacier Instant Retrieval before the 90\-day minimum commitment ended\.  | 
|  `region-EarlyDelete-GIR-SmObjects`  |  GB\-Hours  |  Hourly  |  Prorated storage usage for small objects \(smaller than 128 KB\) that were deleted from S3 Glacier Instant Retrieval before the 90\-day minimum commitment ended\.  | 
|  `region-EarlyDelete-SIA`  |  GB\-Hours  |  Hourly  |  Prorated storage usage for objects deleted from S3 Standard\-IA before the 30\-day minimum commitment ended3  | 
|  `region-EarlyDelete-SIA-SmObjects`  |  GB\-Hours  |  Hourly  |  Prorated storage usage for small objects \(smaller than 128 KB\) that were deleted from S3 Standard\-IA before the 30\-day minimum commitment ended3  | 
|  `region-EarlyDelete-ZIA`  |  GB\-Hours  |  Hourly  |  Prorated storage usage for objects deleted from S3 One Zone\-IA before the 30\-day minimum commitment ended3  | 
|  `region-EarlyDelete-ZIA-SmObjects`  |  GB\-Hours  |  Hourly  |  Prorated storage usage for small objects \(smaller than 128 KB\) that were deleted from S3 One Zone\-IA before the 30\-day minimum commitment ended3  | 
|  `region-Expedited-Retrieval-Bytes`  |  GB  |  Hourly  |  The amount of data retrieved with Expedited S3 Glacier Flexible Retrieval requests  | 
|  `region-Inventory-ObjectsListed`  |  Objects  |  Hourly  |  The number of objects listed for an object group \(objects are grouped by bucket or prefix\) with an inventory list  | 
|  `region-Monitoring-Automation-INT`  |  Objects  |  Hourly  |  The number of unique objects monitored and auto\-tiered in the S3 Intelligent\-Tiering storage class  | 
|  `region-OverwriteBytes-Copy-GDA`  |  GB  |  Monthly  |  The amount of data overwritten by a CopyObject operation from S3 Glacier Deep Archive storage  | 
|  `region-OverwriteBytes-Copy-GIR`  |  GB  |  Monthly  |  The amount of data overwritten by a CopyObject operation from S3 Glacier Instant Retrieval storage\.  | 
|  `region-OverwriteBytes-Copy-GLACIER`  |  GB  |  Monthly  |  The amount of data overwritten by a CopyObject operation from S3 Glacier Flexible Retrieval storage  | 
|  `region-OverwriteBytes-Copy-INT`  |  GB  |  Monthly  |  The amount of data overwritten by a CopyObject operation from S3 Intelligent\-Tiering storage  | 
|  `region-OverwriteBytes-Copy-RRS`  |  GB  |  Monthly  |  The amount of data overwritten by a CopyObject operation from Reduced Redundancy Storage \(RRS\) storage  | 
|  `region-OverwriteBytes-Copy-SIA`  |  GB  |  Monthly  |  The amount of data overwritten by a CopyObject operation from S3 Standard\-IA storage  | 
|  `region-OverwriteBytes-Copy-STANDARD`  |  GB  |  Monthly  |  The amount of data overwritten by a CopyObject operation from S3 Standard storage  | 
|  `region-OverwriteBytes-Copy-ZIA`  |  GB  |  Monthly  |  The amount of data overwritten by a CopyObject operation from S3 One Zone\-IA storage  | 
|  `region-OverwriteBytes-Put-GDA`  |  GB  |  Monthly  |  The amount of data overwritten by a PutObject operation from S3 Glacier Deep Archive storage  | 
|  `region-OverwriteBytes-Put-GIR`  |  GB  |  Monthly  |  The amount of data overwritten by a PutObject operation from S3 Glacier Instant Retrieval storage\.  | 
|  `region-OverwriteBytes-Put-GLACIER`  |  GB  |  Monthly  |  The amount of data overwritten by a PutObject operation from S3 Glacier Flexible Retrieval storage  | 
|  `region-OverwriteBytes-Put-INT`  |  GB  |  Monthly  |  The amount of data overwritten by a PutObject operation from S3 Intelligent\-Tiering storage  | 
|  `region-OverwriteBytes-Put-RRS`  |  GB  |  Monthly  |  The amount of data overwritten by a PutObject operation from Reduced Redundancy Storage \(RRS\) storage  | 
|  `region-OverwriteBytes-Put-SIA`  |  GB  |  Monthly  |  The amount of data overwritten by a PutObject operation from S3 Standard\-IA storage  | 
|  `region-OverwriteBytes-Put-STANDARD`  |  GB  |  Monthly  |  The amount of data overwritten by a PutObject operation from S3 Standard storage  | 
|  `region-OverwriteBytes-Put-ZIA`  |  GB  |  Monthly  |  The amount of data overwritten by a PutObject operation from S3 One Zone\-IA storage  | 
|  `region-Requests-GDA-Tier1`  |  Count  |  Hourly  |  The number of PUT, COPY, POST, InitiateMultipartUpload, UploadPart, or CompleteMultipartUpload requests on DEEP Archive objects  | 
|  `region-Requests-GDA-Tier2`  |  Count  |  Hourly  |  The number of GET, HEAD, and LIST requests  | 
|  `region-Requests-GDA-Tier3`  |  Count  |  Hourly  |  The number of S3 Glacier Deep Archive standard restore requests  | 
|  `region-Requests-GDA-Tier5`  |  Count  |  Hourly  |  The number of Bulk S3 Glacier Deep Archive restore requests  | 
|  `region-Requests-GIR-Tier1`  |  Count  |  Hourly  |  The number of PUT, COPY, POST, or LIST requests on S3 Glacier Instant Retrieval objects\.  | 
|  `region-Requests-GIR-Tier2`  |  Count  |  Hourly  |  The number of GET and all other non\-GIR\-Tier1 requests on S3 Glacier Instant Retrieval objects\.  | 
|  `region-Requests-GLACIER-Tier1`  |  Count  |  Hourly  |  The number of PUT, COPY, POST, InitiateMultipartUpload, UploadPart, or CompleteMultipartUpload requests on S3 Glacier Flexible Retrieval objects  | 
|  `region-Requests-GLACIER-Tier2`  |  Count  |  Hourly  | The number of GET and all other requests not listed on S3 Glacier Flexible Retrieval objects | 
|  `region-Requests-INT-Tier1`  |  Count  |  Hourly  |  The number of PUT, COPY, POST, or LIST requests on S3 Intelligent\-Tiering objects  | 
|  `region-Requests-INT-Tier2`  |  Count  |  Hourly  |  The number of GET and all other non\-Tier1 requests for S3 Intelligent\-Tiering objects  | 
|  `region-Requests-SIA-Tier1`  |  Count  |  Hourly  |  The number of PUT, COPY, POST, or LIST requests on S3 Standard\-IA objects  | 
|  `region-Requests-SIA-Tier2`  |  Count  |  Hourly  |  The number of GET and all other non\-SIA\-Tier1 requests on S3 Standard\-IA objects  | 
|  `region-Requests-Tier1`  |  Count  |  Hourly  |  The number of PUT, COPY, POST, or LIST requests for STANDARD, RRS, and tags  | 
|  `region-Requests-Tier2`  |  Count  |  Hourly  |  The number of GET and all other non\-Tier1 requests  | 
|  `region-Requests-Tier3`  |  Count  |  Hourly  |  The number of lifecycle requests to S3 Glacier Flexible Retrieval or S3 Glacier Deep Archive and standard S3 Glacier Flexible Retrieval restore requests  | 
|  `region-Requests-Tier4`  |  Count  |  Hourly  |  The number of lifecycle transitions to S3 Glacier Instant Retrieval, S3 Intelligent\-Tiering, S3 Standard\-IA, or S3 One Zone\-IA storage  | 
|  `region-Requests-Tier5`  |  Count  |  Hourly  |  The number of Bulk S3 Glacier Flexible Retrieval restore requests  | 
|  `region-Requests-Tier6`  |  Count  |  Hourly  |  The number of Expedited S3 Glacier Flexible Retrieval restore requests  | 
|  `region-Requests-ZIA-Tier1`  |  Count  |  Hourly  |  The number of PUT, COPY, POST, or LIST requests on S3 One Zone\-IA objects  | 
|  `region-Requests-ZIA-Tier2`  |  Count  |  Hourly  |  The number of GET and all other non\-ZIA\-Tier1 requests on S3 One Zone\-IA objects  | 
|  `region-Retrieval-GIR`  |  GB  |  Hourly  |  The amount of data retrieved from S3 Glacier Instant Retrieval storage\.  | 
|  `region-Retrieval-SIA`  |  GB  |  Hourly  |  The amount of data retrieved from S3 Standard\-IA storage  | 
|  `region-Retrieval-ZIA`  |  GB  |  Hourly  |  The amount of data retrieved from S3 One Zone\-IA storage  | 
|  `region-S3G-DataTransfer-In-Bytes`  |  GB  |  Hourly  |  The amount of data transferred into Amazon S3 to restore objects from S3 Glacier Flexible Retrieval or S3 Glacier Deep Archive storage  | 
|  `region-S3G-DataTransfer-Out-Bytes`  |  GB  |  Hourly  |  The amount of data transferred from Amazon S3 to transition objects to S3 Glacier Flexible Retrieval or S3 Glacier Deep Archive storage  | 
|  `region-Select-Returned-Bytes`  |  GB  |  Hourly  |  The amount of data returned with Select requests from S3 Standard storage  | 
|  `region-Select-Returned-GIR-Bytes`  |  GB  |  Hourly  |  The amount of data returned with Select requests from S3 Glacier Instant Retrieval storage\.  | 
|  `region-Select-Returned-INT-Bytes`  |  GB  |  Hourly  |  The amount of data returned with Select requests from S3 Intelligent\-Tiering storage  | 
|  `region-Select-Returned-SIA-Bytes`  |  GB  |  Hourly  |  The amount of data returned with Select requests from S3 Standard\-IA storage  | 
|  `region-Select-Returned-ZIA-Bytes`  |  GB  |  Hourly  |  The amount of data returned with Select requests from S3 One Zone\-IA storage  | 
|  `region-Select-Scanned-Bytes`  |  GB  |  Hourly  |  The amount of data scanned with Select requests from S3 Standard storage  | 
|  `region-Select-Scanned-GIR-Bytes`  |  GB  |  Hourly  |  The amount of data scanned with Select requests from S3 Glacier Instant Retrieval storage\.  | 
|  `region-Select-Scanned-INT-Bytes`  |  GB  |  Hourly  |  The amount of data scanned with Select requests from S3 Intelligent\-Tiering storage  | 
|  `region-Select-Scanned-SIA-Bytes`  |  GB  |  Hourly  |  The amount of data scanned with Select requests from S3 Standard\-IA storage  | 
|  `region-Select-Scanned-ZIA-Bytes`  |  GB  |  Hourly  |  The amount of data scanned with Select requests from S3 One Zone\-IA storage  | 
|  `region-Standard-Retrieval-Bytes`  |  GB  |  Hourly  |  The amount of data retrieved with standard S3 Glacier Flexible Retrieval or S3 Glacier Deep Archive requests  | 
|  `region-StorageAnalytics-ObjCount`  |  Objects  |  Hourly  |  The number of unique objects in each object group \(where objects are grouped by bucket or prefix\) tracked by storage analytics  | 
|  `region-TagStorage-TagHrs`  |  Tag\-Hours  |  Daily  |  The total of tags on all objects in the bucket reported by hour  | 
|  `region-TimedStorage-ByteHrs`  |  GB\-Hours  |  Daily  |  The number of GB\-hours that data was stored in S3 Standard storage  | 
|  `region-TimedStorage-GDA-ByteHrs`  |  GB\-Hours  |  Daily  |  The number of GB\-hours that data was stored in S3 Glacier Deep Archive storage  | 
|  `region-TimedStorage-GDA-Staging`  |  GB\-Hours  |  Daily  |  The number of GB\-hours that data was stored in S3 Glacier Deep Archive staging storage  | 
|  `region-TimedStorage-GIR-ByteHrs`  |  GB\-Hours  |  Daily  |  The number of GB\-hours that data was stored in S3 Glacier Instant Retrieval storage\.  | 
|  `region-TimedStorage-GIR-SmObjects`  |  GB\-Hours  |  Daily  |  The number of GB\-hours that small objects \(smaller than 128 KB\) were stored in S3 Glacier Instant Retrieval storage\.  | 
|  `region-TimedStorage-GlacierByteHrs`  |  GB\-Hours  |  Daily  |  The number of GB\-hours that data was stored in S3 Glacier Flexible Retrieval storage  | 
|  `region-TimedStorage-GlacierStaging`  |  GB\-Hours  |  Daily  |  The number of GB\-hours that data was stored in S3 Glacier Flexible Retrieval staging storage  | 
|  `region-TimedStorage-INT-FA-ByteHrs`  |  GB\-Hours  |  Daily  |  The number of GB\-hours that data was stored in the frequent access tier of S3 Intelligent\-Tiering storage5  | 
|  `region-TimedStorage-INT-IA-ByteHrs`  |  GB\-Hours  |  Daily  |  The number of GB\-hours that data was stored in the Infrequent Access tier of S3 Intelligent\-Tiering storage  | 
|  `region-TimedStorage-INT-AA-ByteHrs`  |  GB\-Hours  |  Daily  | The number of GB\-hours that data was stored in the Archive Access tier of S3 Intelligent\-Tiering storage | 
|  `region-TimedStorage-INT-AIA-ByteHrs`  |  GB\-Hours  |  Daily  | The number of GB\-hours that data was stored in the Archive Instant Access tier of S3 Intelligent\-Tiering storage | 
|  `region-TimedStorage-INT-DAA-ByteHrs`  |  GB\-Hours  |  Daily  | The number of GB\-hours that data was stored in the Deep Archive Access tier of S3 Intelligent\-Tiering storage | 
|  `region-TimedStorage-RRS-ByteHrs`  |  GB\-Hours  |  Daily  |  The number of GB\-hours that data was stored in Reduced Redundancy Storage \(RRS\) storage  | 
|  `region-TimedStorage-SIA-ByteHrs`  |  GB\-Hours  |  Daily  |  The number of GB\-hours that data was stored in S3 Standard\-IA storage  | 
|  `region-TimedStorage-SIA-SmObjects`  |  GB\-Hours  |  Daily  |  The number of GB\-hours that small objects \(smaller than 128 KB\) were stored in S3 Standard\-IA storage4  | 
|  `region-TimedStorage-ZIA-ByteHrs`  |  GB\-Hours  |  Daily  |  The number of GB\-hours that data was stored in S3 One Zone\-IA storage  | 
|  `region-TimedStorage-ZIA-SmObjects`  |  GB\-Hours  |  Daily  |  The number of GB\-hours that small objects \(smaller than 128 KB\) were stored in One Zone\-IA storage  | 
|  `StorageObjectCount`  |  Count  |  Daily  |  The number of objects stored within a given bucket  | 

**Notes:**

1. If you terminate a transfer before completion, the amount of data that is transferred might exceed the amount of data that your application receives\. This discrepancy can occur because a transfer termination request cannot be executed instantaneously, and some amount of data might be in transit pending execution of the termination request\. This data in transit is billed as data transferred “out\.”

1. When objects that are archived to the S3 Glacier Instant Retrieval, S3 Glacier Flexible Retrieval, or S3 Glacier Deep Archive storage class are deleted, overwritten, or transitioned to a different storage class before the minimum storage commitment has passed, which is 90 days for S3 Glacier Instant Retrieval and S3 Glacier Flexible Retrieval, or 180\-days for S3 Glacier Deep Archive, there is a prorated charge per gigabyte for the remaining days\.

1. For objects that are in S3 Standard\-IA or S3 One Zone\-IA storage, when they are deleted, overwritten, or transitioned to a different storage class prior to 30 days, there is a prorated charge per gigabyte for the remaining days\.

1. For small objects \(smaller than 128 KB\) that are in S3 Standard\-IA or S3 One Zone\-IA storage, when they are deleted, overwritten, or transitioned to a different storage class prior to 30 days, there is a prorated charge per gigabyte for the remaining days\.

1. There is no minimum billable object size for objects in the S3 Intelligent\-Tiering storage class\. Objects that are smaller than 128 KB are not monitored or eligible for auto\-tiering\. Smaller objects are always stored in the S3 Intelligent\-Tiering Frequent Access tier\.

## Tracking Operations in Your Usage Reports<a name="aws-usage-report-understand-operations"></a>

Operations describe the action taken on your AWS object or bucket by the specified usage type\. Operations are indicated by self\-explanatory codes, such as `PutObject` or `ListBucket`\. To see which actions on your bucket generated a specific type of usage, use these codes\. When you create a usage report, you can choose to include **All Operations**, or a specific operation, for example, `GetObject`, to report on\.

## More Info<a name="aws-usage-report-understand-more-info"></a>
+ [AWS usage report for Amazon S3](aws-usage-report.md)
+ [AWS Billing reports for Amazon S3](aws-billing-reports.md)
+ [Amazon S3 Pricing](https://aws.amazon.com/s3/pricing/)
+ [Amazon S3 FAQ](https://aws.amazon.com/s3/faqs/#billing)
+ [S3 Glacier Pricing](https://aws.amazon.com/glacier/pricing/)
+ [S3 Glacier FAQs](https://aws.amazon.com/glacier/faqs/)