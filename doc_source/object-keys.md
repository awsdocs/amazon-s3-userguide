# Creating object key names<a name="object-keys"></a>

The *object key* \(or key name\) uniquely identifies the object in an Amazon S3 bucket\. *Object metadata* is a set of name\-value pairs\. For more information about object metadata, see [Working with object metadata](UsingMetadata.md)\.

When you create an object, you specify the key name, which uniquely identifies the object in the bucket\. For example, on the [Amazon S3 console](https://console.aws.amazon.com/s3/home), when you highlight a bucket, a list of objects in your bucket appears\. These names are the *object keys*\. The name for a key is a sequence of Unicode characters whose UTF\-8 encoding is at most 1,024 bytes long\. 

The Amazon S3 data model is a flat structure: You create a bucket, and the bucket store objects\. There is no hierarchy of subbuckets or subfolders\. However, you can infer logical hierarchy using key name prefixes and delimiters as the Amazon S3 console does\. The Amazon S3 console supports a concept of folders\. For more information about how to edit metadata from the Amazon S3 console, see [Editing object metadata in the Amazon S3 console](add-object-metadata.md)\.

Suppose that your bucket \(`admin-created`\) has four objects with the following object keys:

`Development/Projects.xls`

`Finance/statement1.pdf`

`Private/taxdocument.pdf`

`s3-dg.pdf`

The console uses the key name prefixes \(`Development/`, `Finance/`, and `Private/`\) and delimiter \('/'\) to present a folder structure\. The `s3-dg.pdf` key does not have a prefix, so its object appears directly at the root level of the bucket\. If you open the `Development/` folder, you see the `Projects.xlsx` object in it\. 
+ Amazon S3 supports buckets and objects, and there is no hierarchy\. However, by using prefixes and delimiters in an object key name, the Amazon S3 console and the AWS SDKs can infer hierarchy and introduce the concept of folders\.
+ The Amazon S3 console implements folder object creation by creating a zero\-byte object with the folder *prefix and delimiter* value as the key\. These folder objects don't appear in the console\. Otherwise they behave like any other objects and can be viewed and manipulated through the REST API, AWS CLI, and AWS SDKs\.

## Object key naming guidelines<a name="object-key-guidelines"></a>

You can use any UTF\-8 character in an object key name\. However, using certain characters in key names can cause problems with some applications and protocols\. The following guidelines help you maximize compliance with DNS, web\-safe characters, XML parsers, and other APIs\. 

### Safe characters<a name="object-key-guidelines-safe-characters"></a>

The following character sets are generally safe for use in key names\.


|  |  | 
| --- |--- |
| Alphanumeric characters |    0\-9   a\-z   A\-Z    | 
| Special characters |    Forward slash \(`/`\)   Exclamation point \(`!`\)   Hyphen \(`-`\)   Underscore \(`_`\)   Period \(`.`\)   Asterisk \(`*`\)   Single quote \(`'`\)   Open parenthesis \(`(`\)   Close parenthesis \(`)`\)    | 

The following are examples of valid object key names:
+ `4my-organization`
+ `my.great_photos-2014/jan/myvacation.jpg`
+ `videos/2014/birthday/video1.wmv`

**Note**  
Objects with key names ending with period\(s\) "\." downloaded using the Amazon S3 console will have the period\(s\) "\." removed from the key name of the downloaded object\. To download an object with the key name ending in period\(s\) "\." retained in the downloaded object, you must use the AWS Command Line Interface \(AWS CLI\), AWS SDKs, or REST API\.

### Characters that might require special handling<a name="object-key-guidelines-special-handling"></a>

The following characters in a key name might require additional code handling and likely need to be URL encoded or referenced as HEX\. Some of these are non\-printable characters that your browser might not handle, which also requires special handling:
+ Ampersand \("&"\) 
+ Dollar \("$"\) 
+ ASCII character ranges 00–1F hex \(0–31 decimal\) and 7F \(127 decimal\) 
+ 'At' symbol \("@"\) 
+ Equals \("="\) 
+ Semicolon \(";"\) 
+ Colon \(":"\) 
+ Plus \("\+"\) 
+ Space – Significant sequences of spaces might be lost in some uses \(especially multiple spaces\) 
+ Comma \(","\) 
+ Question mark \("?"\) 

### Characters to avoid<a name="object-key-guidelines-avoid-characters"></a>

Avoid the following characters in a key name because of significant special handling for consistency across all applications\. 
+ Backslash \("\\"\) 
+ Left curly brace \("\{"\) 
+ Non\-printable ASCII characters \(128–255 decimal characters\)
+ Caret \("^"\) 
+ Right curly brace \("\}"\) 
+ Percent character \("%"\) 
+ Grave accent / back tick \("`"\) 
+ Right square bracket \("\]"\) 
+ Quotation marks 
+ 'Greater Than' symbol \(">"\) 
+ Left square bracket \("\["\) 
+ Tilde \("\~"\) 
+ 'Less Than' symbol \("<"\) 
+ 'Pound' character \("\#"\) 
+ Vertical bar / pipe \("\|"\) 

### XML related object key constraints<a name="object-key-xml-related-constraints"></a>

As specified by the [XML standard on end\-of\-line handling](https://www.w3.org/TR/REC-xml/#sec-line-ends), all XML text is normalized such that single carriage returns \(ASCII code 13\) and carriage returns immediately followed by a line feed \(ASCII code 10\) are replaced by a single line feed character\. To ensure the correct parsing of object keys in XML requests, carriage returns and [other special characters must be replaced with their equivalent XML entity code](https://www.w3.org/TR/xml/#syntax) when they are inserted within XML tags\. The following is a list of such special characters and their equivalent entity codes:
+ ' as `&apos;`
+ ” as `&quot;`
+ & as `&amp;`
+ < as `&lt;`
+ > as `&gt;`
+ \\r as `&#13;` or `&#x0D;`
+ \\n as `&#10;` or `&#x0A;`

**Example**  
The following example illustrates the use of an XML entity code as a substitution for a carriage return\. This `DeleteObjects` request deletes an object with the `key` parameter: `/some/prefix/objectwith\rcarriagereturn` \(where the \\r is the carriage return\)\.  

```
<Delete xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
  <Object>
    <Key>/some/prefix/objectwith&#13;carriagereturn</Key>
  </Object>
</Delete>
```