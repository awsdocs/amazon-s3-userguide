# Developing with Amazon S3 using the REST API<a name="developing-rest-api"></a>

The Amazon S3 architecture is designed to be programming language\-neutral, using our supported interfaces to store and retrieve objects\. 

Amazon S3 currently provides a REST interface\. With REST, metadata is returned in HTTP headers\. Because we only support HTTP requests of up to 4 KB \(not including the body\), the amount of metadata you can supply is restricted\. The REST API is an HTTP interface to Amazon S3\. Using REST, you use standard HTTP requests to create, fetch, and delete buckets and objects\.

You can use any toolkit that supports HTTP to use the REST API\. You can even use a browser to fetch objects, as long as they are anonymously readable\.

The REST API uses the standard HTTP headers and status codes, so that standard browsers and toolkits work as expected\. In some areas, we have added functionality to HTTP \(for example, we added headers to support access control\)\. In these cases, we have done our best to add the new functionality in a way that matched the style of standard HTTP usage\.

For more information about sending requests using the REST API, see [Making requests using the REST API](RESTAPI.md)\. For some considerations you should keep in mind when using the REST API, see the topics below\.

**Topics**
+ [Request routing](UsingRouting.md)