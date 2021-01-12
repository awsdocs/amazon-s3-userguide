--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Operators<a name="s3-glacier-select-sql-reference-operators"></a>

Amazon S3 Select and S3 Glacier Select support the following operators\.

## Logical Operators<a name="s3-glacier-select-sql-reference-loical-ops"></a>


+ `AND`
+ `NOT`
+ `OR`

## Comparison Operators<a name="s3-glacier-select-sql-reference-compare-ops"></a>


+ `<` 
+ `>` 
+ `<=`
+ `>=`
+ `=`
+ `<>`
+ `!=`
+ `BETWEEN`
+ `IN` – For example: `IN ('a', 'b', 'c')`

  

## Pattern Matching Operators<a name="s3-glacier-select-sql-reference-pattern"></a>
+ `LIKE`
+ `_` \(Matches any character\)
+ `%` \(Matches any sequence of characters\)

## Unitary Operators<a name="s3-glacier-select-sql-reference-unitary-ops"></a>
+ IS NULL
+ IS NOT NULL

## Math Operators<a name="s3-glacier-select-sql-referencemath-ops"></a>

Addition, subtraction, multiplication, division, and modulo are supported\.
+ \+
+ \-
+ \*
+ /
+ %

## Operator Precedence<a name="s3-glacier-select-sql-reference-op-Precedence"></a>

The following table shows the operators' precedence in decreasing order\.


|  Operator/Element  |  Associativity |  Required  | 
| --- | --- | --- | 
| \-  | right  | unary minus  | 
| \*, /, %  | left  | multiplication, division, modulo  | 
| \+, \-  | left  | addition, subtraction  | 
| IN |  | set membership  | 
| BETWEEN |  | range containment  | 
| LIKE |  | string pattern matching  | 
| <> |  | less than, greater than  | 
| = | right  | equality, assignment | 
| NOT | right | logical negation  | 
| AND | left | logical conjunction  | 
| OR | left | logical disjunction  | 