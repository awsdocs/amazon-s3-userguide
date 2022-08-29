# Appendix a: Using the SOAP API<a name="SOAPAPI3"></a>

**Note**  
 SOAP support over HTTP is deprecated, but SOAP is still available over HTTPS\. New Amazon S3 features are not supported for SOAP\. Instead of using SOAP, we recommend that you use either the REST API or the AWS SDKs\. 

This section contains information specific to the Amazon S3 SOAP API\. 

**Note**  
SOAP requests, both authenticated and anonymous, must be sent to Amazon S3 using SSL\. Amazon S3 returns an error when you send a SOAP request over HTTP\. 

**Topics**
+ [Common SOAP API elements](UsingSOAPOperations.md)
+ [Authenticating SOAP requests](SOAPAuthentication.md)
+ [Setting access policy with SOAP](SOAPAccessPolicy.md)