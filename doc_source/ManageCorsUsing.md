--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Creating a cross\-origin resource sharing \(CORS\) configuration<a name="ManageCorsUsing"></a>

To configure your bucket to allow cross\-origin requests, you create a CORS configuration\. The CORS configuration is a document with rules that identify the origins that you will allow to access your bucket, the operations \(HTTP methods\) that will support for each origin, and other operation\-specific information\. You can add up to 100 rules to the configuration\. You can add the CORS configuration as the `cors` subresource to the bucket\.

If you are configuring CORS in the S3 console, you must use JSON to create a CORS configuration\. The new S3 console only supports JSON CORS configurations\. 

For more information about the CORS configuration and the elements in it, see the topics below\. For instructions on how to add a CORS configuration, see [Configuring cross\-origin resource sharing \(CORS\)](enabling-cors-examples.md)\.

**Important**  
In the S3 console, the CORS configuration must be JSON\. 

**Topics**
+ [Example 1](#cors-example-1)
+ [Example 2](#cors-example-2)
+ [AllowedMethod element](#cors-allowed-methods)
+ [AllowedOrigin element](#cors-allowed-origin)
+ [AllowedHeader element](#cors-allowed-headers)
+ [ExposeHeader element](#cors-expose-headers)
+ [MaxAgeSeconds element](#cors-max-age)

## Example 1<a name="cors-example-1"></a>

Instead of accessing a website by using an Amazon S3 website endpoint, you can use your own domain, such as `example1.com` to serve your content\. For information about using your own domain, see [Configuring a static website using a custom domain registered with Route 53](website-hosting-custom-domain-walkthrough.md)\. 

The following example `cors` configuration has three rules, which are specified as `CORSRule` elements:
+ The first rule allows cross\-origin PUT, POST, and DELETE requests from the `http://www.example1.com` origin\. The rule also allows all headers in a preflight OPTIONS request through the `Access-Control-Request-Headers` header\. In response to preflight OPTIONS requests, Amazon S3 returns requested headers\.
+ The second rule allows the same cross\-origin requests as the first rule, but the rule applies to another origin, `http://www.example2.com`\. 
+ The third rule allows cross\-origin GET requests from all origins\. The `*` wildcard character refers to all origins\. 

------
#### [ JSON ]

```
[
    {
        "AllowedHeaders": [
            "*"
        ],
        "AllowedMethods": [
            "PUT",
            "POST",
            "DELETE"
        ],
        "AllowedOrigins": [
            "http://www.example1.com"
        ],
        "ExposeHeaders": []
    },
    {
        "AllowedHeaders": [
            "*"
        ],
        "AllowedMethods": [
            "PUT",
            "POST",
            "DELETE"
        ],
        "AllowedOrigins": [
            "http://www.example2.com"
        ],
        "ExposeHeaders": []
    },
    {
        "AllowedHeaders": [],
        "AllowedMethods": [
            "GET"
        ],
        "AllowedOrigins": [
            "*"
        ],
        "ExposeHeaders": []
    }
]
```

------
#### [ XML ]

```
<CORSConfiguration>
 <CORSRule>
   <AllowedOrigin>http://www.example1.com</AllowedOrigin>

   <AllowedMethod>PUT</AllowedMethod>
   <AllowedMethod>POST</AllowedMethod>
   <AllowedMethod>DELETE</AllowedMethod>

   <AllowedHeader>*</AllowedHeader>
 </CORSRule>
 <CORSRule>
   <AllowedOrigin>http://www.example2.com</AllowedOrigin>

   <AllowedMethod>PUT</AllowedMethod>
   <AllowedMethod>POST</AllowedMethod>
   <AllowedMethod>DELETE</AllowedMethod>

   <AllowedHeader>*</AllowedHeader>
 </CORSRule>
 <CORSRule>
   <AllowedOrigin>*</AllowedOrigin>
   <AllowedMethod>GET</AllowedMethod>
 </CORSRule>
</CORSConfiguration>
```

------

## Example 2<a name="cors-example-2"></a>

The CORS configuration also allows optional configuration parameters, as shown in the following CORS configuration\. In this example, the CORS configuration allows cross\-origin PUT, POST, and DELETE requests from the `http://www.example.com` origin\.

------
#### [ JSON ]

```
[
    {
        "AllowedHeaders": [
            "*"
        ],
        "AllowedMethods": [
            "PUT",
            "POST",
            "DELETE"
        ],
        "AllowedOrigins": [
            "http://www.example.com"
        ],
        "ExposeHeaders": [
            "x-amz-server-side-encryption",
            "x-amz-request-id",
            "x-amz-id-2"
        ],
        "MaxAgeSeconds": 3000
    }
]
```

------
#### [ XML ]

```
<CORSConfiguration>
 <CORSRule>
   <AllowedOrigin>http://www.example.com</AllowedOrigin>
   <AllowedMethod>PUT</AllowedMethod>
   <AllowedMethod>POST</AllowedMethod>
   <AllowedMethod>DELETE</AllowedMethod>
   <AllowedHeader>*</AllowedHeader>
  <MaxAgeSeconds>3000</MaxAgeSeconds>
  <ExposeHeader>x-amz-server-side-encryption</ExposeHeader>
  <ExposeHeader>x-amz-request-id</ExposeHeader>
  <ExposeHeader>x-amz-id-2</ExposeHeader>
 </CORSRule>
</CORSConfiguration>
```

------

The `CORSRule` element in the preceding configuration includes the following optional elements:
+ `MaxAgeSeconds`—Specifies the amount of time in seconds \(in this example, 3000\) that the browser caches an Amazon S3 response to a preflight OPTIONS request for the specified resource\. By caching the response, the browser does not have to send preflight requests to Amazon S3 if the original request will be repeated\. 
+ `ExposeHeader`—Identifies the response headers \(in this example, `x-amz-server-side-encryption`, `x-amz-request-id`, and `x-amz-id-2`\) that customers are able to access from their applications \(for example, from a JavaScript `XMLHttpRequest` object\)\.

## AllowedMethod element<a name="cors-allowed-methods"></a>

In the CORS configuration, you can specify the following values for the `AllowedMethod` element\.
+ GET
+ PUT
+ POST
+ DELETE
+ HEAD

## AllowedOrigin element<a name="cors-allowed-origin"></a>

In the `AllowedOrigin` element, you specify the origins that you want to allow cross\-domain requests from, for example,` http://www.example.com`\. The origin string can contain only one `*` wildcard character, such as `http://*.example.com`\. You can optionally specify `*` as the origin to enable all the origins to send cross\-origin requests\. You can also specify `https` to enable only secure origins\.

## AllowedHeader element<a name="cors-allowed-headers"></a>

The `AllowedHeader` element specifies which headers are allowed in a preflight request through the `Access-Control-Request-Headers` header\. Each header name in the `Access-Control-Request-Headers` header must match a corresponding entry in the rule\. Amazon S3 will send only the allowed headers in a response that were requested\. For a sample list of headers that can be used in requests to Amazon S3, go to [Common Request Headers](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTCommonRequestHeaders.html) in the *Amazon Simple Storage Service API Reference* guide\.

Each AllowedHeader string in the rule can contain at most one \* wildcard character\. For example, `<AllowedHeader>x-amz-*</AllowedHeader>` will enable all Amazon\-specific headers\.

## ExposeHeader element<a name="cors-expose-headers"></a>

Each `ExposeHeader` element identifies a header in the response that you want customers to be able to access from their applications \(for example, from a JavaScript `XMLHttpRequest` object\)\. For a list of common Amazon S3 response headers, go to [Common Response Headers](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTCommonResponseHeaders.html) in the *Amazon Simple Storage Service API Reference* guide\.

## MaxAgeSeconds element<a name="cors-max-age"></a>

The `MaxAgeSeconds` element specifies the time in seconds that your browser can cache the response for a preflight request as identified by the resource, the HTTP method, and the origin\.