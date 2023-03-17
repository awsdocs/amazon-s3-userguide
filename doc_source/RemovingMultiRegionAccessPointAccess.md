# Removing access to a Multi\-Region Access Point from a VPC endpoint<a name="RemovingMultiRegionAccessPointAccess"></a>

If you own a Multi\-Region Access Point and want to remove access to it from an interface endpoint, you must supply a new access policy for the Multi\-Region Access Point that prevents access for requests coming through VPC endpoints\. However, if the buckets in your Multi\-Region Access Point support requests through VPC endpoints, they will continue to support these requests\. If you want to prevent that support, you must also update the policies for the buckets\. Supplying a new access policy to the Multi\-Region Access Point prevents access only to the Multi\-Region Access Point, not to the underlying buckets\. 

**Note**  
You can't delete an access policy for a Multi\-Region Access Point\. To remove access to a Multi\-Region Access Point, you must provide a new access policy with the modified access that you want\. 

Instead of updating the access policy for the Multi\-Region Access Point, you can update the bucket policies to prevent requests through VPC endpoints\. In this case, users can still access the Multi\-Region Access Point through the VPC endpoint\. However, if the Multi\-Region Access Point request is routed to a bucket where the bucket policy prevents access, the request will generate an error message\. 