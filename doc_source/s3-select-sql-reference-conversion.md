# Conversion functions<a name="s3-select-sql-reference-conversion"></a>

Amazon S3 Select supports the following conversion function\.

**Topics**
+ [CAST](#s3-select-sql-reference-cast)

## CAST<a name="s3-select-sql-reference-cast"></a>

The `CAST` function converts an entity, such as an expression that evaluates to a single value, from one type to another\. 

### Syntax<a name="s3-select-sql-reference-cast-syntax"></a>

```
CAST ( expression AS data_type )
```

### Parameters<a name="s3-select-sql-reference-cast-parameters"></a>

 *`expression`*   
A combination of one or more values, operators, and SQL functions that evaluate to a value\.

 *`data_type`*   
The target data type, such as `INT`, to cast the expression to\. For a list of supported data types, see [Data types](s3-select-sql-reference-data-types.md)\.

### Examples<a name="s3-select-sql-reference-cast-examples"></a>

```
CAST('2007-04-05T14:30Z' AS TIMESTAMP)
CAST(0.456 AS FLOAT)
```