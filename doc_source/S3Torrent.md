# Retrieving Amazon S3 objects using BitTorrent<a name="S3Torrent"></a>

BitTorrent is an open, peer\-to\-peer protocol for distributing files\. You can use the BitTorrent protocol to retrieve any publicly\-accessible object in Amazon S3\. This section describes why you might want to use BitTorrent to distribute your data out of Amazon S3 and how to do so\.

Amazon S3 supports the BitTorrent protocol so that developers can save costs when distributing content at high scale\. Amazon S3 is useful for simple, reliable storage of any data\. The default distribution mechanism for Amazon S3 data is via client/server download\. In client/server distribution, the entire object is transferred point\-to\-point from Amazon S3 to every authorized user who requests that object\. While client/server delivery is appropriate for a wide variety of use cases, it is not optimal for everybody\. Specifically, the costs of client/server distribution increase linearly as the number of users downloading objects increases\. This can make it expensive to distribute popular objects\. 

BitTorrent addresses this problem by recruiting the very clients that are downloading the object as distributors themselves: Each client downloads some pieces of the object from Amazon S3 and some from other clients, while simultaneously uploading pieces of the same object to other interested "peers\." The benefit for publishers is that for large, popular files the amount of data actually supplied by Amazon S3 can be substantially lower than what it would have been serving the same clients via client/server download\. Less data transferred means lower costs for the publisher of the object\.

**Note**  
 Amazon S3 does not support the BitTorrent protocol in AWS Regions launched after May 30, 2016\.
You can only get a torrent file for objects that are less than 5 GBs in size\.

For more information, see the topics below\.

**Topics**
+ [How you are charged for BitTorrent delivery](#S3TorrentCharge)
+ [Using BitTorrent to retrieve objects stored in Amazon S3](#S3TorrentRetrieve)
+ [Publishing content using Amazon S3 and BitTorrent](#S3TorrentPublish)

## How you are charged for BitTorrent delivery<a name="S3TorrentCharge"></a>

There is no extra charge for use of BitTorrent with Amazon S3\. Data transfer via the BitTorrent protocol is metered at the same rate as client/server delivery\. To be precise, whenever a downloading BitTorrent client requests a "piece" of an object from the Amazon S3 "seeder," charges accrue just as if an anonymous request for that piece had been made using the REST or SOAP protocol\. These charges will appear on your Amazon S3 bill and usage reports in the same way\. The difference is that if a lot of clients are requesting the same object simultaneously via BitTorrent, then the amount of data Amazon S3 must serve to satisfy those clients will be lower than with client/server delivery\. This is because the BitTorrent clients are simultaneously uploading and downloading amongst themselves\.

**Note**  
 SOAP support over HTTP is deprecated, but it is still available over HTTPS\. New Amazon S3 features will not be supported for SOAP\. We recommend that you use either the REST API or the AWS SDKs\. 

The data transfer savings achieved from use of BitTorrent can vary widely depending on how popular your object is\. Less popular objects require heavier use of the "seeder" to serve clients, and thus the difference between BitTorrent distribution costs and client/server distribution costs might be small for such objects\. In particular, if only one client is ever downloading a particular object at a time, the cost of BitTorrent delivery will be the same as direct download\.

## Using BitTorrent to retrieve objects stored in Amazon S3<a name="S3TorrentRetrieve"></a>

Any object in Amazon S3 that can be read anonymously can also be downloaded via BitTorrent\. Doing so requires use of a BitTorrent client application\. Amazon does not distribute a BitTorrent client application, but there are many free clients available\. The Amazon S3BitTorrent implementation has been tested to work with the official BitTorrent client \(go to [http://www\.bittorrent\.com/](http://www.bittorrent.com/)\)\.

The starting point for a BitTorrent download is a \.torrent file\. This small file describes for BitTorrent clients both the data to be downloaded and where to get started finding that data\. A \.torrent file is a small fraction of the size of the actual object to be downloaded\. Once you feed your BitTorrent client application an Amazon S3 generated \.torrent file, it should start downloading immediately from Amazon S3 and from any "peer" BitTorrent clients\.

Retrieving a \.torrent file for any publicly available object is easy\. Simply add a "?torrent" query string parameter at the end of the REST GET request for the object\. No authentication is required\. Once you have a BitTorrent client installed, downloading an object using BitTorrent download might be as easy as opening this URL in your web browser\.

There is no mechanism to fetch the \.torrent for an Amazon S3 object using the SOAP API\.

**Note**  
 SOAP support over HTTP is deprecated, but it is still available over HTTPS\. New Amazon S3 features will not be supported for SOAP\. We recommend that you use either the REST API or the AWS SDKs\. 

**Example**  
This example retrieves the Torrent file for the "Nelson" object in the "quotes" bucket\.  
`Sample Request`  

```
1. GET /quotes/Nelson?torrent HTTP/1.0
2. Date: Wed, 25 Nov 2009 12:00:00 GMT
```
`Sample Response`  

```
1. HTTP/1.1 200 OK
2. x-amz-request-id: 7CD745EBB7AB5ED9
3. Date: Wed, 25 Nov 2009 12:00:00 GMT
4. Content-Disposition: attachment; filename=Nelson.torrent;
5. Content-Type: application/x-bittorrent
6. Content-Length: 537
7. Server: AmazonS3
8. 
9. <body: a Bencoded dictionary as defined by the BitTorrent specification>
```

## Publishing content using Amazon S3 and BitTorrent<a name="S3TorrentPublish"></a>

Every anonymously readable object stored in Amazon S3 is automatically available for download using BitTorrent\. The process for changing the ACL on an object to allow anonymous `READ` operations is described in [Identity and access management in Amazon S3](s3-access-control.md)\.

You can direct your clients to your BitTorrent accessible objects by giving them the \.torrent file directly or by publishing a link to the ?torrent URL of your object, as described by [GetObjectTorrent](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObjectTorrent.html) in the *Amazon Simple Storage Service API Reference*\. One important thing to note is that the \.torrent file describing an Amazon S3 object is generated on demand the first time it is requested \(via the REST ?torrent resource\)\. Generating the \.torrent for an object takes time proportional to the size of that object\. For large objects, this time can be significant\. Therefore, before publishing a ?torrent link, we suggest making the first request for it yourself\. Amazon S3 might take several minutes to respond to this first request, as it generates the \.torrent file\. Unless you update the object in question, subsequent requests for the \.torrent will be fast\. Following this procedure before distributing a ?torrent link will ensure a smooth BitTorrent downloading experience for your customers\.

To stop distributing a file using BitTorrent, simply remove anonymous access to it\. This can be accomplished by either deleting the file from Amazon S3, or modifying your access control policy to prohibit anonymous reads\. After doing so, Amazon S3 will no longer act as a "seeder" in the BitTorrent network for your file, and will no longer serve the \.torrent file via the ?torrent REST API\. However, after a \.torrent for your file is published, this action might not stop public downloads of your object that happen exclusively using the BitTorrent peer to peer network\.