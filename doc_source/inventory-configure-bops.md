# Converting empty version ID strings in Amazon S3 Inventory reports to null strings<a name="inventory-configure-bops"></a>

**Note**  
**The following procedure applies only to Amazon S3 Inventory reports that include all versions, and only if the "all versions" reports are used as manifests for S3 Batch Operations on buckets that have S3 Versioning enabled\.** You are not required to convert strings for S3 Inventory reports that specify the current version only\.

You can use S3 Inventory reports as manifests for S3 Batch Operations\. However, when S3 Versioning is enabled on a bucket, S3 Inventory reports that include all versions mark any null\-versioned objects with empty strings in the version ID field\. When an Inventory Report includes all object version IDs, Batch Operations recognizes `null` strings as version IDs, but not empty strings\. 

When an S3 Batch Operations job uses an "all versions" S3 Inventory report as a manifest, it fails all tasks on objects that have an empty string in the version ID field\. To convert empty strings in the version ID field of the S3 Inventory report to `null` strings for Batch Operations, use the following procedure\.

**Update an Amazon S3 Inventory report for use with Batch Operations**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. Navigate to your S3 Inventory report\. The inventory report is located in the destination bucket that you specified while configuring your inventory report\. For more information about locating inventory reports, see [Locating your inventory list](storage-inventory-location.md)\.

   1. Choose the destination bucket\.

   1. Choose the folder\. The folder is named after the original source bucket\.

   1. Choose the folder named after the inventory configuration\.

   1. Select the check box next to the folder named **hive**\. At the top of the page, choose **Copy S3 URI** to copy the S3 URI for the folder\.

1. Open the Amazon Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\. 

1. In the query editor, choose **Settings**, then choose **Manage**\. On the **Manage settings** page, for **Location of query result**, choose an S3 bucket to store your query results in\.

1. In the query editor, create an Athena table to hold the data in the inventory report using the following command\. Replace `table_name` with a name of your choosing, and in the `LOCATION` clause, insert the S3 URI that you copied earlier\. Then choose **Run** to run the query\.

   ```
   CREATE EXTERNAL TABLE table_name(bucket string, key string, version_id string) PARTITIONED BY (dt string)ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.OpenCSVSerde'STORED AS INPUTFORMAT 'org.apache.hadoop.hive.ql.io.SymlinkTextInputFormat' OUTPUTFORMAT 'org.apache.hadoop.hive.ql.io.IgnoreKeyTextOutputFormat' LOCATION 'Copied S3 URI';
   ```

1. To clear the query editor, choose **Clear**\. Then load the inventory report into the table using the following command\. Replace `table_name` with the one that you chose in the prior step\. Then choose **Run** to run the query\.

   ```
   MSCK REPAIR TABLE table_name;
   ```

1. To clear the query editor, choose **Clear**\. Run the following `SELECT` query to retrieve all entries in the original inventory report and replace any empty version IDs with `null` strings\. Replace `table_name` with the one that you chose earlier, and replace `YYYY-MM-DD-HH-MM` in the `WHERE` clause with the date of the inventory report that you want this tool to run on\. Then choose **Run** to run the query\.

   ```
   SELECT bucket as Bucket, key as Key, CASE WHEN version_id = '' THEN 'null' ELSE version_id END as VersionId FROM table_name WHERE dt = 'YYYY-MM-DD-HH-MM';
   ```

1. Return to the Amazon S3 console \([https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\), and navigate to the S3 bucket that you chose for **Location of query result** earlier\. Inside, there should be a series of folders ending with the date\.

   For example, you should see something like **s3://*DOC\-EXAMPLE\-BUCKET*/*query\-result\-location*/Unsaved/2021/10/07/**\. You should see `.csv` files containing the results of the `SELECT` query that you ran\. 

   Choose the CSV file with the latest modified date\. Download this file to your local machine for the next step\.

1. The generated CSV file contains a header row\. To use this CSV file as input for an S3 Batch Operations job, you must remove the header row, because Batch Operations doesn't support header rows on CSV manifests\. 

   To remove the header row, you can run one of the following commands on the file\. Replace *`file.csv`* with the name of your CSV file\. 

   **For macOS and Linux machines**, run the `tail` command in a Terminal window\. 

   ```
   tail -n +2 file.csv > tmp.csv && mv tmp.csv file.csv 
   ```

   **For Windows machines**, run the following script in a Windows PowerShell window\. Replace *File\-location* with the path to your file, and *file\.csv* with the file name\.

   ```
   $ins = New-Object System.IO.StreamReader File-location\file.csv
   $outs = New-Object System.IO.StreamWriter File-location\temp.csv
   try {
       $skip = 0
       while ( !$ins.EndOfStream ) {
           $line = $ins.ReadLine();
           if ( $skip -ne 0 ) {
               $outs.WriteLine($line);
           } else {
               $skip = 1
           }
       }
   } finally {
       $outs.Close();
       $ins.Close();
   }
   Move-Item File-location\temp.csv File-location\file.csv -Force
   ```

1. After removing the header row from the CSV file, you are ready to use it as a manifest in an S3 Batch Operations job\. Upload the CSV file to an S3 bucket or location of your choosing, and then create a Batch Operations job using the CSV file as the manifest\.

   For more information about creating a Batch Operations job, see [Creating an S3 Batch Operations job](batch-ops-create-job.md)\.