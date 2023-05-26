# Examples of using Amazon S3 Select on objects<a name="using-select"></a>

You can use S3 Select with the Amazon S3 console, REST API, and the AWS SDK to select content from objects\. 

## Using the S3 console<a name="s3-select-objects-console"></a>

**To select content from an object in the Amazon S3 console**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Buckets**\.

1. Choose the bucket that contains the object that you want to select content from, and then choose the name of the object\.

1. Choose **Object actions**, and choose **Query with S3 Select**\.

1. Configure **Input settings**, based on the format of your input data\.

1. Configure **Output settings**, based on the format of the output that you want to receive\.

1. To extract records from the chosen object, under **SQL query**, enter the SELECT SQL commands\. For more information on how to write SQL commands, see [SQL reference for Amazon S3 Select](s3-select-sql-reference.md)\.

1. After you enter SQL queries, choose **Run SQL query**\. Then, under **Query results**, you can see the results of your SQL queries\.

## Using the REST API<a name="SelectObjectContentUsingRestApi"></a>

You can use the AWS SDK to select content from objects\. However, if your application requires it, you can send REST requests directly\. For more information about the request and response format, see [SELECT Object Content](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectSELECTContent.html)\.

## Using the AWS SDKs<a name="SelectObjectContentUsingSDK"></a>

You can use Amazon S3 Select to select contents of an object using the `selectObjectContent` method, which on success returns the results of the SQL expression\.

------
#### [ Java ]

The following Java code returns the value of the first column for each record that is stored in an object that contains data stored in CSV format\. It also requests `Progress` and `Stats` messages to be returned\. You must provide a valid bucket name and an object that contains data in CSV format\.

For instructions on creating and testing a working sample, see [Testing the Amazon S3 Java Code Examples](UsingTheMPJavaAPI.md#TestingJavaSamples)\.

```
package com.amazonaws;

import com.amazonaws.services.s3.AmazonS3;
import com.amazonaws.services.s3.AmazonS3ClientBuilder;
import com.amazonaws.services.s3.model.CSVInput;
import com.amazonaws.services.s3.model.CSVOutput;
import com.amazonaws.services.s3.model.CompressionType;
import com.amazonaws.services.s3.model.ExpressionType;
import com.amazonaws.services.s3.model.InputSerialization;
import com.amazonaws.services.s3.model.OutputSerialization;
import com.amazonaws.services.s3.model.SelectObjectContentEvent;
import com.amazonaws.services.s3.model.SelectObjectContentEventVisitor;
import com.amazonaws.services.s3.model.SelectObjectContentRequest;
import com.amazonaws.services.s3.model.SelectObjectContentResult;

import java.io.File;
import java.io.FileOutputStream;
import java.io.InputStream;
import java.io.OutputStream;
import java.util.concurrent.atomic.AtomicBoolean;

import static com.amazonaws.util.IOUtils.copy;

/**
 * This example shows how to query data from S3Select and consume the response in the form of an
 * InputStream of records and write it to a file.
 */

public class RecordInputStreamExample {

    private static final String BUCKET_NAME = "${my-s3-bucket}";
    private static final String CSV_OBJECT_KEY = "${my-csv-object-key}";
    private static final String S3_SELECT_RESULTS_PATH = "${my-s3-select-results-path}";
    private static final String QUERY = "select s._1 from S3Object s";

    public static void main(String[] args) throws Exception {
        final AmazonS3 s3Client = AmazonS3ClientBuilder.defaultClient();

        SelectObjectContentRequest request = generateBaseCSVRequest(BUCKET_NAME, CSV_OBJECT_KEY, QUERY);
        final AtomicBoolean isResultComplete = new AtomicBoolean(false);

        try (OutputStream fileOutputStream = new FileOutputStream(new File (S3_SELECT_RESULTS_PATH));
             SelectObjectContentResult result = s3Client.selectObjectContent(request)) {
            InputStream resultInputStream = result.getPayload().getRecordsInputStream(
                    new SelectObjectContentEventVisitor() {
                        @Override
                        public void visit(SelectObjectContentEvent.StatsEvent event)
                        {
                            System.out.println(
                                    "Received Stats, Bytes Scanned: " + event.getDetails().getBytesScanned()
                                            +  " Bytes Processed: " + event.getDetails().getBytesProcessed());
                        }

                        /*
                         * An End Event informs that the request has finished successfully.
                         */
                        @Override
                        public void visit(SelectObjectContentEvent.EndEvent event)
                        {
                            isResultComplete.set(true);
                            System.out.println("Received End Event. Result is complete.");
                        }
                    }
            );

            copy(resultInputStream, fileOutputStream);
        }

        /*
         * The End Event indicates all matching records have been transmitted.
         * If the End Event is not received, the results may be incomplete.
         */
        if (!isResultComplete.get()) {
            throw new Exception("S3 Select request was incomplete as End Event was not received.");
        }
    }

    private static SelectObjectContentRequest generateBaseCSVRequest(String bucket, String key, String query) {
        SelectObjectContentRequest request = new SelectObjectContentRequest();
        request.setBucketName(bucket);
        request.setKey(key);
        request.setExpression(query);
        request.setExpressionType(ExpressionType.SQL);

        InputSerialization inputSerialization = new InputSerialization();
        inputSerialization.setCsv(new CSVInput());
        inputSerialization.setCompressionType(CompressionType.NONE);
        request.setInputSerialization(inputSerialization);

        OutputSerialization outputSerialization = new OutputSerialization();
        outputSerialization.setCsv(new CSVOutput());
        request.setOutputSerialization(outputSerialization);

        return request;
    }
}
```

------
#### [ JavaScript ]

For a JavaScript example using the AWS SDK for JavaScript with the S3 SelectObjectContent API to select records from JSON and CSV files stored in Amazon S3, see the blog post [ Introducing support for Amazon S3 Select in the AWS SDK for JavaScript](http://aws.amazon.com/blogs/developer/introducing-support-for-amazon-s3-select-in-the-aws-sdk-for-javascript/)\. 

------
#### [ Python ]

For a Python example on using structured query language \(SQL\) queries to search through data loaded to Amazon S3 as a comma\-separated value \(CSV\) file using S3 Select, see the blog post [ Querying data without servers or databases using Amazon S3 Select](http://aws.amazon.com/blogs/storage/querying-data-without-servers-or-databases-using-amazon-s3-select/)\. 

------