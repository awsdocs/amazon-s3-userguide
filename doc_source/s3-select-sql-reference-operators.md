# Operators<a name="s3-select-sql-reference-operators"></a>

Amazon S3 Select supports the following operators\.

## Logical operators<a name="s3-select-sql-reference-loical-ops"></a>
+ `AND`
+ `NOT`
+ `OR`

## Comparison operators<a name="s3-select-sql-reference-compare-ops"></a>
+ `<` 
+ `>` 
+ `<=`
+ `>=`
+ `=`
+ `<>`
+ `!=`
+ `BETWEEN`
+ `IN` – For example: `IN ('a', 'b', 'c')`

  

## Pattern\-matching operators<a name="s3-select-sql-reference-pattern"></a>
+ `LIKE`
+ `_` \(Matches any character\)
+ `%` \(Matches any sequence of characters\)

## Unitary operators<a name="s3-select-sql-reference-unitary-ops"></a>
+ `IS NULL`
+ `IS NOT NULL`

## Math operators<a name="s3-select-sql-referencemath-ops"></a>

Addition, subtraction, multiplication, division, and modulo are supported, as follows:
+ \+
+ \-
+ \*
+ /
+ %

## Operator precedence<a name="s3-select-sql-reference-op-Precedence"></a>

The following table shows the operators' precedence in decreasing order\.


|  Operator or element  |  Associativity |  Required  | 
| --- | --- | --- | 
| `-`  | right  | unary minus  | 
| `*`, `/`, `%`  | left  | multiplication, division, modulo  | 
| `+`, `-`  | left  | addition, subtraction  | 
| `IN` |  | set membership  | 
| `BETWEEN` |  | range containment  | 
| `LIKE` |  | string pattern matching  | 
| `<``>` |  | less than, greater than  | 
| `=` | right  | equality, assignment | 
| `NOT` | right | logical negation  | 
| `AND` | left | logical conjunction  | 
| `OR` | left | logical disjunction  | 