# AWS usage report for Amazon S3<a name="aws-usage-report"></a>

When you download a usage report, you can choose to aggregate usage data by hour, day, or month\. The Amazon S3 usage report lists operations by usage type and AWS Region\. For more detailed reports about your Amazon S3 storage usage, download dynamically generated AWS usage reports\. You can choose which usage type, operation, and time period to include\. You can also choose how the data is aggregated\. 

The Amazon S3 usage report includes the following information:
+ **Service** – Amazon S3
+ **Operation** – The operation performed on your bucket or object\. For a detailed explanation of Amazon S3 operations, see [Tracking Operations in Your Usage Reports](aws-usage-report-understand.md#aws-usage-report-understand-operations)\.
+ **UsageType** – One of the following values:
  + A code that identifies the type of storage
  + A code that identifies the type of request
  + A code that identifies the type of retrieval
  + A code that identifies the type of data transfer
  + A code that identifies early deletions from S3 Intelligent\-Tiering, S3 Standard\-IA, S3 One Zone\-Infrequent Access \(S3 One Zone\-IA\), S3 Glacier Flexible Retrieval, or S3 Glacier Deep Archive storage
  + `StorageObjectCount` – The count of objects stored within a given bucket

  For a detailed explanation of Amazon S3 usage types, see [Understanding your AWS billing and usage reports for Amazon S3](aws-usage-report-understand.md)\.
+ **Resource** – The name of the bucket associated with the listed usage\.
+ **StartTime** – Start time of the day that the usage applies to, in Coordinated Universal Time \(UTC\)\.
+ **EndTime** – End time of the day that the usage applies to, in Coordinated Universal Time \(UTC\)\. 
+ **UsageValue** – One of the following volume values\. The typical unit of measurement for data is gigabytes \(GB\)\. However, depending on the service and the report, terabytes \(TB\) might appear instead\.
  + The number of requests during the specified time period
  + The amount of data transferred
  + The amount of data stored in a given hour
  + The amount of data associated with restorations from S3 Standard\-IA, S3 One Zone\-IA, S3 Glacier Flexible Retrieval, or S3 Glacier Deep Archive storage

**Tip**  
For detailed information about every request that Amazon S3 receives for your objects, turn on server access logging for your buckets\. For more information, see [Logging requests using server access logging](ServerLogs.md)\. 

You can download a usage report as an XML or a comma\-separated values \(CSV\) file\. The following is an example CSV usage report opened in a spreadsheet application\.

![\[Screenshot of a CSV usage report in a spreadsheet application.\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/s3-usage-report.png)

For more information, see [Understanding your AWS billing and usage reports for Amazon S3](aws-usage-report-understand.md)\.

## Downloading the AWS Usage Report<a name="aws-usage-report-download"></a>

You can download a usage report as an \.xml or a \.csv file\.

**To download the usage report**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the title bar, choose your AWS Identity and Access Management \(IAM\) user name, and then choose **My Billing Dashboard**\. 

1. In the navigation pane, choose **AWS Cost & Usage Reports**\.

1. In the **Other Reports** section, choose **AWS Usage Report**\.

1. For **Services**, choose **Amazon Simple Storage Service**\.

1. For **Download Usage Report**, choose the following settings:
   + ****Usage Types**** – For a detailed explanation of Amazon S3 usage types, see [Understanding your AWS billing and usage reports for Amazon S3](aws-usage-report-understand.md)\.
   + ****Operation**** – For a detailed explanation of Amazon S3 operations, see [Tracking Operations in Your Usage Reports](aws-usage-report-understand.md#aws-usage-report-understand-operations)\.
   + ****Time Period**** – The time period that you want the report to cover\. 
   + ****Report Granularity**** – Whether you want the report to include subtotals by the hour, by the day, or by the month\.

1. Choose the **Download** format and follow the prompts to open or save the report\.

## More Info<a name="aws-usage-report-more-info"></a>
+ [Understanding your AWS billing and usage reports for Amazon S3](aws-usage-report-understand.md)
+ [AWS Billing reports for Amazon S3](aws-billing-reports.md)