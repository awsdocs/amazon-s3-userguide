# SELECT command<a name="s3-select-sql-reference-select"></a>

Amazon S3 Select supports only the `SELECT` SQL command\. The following ANSI standard clauses are supported for `SELECT`: 


+ `SELECT` list
+ `FROM` clause 
+ `WHERE` clause
+ `LIMIT` clause

**Note**  
Amazon S3 Select queries currently do not support subqueries or joins\.

## SELECT list<a name="s3-select-sql-reference-select-list"></a>

The `SELECT` list names the columns, functions, and expressions that you want the query to return\. The list represents the output of the query\. 

```
SELECT *
SELECT projection1 AS column_alias_1, projection2 AS column_alias_2
```

The first form of `SELECT` with the `*` \(asterisk\) returns every row that passed the `WHERE` clause, as\-is\. The second form of `SELECT` creates a row with user\-defined output scalar expressions **`projection1`** and **`projection2`** for each column\.

## FROM clause<a name="s3-select-sql-reference-from"></a>

Amazon S3 Select supports the following forms of the `FROM` clause:

```
FROM table_name
FROM table_name alias
FROM table_name AS alias
```

In each form of the `FROM` clause, `table_name` is the `S3Object` that's being queried\. Users coming from traditional relational databases can think of this as a database schema that contains multiple views over a table\.

Following standard SQL, the `FROM` clause creates rows that are filtered in the `WHERE` clause and projected in the `SELECT` list\. 

For JSON objects that are stored in Amazon S3 Select, you can also use the following forms of the `FROM` clause:

```
FROM S3Object[*].path
FROM S3Object[*].path alias
FROM S3Object[*].path AS alias
```

Using this form of the `FROM` clause, you can select from arrays or objects within a JSON object\. You can specify `path` by using one of the following forms:
+ By name \(in an object\): `.name` or `['name']`
+ By index \(in an array\): `[index]`
+ By wildcard character \(in an object\): `.*`
+ By wildcard character \(in an array\): `[*]`

**Note**  
This form of the `FROM` clause works only with JSON objects\.
Wildcard characters always emit at least one record\. If no record matches, then Amazon S3 Select emits the value `MISSING`\. During output serialization \(after the query finishes running\), Amazon S3 Select replaces `MISSING` values with empty records\.
Aggregate functions \(`AVG`, `COUNT`, `MAX`, `MIN`, and `SUM`\) skip `MISSING` values\.
If you don't provide an alias when using a wildcard character, you can refer to the row by using the last element in the path\. For example, you could select all prices from a list of books by using the query `SELECT price FROM S3Object[*].books[*].price`\. If the path ends in a wildcard character instead of a name, then you can use the value `_1` to refer to the row\. For example, instead of `SELECT price FROM S3Object[*].books[*].price`, you could use the query `SELECT _1.price FROM S3Object[*].books[*]`\.
Amazon S3 Select always treats a JSON document as an array of root\-level values\. Thus, even if the JSON object that you are querying has only one root element, the `FROM` clause must begin with `S3Object[*]`\. However, for compatibility reasons, Amazon S3 Select allows you to omit the wildcard character if you don't include a path\. Thus, the complete clause `FROM S3Object` is equivalent to `FROM S3Object[*] as S3Object`\. If you include a path, you must also use the wildcard character\. So, `FROM S3Object` and `FROM S3Object[*].path` are both valid clauses, but `FROM S3Object.path` is not\.

**Example**  
**Examples:**  
*Example \#1*  
This example shows results when using the following dataset and query:  

```
{ "Rules": [ {"id": "1"}, {"expr": "y > x"}, {"id": "2", "expr": "z = DEBUG"} ]}
{ "created": "June 27", "modified": "July 6" }
```

```
SELECT id FROM S3Object[*].Rules[*].id
```

```
{"id":"1"}
{}
{"id":"2"}
{}
```
Amazon S3 Select produces each result for the following reasons:  
+ `{"id":"id-1"}` – `S3Object[0].Rules[0].id` produced a match\.
+ `{}` – `S3Object[0].Rules[1].id` did not match a record, so Amazon S3 Select emitted `MISSING`, which was then changed to an empty record during output serialization and returned\.
+ `{"id":"id-2"}` – `S3Object[0].Rules[2].id` produced a match\.
+ `{}` – `S3Object[1]` did not match on `Rules`, so Amazon S3 Select emitted `MISSING`, which was then changed to an empty record during output serialization and returned\.
If you don't want Amazon S3 Select to return empty records when it doesn't find a match, you can test for the value `MISSING`\. The following query returns the same results as the previous query, but with the empty values omitted:  

```
SELECT id FROM S3Object[*].Rules[*].id WHERE id IS NOT MISSING
```

```
{"id":"1"}
{"id":"2"}
```
*Example \#2*  
This example shows results when using the following dataset and queries:  

```
{ "created": "936864000", "dir_name": "important_docs", "files": [ { "name": "." }, { "name": ".." }, { "name": ".aws" }, { "name": "downloads" } ], "owner": "Amazon S3" }
{ "created": "936864000", "dir_name": "other_docs", "files": [ { "name": "." }, { "name": ".." }, { "name": "my stuff" }, { "name": "backup" } ], "owner": "User" }
```

```
SELECT d.dir_name, d.files FROM S3Object[*] d
```

```
{"dir_name":"important_docs","files":[{"name":"."},{"name":".."},{"name":".aws"},{"name":"downloads"}]}
{"dir_name":"other_docs","files":[{"name":"."},{"name":".."},{"name":"my stuff"},{"name":"backup"}]}
```

```
SELECT _1.dir_name, _1.owner FROM S3Object[*]
```

```
{"dir_name":"important_docs","owner":"Amazon S3"}
{"dir_name":"other_docs","owner":"User"}
```

## WHERE clause<a name="s3-select-sql-reference-where"></a>

The `WHERE` clause follows this syntax: 

```
WHERE condition
```

The `WHERE` clause filters rows based on the `condition`\. A condition is an expression that has a Boolean result\. Only rows for which the condition evaluates to `TRUE` are returned in the result\.

## LIMIT clause<a name="s3-select-sql-reference-limit"></a>

The `LIMIT` clause follows this syntax: 

```
LIMIT number
```

The `LIMIT` clause limits the number of records that you want the query to return based on `number`\.

## Attribute access<a name="s3-select-sql-reference-attribute-access"></a>

The `SELECT` and `WHERE` clauses can refer to record data by using one of the methods in the following sections, depending on whether the file that is being queried is in CSV or JSON format\.

### CSV<a name="s3-select-sql-reference-attribute-access-csv"></a>
+ **Column Numbers** – You can refer to the *Nth* column of a row with the column name `_N`, where *`N`* is the column position\. The position count starts at 1\. For example, the first column is named `_1` and the second column is named `_2`\.

  You can refer to a column as `_N` or `alias._N`\. For example, `_2` and `myAlias._2` are both valid ways to refer to a column in the `SELECT` list and `WHERE` clause\.
+ **Column Headers** – For objects in CSV format that have a header row, the headers are available to the `SELECT` list and `WHERE` clause\. In particular, as in traditional SQL, within `SELECT` and `WHERE` clause expressions, you can refer to the columns by `alias.column_name` or `column_name`\.

### JSON<a name="s3-select-sql-reference-attribute-access-json"></a>
+ **Document** – You can access JSON document fields as `alias.name`\. You can also access nested fields, for example, `alias.name1.name2.name3`\.
+ **List** – You can access elements in a JSON list by using zero\-based indexes with the `[]` operator\. For example, you can access the second element of a list as `alias[1]`\. You can combine accessing list elements with fields, for example, `alias.name1.name2[1].name3`\.
+ **Examples:** Consider this JSON object as a sample dataset:

  ```
  {"name": "Susan Smith",
  "org": "engineering",
  "projects":
      [
       {"project_name":"project1", "completed":false},
       {"project_name":"project2", "completed":true}
      ]
  }
  ```

  *Example \#1*

  The following query returns these results:

  ```
  Select s.name from S3Object s
  ```

  ```
  {"name":"Susan Smith"}
  ```

  *Example \#2*

  The following query returns these results:

  ```
  Select s.projects[0].project_name from S3Object s
  ```

  ```
  {"project_name":"project1"}
  ```

## Case sensitivity of header and attribute names<a name="s3-select-sql-reference-case-sensitivity"></a>

With Amazon S3 Select, you can use double quotation marks to indicate that column headers \(for CSV objects\) and attributes \(for JSON objects\) are case sensitive\. Without double quotation marks, object headers and attributes are case insensitive\. An error is thrown in cases of ambiguity\.

The following examples are either 1\) Amazon S3 objects in CSV format with the specified column headers, and with `FileHeaderInfo` set to `"Use"` for the query request; or 2\) Amazon S3 objects in JSON format with the specified attributes\.

*Example \#1:* The object being queried has the header or attribute `NAME`\.
+ The following expression successfully returns values from the object\. Because there are no quotation marks, the query is case insensitive\.

  ```
  SELECT s.name from S3Object s
  ```
+ The following expression results in a 400 error `MissingHeaderName`\. Because there are quotation marks, the query is case sensitive\. 

  ```
  SELECT s."name" from S3Object s
  ```

*Example \#2:* The Amazon S3 object being queried has one header or attribute with `NAME` and another header or attribute with `name`\.
+ The following expression results in a 400 error `AmbiguousFieldName`\. Because there are no quotation marks, the query is case insensitive, but there are two matches, so the error is thrown\.

  ```
  SELECT s.name from S3Object s
  ```
+ The following expression successfully returns values from the object\. Because there are quotation marks, the query is case sensitive, so there is no ambiguity\.

  ```
  SELECT s."NAME" from S3Object s
  ```

## Using reserved keywords as user\-defined terms<a name="s3-select-sql-reference-using-keywords"></a>

Amazon S3 Select has a set of reserved keywords that are needed to run the SQL expressions used to query object content\. Reserved keywords include function names, data types, operators, and so on\. In some cases, user\-defined terms, such as the column headers \(for CSV files\) or attributes \(for JSON objects\), might clash with a reserved keyword\. When this happens, you must use double quotation marks to indicate that you are intentionally using a user\-defined term that clashes with a reserved keyword\. Otherwise a 400 parse error will result\.

For the full list of reserved keywords, see [Reserved keywords](s3-select-sql-reference-keyword-list.md)\.

The following example is either 1\) an Amazon S3 object in CSV format with the specified column headers, with `FileHeaderInfo` set to `"Use"` for the query request, or 2\) an Amazon S3 object in JSON format with the specified attributes\.

*Example:* The object being queried has a header or attribute named `CAST`, which is a reserved keyword\.
+ The following expression successfully returns values from the object\. Because quotation marks are used in the query, S3 Select uses the user\-defined header or attribute\.

  ```
  SELECT s."CAST" from S3Object s
  ```
+ The following expression results in a 400 parse error\. Because no quotation marks are used in the query, `CAST` clashes with a reserved keyword\.

  ```
  SELECT s.CAST from S3Object s
  ```

## Scalar expressions<a name="s3-select-sql-reference-scalar"></a>

Within the `WHERE` clause and the `SELECT` list, you can have SQL *scalar expressions*, which are expressions that return scalar values\. They have the following form:
+ ***`literal`*** 

  An SQL literal\. 
+ ***`column_reference`*** 

  A reference to a column in the form `column_name` or `alias.column_name`\. 
+ **`unary_op`** **`expression`** 

  In this case, ****`unary_op`**** is an SQL unary operator\.
+ **`expression`** **`binary_op`** ***`expression`*** 

   In this case, ****`binary_op`**** is an SQL binary operator\. 
+ **`func_name`** 

   In this case, **`func_name`** is the name of the scalar function to invoke\. 
+ ***`expression`*** `[ NOT ] BETWEEN` ****`expression`**** `AND` ****`expression`****
+ ***`expression`*** `LIKE` ****`expression`**** \[ `ESCAPE` ***`expression`*** \]