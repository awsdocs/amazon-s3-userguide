# Data Types<a name="s3-glacier-select-sql-reference-data-types"></a>

Amazon S3 Select and S3 Glacier Select support several primitive data types\.

## Data Type Conversions<a name="s3-glacier-select-sql-reference-data-conversion"></a>

The general rule is to follow the `CAST` function if defined\. If `CAST` is not defined, then all input data is treated as a string\. It must be cast into the relevant data types when necessary\.

For more information about the `CAST` function, see [CAST](s3-glacier-select-sql-reference-conversion.md#s3-glacier-select-sql-reference-cast)\.

## Supported Data Types<a name="s3-glacier-select-sql-reference-supported-data-types"></a>

Amazon S3 Select and S3 Glacier Select support the following set of primitive data types\.


|  Name  |  Description  |  Examples  | 
| --- | --- | --- | 
| bool | TRUE or FALSE | FALSE | 
| int, integer | 8\-byte signed integer in the range \-9,223,372,036,854,775,808 to 9,223,372,036,854,775,807\.  | 100000 | 
| string | UTF8\-encoded variable\-length string\. The default limit is one character\. The maximum character limit is 2,147,483,647\.  | 'xyz' | 
| float | 8\-byte floating point number\.  | CAST\(0\.456 AS FLOAT\) | 
| decimal, numeric |  Base\-10 number, with maximum precision of 38 \(that is, the maximum number of significant digits\), and with scale within the range of \-231 to 231\-1 \(that is, the base\-10 exponent\)\. Amazon S3 Select ignores scale and precision when you provide both at the same time\.   | 123\.456  | 
| timestamp |  Time stamps represent a specific moment in time, always include a local offset, and are capable of arbitrary precision\. In the text format, time stamps follow the [W3C note on date and time formats](https://www.w3.org/TR/NOTE-datetime), but they must end with the literal "T" if not at least whole\-day precision\. Fractional seconds are allowed, with at least one digit of precision, and an unlimited maximum\. Local\-time offsets can be represented as either hour:minute offsets from UTC, or as the literal "Z" to denote a local time of UTC\. They are required on time stamps with time and are not allowed on date values\.  | CAST\('2007\-04\-05T14:30Z' AS TIMESTAMP\) | 

### Supported Parquet types<a name="s3-glacier-select-sql-reference-supported-data-types-parquet"></a>

Amazon S3 Select supports the following Parquet types\.
+ DATE
+ DECIMAL
+ ENUM
+ INT\(8\)
+ INT\(16\)
+ INT\(32\)
+ INT\(64\)
+ LIST
**Note**  
For LIST Parquet type output, Amazon S3 Select only supports JSON format\. However, if the query limits the data to simple values, the LIST Parquet type can also be queried in CSV format\.
+ STRING
+ TIMESTAMP supported precision \(MILLIS/MICROS/NANOS\)
**Note**  
Timestamps saved as an INT\(96\) are unsupported\.   
Due to the range of the INT\(64\) type, timestamps using the NANOS unit can only represent values between `1677-09-21 00:12:43` and `2262-04-11 23:47:16`\. Values outside of this range cannot be represented with the NANOS unit\.

### Mapping of Parquet types to supported data types in Amazon S3 Select<a name="s3-glacier-select-sql-reference-supported-data-types-parquet-mapping"></a>


| Parquet types | Supported data types | 
| --- | --- | 
| DATE |  timestamp  | 
|  DECIMAL  |  decimal, numeric  | 
|  ENUM  |  string  | 
|  INT\(8\)  |  int, integer  | 
|  INT\(16\)  | int, integer | 
| INT\(32\) | int, integer | 
|  INT\(64\)  |  decimal, numeric  | 
|  LIST  |  Each Parquet type in list is mapped to the corresponding data type  | 
|  STRING  |  string  | 
|  TIMESTAMP  |  timestamp  | 