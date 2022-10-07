# Conditional Functions<a name="s3-glacier-select-sql-reference-conditional"></a>

Amazon S3 Select support the following conditional functions\.

**Topics**
+ [CASE](#s3-glacier-select-sql-reference-case)
+ [COALESCE](#s3-glacier-select-sql-reference-coalesce)
+ [NULLIF](#s3-glacier-select-sql-reference-nullif)

## CASE<a name="s3-glacier-select-sql-reference-case"></a>

The CASE expression is a conditional expression, similar to if/then/else statements found in other languages\. CASE is used to specify a result when there are multiple conditions\. There are two types of CASE expressions: simple and searched\.

In simple CASE expressions, an expression is compared with a value\. When a match is found, the specified action in the THEN clause is applied\. If no match is found, the action in the ELSE clause is applied\.

In searched CASE expressions, each CASE is evaluated based on a Boolean expression, and the CASE statement returns the first matching CASE\. If no matching CASEs are found among the WHEN clauses, the action in the ELSE clause is returned\.

### Syntax<a name="s3-glacier-select-sql-reference-case-syntax"></a>

**Note**  
Currently, Amazon S3 Select doesn't support `ORDER BY` or queries that contain new lines\. Make sure that you use queries with no line breaks\.

Simple CASE statement used to match conditions:

```
CASE expression WHEN value THEN result [WHEN...] [ELSE result] END
```

Searched CASE statement used to evaluate each condition:

```
CASE WHEN boolean condition THEN result [WHEN ...] [ELSE result] END
```

### Examples<a name="s3-glacier-select-sql-reference-case-examples"></a>

**Note**  
If you use the Amazon S3 console to run the following examples and your CSV file contains a header row, please select **Exclude the first line of CSV data**\. 

**Example 1:** Use a simple CASE expression to replace New York City with Big Apple in a query\. Replace all other city names with other\.

```
SELECT venuecity, CASE venuecity WHEN 'New York City' THEN 'Big Apple' ELSE 'other' END FROM S3Object;
```

Query Result: 

```
venuecity        |   case
-----------------+-----------
Los Angeles      | other
New York City    | Big Apple
San Francisco    | other
Baltimore        | other
...
```

**Example 2:** Use a searched CASE expression to assign group numbers based on the PRICEPAID value for individual ticket sales:

```
SELECT pricepaid, CASE WHEN CAST(pricepaid as FLOAT) < 10000 THEN 'group 1' WHEN CAST(pricepaid as FLOAT) > 10000 THEN 'group 2' ELSE 'group 3' END FROM S3Object;
```

Query Result: 

```
pricepaid |  case
-----------+---------
12624.00 | group 2
10000.00 | group 3
10000.00 | group 3
9996.00 | group 1
9988.00 | group 1
...
```

## COALESCE<a name="s3-glacier-select-sql-reference-coalesce"></a>

Evaluates the arguments in order and returns the first non\-unknown, that is, the first non\-null or non\-missing\. This function does not propagate null and missing\.

### Syntax<a name="s3-glacier-select-sql-reference-coalesce-syntax"></a>

```
COALESCE ( expression, expression, ... )
```

### Parameters<a name="s3-glacier-select-sql-reference-coalesce-parameters"></a>

 *expression*   
The target expression that the function operates on\.

### Examples<a name="s3-glacier-select-sql-reference-coalesce-examples"></a>

```
COALESCE(1)                -- 1
COALESCE(null)             -- null
COALESCE(null, null)       -- null
COALESCE(missing)          -- null
COALESCE(missing, missing) -- null
COALESCE(1, null)          -- 1
COALESCE(null, null, 1)    -- 1
COALESCE(null, 'string')   -- 'string'
COALESCE(missing, 1)       -- 1
```

## NULLIF<a name="s3-glacier-select-sql-reference-nullif"></a>

Given two expressions, returns NULL if the two expressions evaluate to the same value; otherwise, returns the result of evaluating the first expression\.

### Syntax<a name="s3-glacier-select-sql-reference-nullif-syntax"></a>

```
NULLIF ( expression1, expression2 )
```

### Parameters<a name="s3-glacier-select-sql-reference-nullif-parameters"></a>

 *expression1, expression2*   
The target expressions that the function operates on\.

### Examples<a name="s3-glacier-select-sql-reference-nullif-examples"></a>

```
NULLIF(1, 1)             -- null
NULLIF(1, 2)             -- 1
NULLIF(1.0, 1)           -- null
NULLIF(1, '1')           -- 1
NULLIF([1], [1])         -- null
NULLIF(1, NULL)          -- 1
NULLIF(NULL, 1)          -- null
NULLIF(null, null)       -- null
NULLIF(missing, null)    -- null
NULLIF(missing, missing) -- null
```