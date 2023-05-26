# Aggregate functions<a name="s3-select-sql-reference-aggregate"></a>

Amazon S3 Select supports the following aggregate functions\.


| Function | Argument type | Return type | 
| --- | --- | --- | 
| `AVG(expression)` | `INT`, `FLOAT`, `DECIMAL` | `DECIMAL` for an `INT` argument, `FLOAT` for a floating\-point argument; otherwise the same as the argument data type\. | 
| `COUNT` |  `-`  | `INT` | 
| `MAX(expression)` | `INT`, `DECIMAL` | Same as the argument type\. | 
| `MIN(expression)` | `INT`, `DECIMAL` | Same as the argument type\. | 
| `SUM(expression)` | `INT`, `FLOAT`, `DOUBLE`, `DECIMAL` | `INT` for an `INT` argument, `FLOAT` for a floating\-point argument; otherwise, the same as the argument data type\. | 

## SUM example<a name="s3-select-sql-reference-aggregate-case-examples"></a>

To aggregate the total object sizes of a folder in an [S3 Inventory report](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-inventory.html), use a `SUM` expression\.

The following S3 Inventory report is a CSV file that's compressed with GZIP\. There are three columns\.
+ The first column is the name of the S3 bucket \(*`DOC-EXAMPLE-BUCKET`*\) that the S3 Inventory report is for\.
+ The second column is the object key name that uniquely identifies the object in the bucket\.

  The `example-folder/` value in the first row is for the folder `example-folder`\. In Amazon S3, when you create a folder in your bucket, S3 creates a 0\-byte object with a key that's set to the folder name that you provided\.

  The `example-folder/object1` value in the second row is for the object `object1` in the folder `example-folder`\.

  The `example-folder/object2` value in the third row is for the object `object2` in the folder `example-folder`\.

  For more information about S3 folders, see [Organizing objects in the Amazon S3 console by using folders](using-folders.md)\.
+ The third column is the object size in bytes\.

```
"DOC-EXAMPLE-BUCKET","example-folder/","0"
"DOC-EXAMPLE-BUCKET","example-folder/object1","2011267"
"DOC-EXAMPLE-BUCKET","example-folder/object2","1570024"
```

To use a `SUM` expression to calculate the total size of the folder `example-folder`, run the SQL query with Amazon S3 Select\.

```
SELECT SUM(CAST(_3 as INT)) FROM s3object s WHERE _2 LIKE 'example-folder/%' AND _2 != 'example-folder/';
```

Query Result: 

```
3581291
```