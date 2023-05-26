# Data types<a name="s3-select-sql-reference-data-types"></a>

Amazon S3 Select supports several primitive data types\.

## Data type conversions<a name="s3-select-sql-reference-data-conversion"></a>

The general rule is to follow the `CAST` function if it's defined\. If `CAST` is not defined, then all input data is treated as a string\. In that case, you must cast your input data into the relevant data types when necessary\.

For more information about the `CAST` function, see [CAST](s3-select-sql-reference-conversion.md#s3-select-sql-reference-cast)\.

## Supported data types<a name="s3-select-sql-reference-supported-data-types"></a>

Amazon S3 Select supports the following set of primitive data types\.


|  Name  |  Description  |  Examples  | 
| --- | --- | --- | 
| `bool` | A Boolean value, either `TRUE` or `FALSE`\. | `FALSE` | 
| `int`, `integer` | An 8\-byte signed integer in the range \-9,223,372,036,854,775,808 to 9,223,372,036,854,775,807\.  | `100000` | 
| `string` | A UTF8\-encoded variable\-length string\. The default limit is 1 character\. The maximum character limit is 2,147,483,647\.  | `'xyz'` | 
| `float` | An 8\-byte floating point number\.  | `CAST(0.456 AS FLOAT)` | 
| `decimal`, `numeric` |  A base\-10 number, with a maximum precision of 38 \(that is, the maximum number of significant digits\), and with a scale within the range of \-231 to 231\-1 \(that is, the base\-10 exponent\)\.  Amazon S3 Select ignores scale and precision when you provide both at the same time\.   | `123.456 ` | 
| `timestamp` |  Timestamps represent a specific moment in time, always include a local offset, and are capable of arbitrary precision\. In the text format, timestamps follow the [W3C note on date and time formats](https://www.w3.org/TR/NOTE-datetime), but they must end with the literal `T` if the timestamps are not at least whole\-day precision\. Fractional seconds are allowed, with at least one digit of precision, and an unlimited maximum\. Local\-time offsets can be represented as either hour:minute offsets from UTC, or as the literal `Z` to denote a local time of UTC\. Local\-time offsets are required on timestamps with time and are not allowed on date values\.  | `CAST('2007-04-05T14:30Z' AS TIMESTAMP)` | 

### Supported Parquet types<a name="s3-select-sql-reference-supported-data-types-parquet"></a>

Amazon S3 Select supports the following Parquet types\.
+ `DATE`
+ `DECIMAL`
+ `ENUM`
+ `INT(8)`
+ `INT(16)`
+ `INT(32)`
+ `INT(64)`
+ `LIST`
**Note**  
For `LIST` Parquet type output, Amazon S3 Select supports only JSON format\. However, if the query limits the data to simple values, the `LIST` Parquet type can also be queried in CSV format\.
+ `STRING`
+ `TIMESTAMP` supported precision \(`MILLIS`/`MICROS`/`NANOS`\)
**Note**  
Timestamps saved as an `INT(96)` are unsupported\.   
Because of the range of the `INT(64)` type, timestamps that are using the `NANOS` unit can represent only values between `1677-09-21 00:12:43` and `2262-04-11 23:47:16`\. Values outside of this range cannot be represented with the `NANOS` unit\.

### Mapping of Parquet types to supported data types in Amazon S3 Select<a name="s3-select-sql-reference-supported-data-types-parquet-mapping"></a>


| Parquet types | Supported data types | 
| --- | --- | 
| `DATE` |  `timestamp`  | 
|  `DECIMAL`  |  `decimal`, `numeric`  | 
|  `ENUM`  |  `string`  | 
|  `INT(8)`  |  `int`, `integer`  | 
|  `INT(16)`  | `int`, `integer` | 
| `INT(32)` | `int`, `integer` | 
|  `INT(64)`  |  `decimal`, `numeric`  | 
|  `LIST`  |  Each Parquet type in list is mapped to the corresponding data type\.  | 
|  `STRING`  |  `string`  | 
|  `TIMESTAMP`  |  `timestamp`  | 