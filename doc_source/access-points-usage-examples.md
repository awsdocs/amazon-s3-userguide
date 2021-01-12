--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Using access points with compatible Amazon S3 operations<a name="access-points-usage-examples"></a>

The following examples demonstrate how to use access points with compatible operations in Amazon S3\.

**Topics**
+ [Request an object through an access point](#get-object-ap)
+ [Upload an object through an access point](#put-object-ap)
+ [Delete an object through an access point](#delete-object-ap)
+ [List objects through an access point](#list-object-ap)
+ [Add a tag set to an object through an access point](#add-tag-set-ap)
+ [Grant access permissions through an access point using an ACL](#acl-permissions-ap)

## Request an object through an access point<a name="get-object-ap"></a>

The following example requests the object `my-image.jpg` through the access point `prod` owned by account ID `123456789012` in Region `us-west-2`, and saves the downloaded file as `download.jpg`\.

------
#### [ AWS CLI ]

```
aws s3api get-object --key my-image.jpg --bucket arn:aws:s3:us-west-2:123456789012:accesspoint/prod download.jpg
```

------

## Upload an object through an access point<a name="put-object-ap"></a>

The following example uploads the object `my-image.jpg` through the access point `prod` owned by account ID `123456789012` in Region `us-west-2`\.

------
#### [ AWS CLI ]

```
aws s3api put-object --bucket arn:aws:s3:us-west-2:123456789012:accesspoint/prod --key my-image.jpg --body my-image.jpg
```

------

## Delete an object through an access point<a name="delete-object-ap"></a>

The following example deletes the object `my-image.jpg` through the access point `prod` owned by account ID `123456789012` in Region `us-west-2`\.

------
#### [ AWS CLI ]

```
aws s3api delete-object --bucket arn:aws:s3:us-west-2:123456789012:accesspoint/prod --key my-image.jpg
```

------

## List objects through an access point<a name="list-object-ap"></a>

The following example lists objects through the access point `prod` owned by account ID `123456789012` in Region `us-west-2`\.

------
#### [ AWS CLI ]

```
aws s3api list-objects-v2 --bucket arn:aws:s3:us-west-2:123456789012:accesspoint/prod
```

------

## Add a tag set to an object through an access point<a name="add-tag-set-ap"></a>

The following example adds a tag set to the existing object `my-image.jpg` through the access point `prod` owned by account ID `123456789012` in Region `us-west-2`\.

------
#### [ AWS CLI ]

```
aws s3api put-object-tagging --bucket arn:aws:s3:us-west-2:123456789012:accesspoint/prod --key my-image.jpg --tagging TagSet=[{Key="finance",Value="true"}]
```

------

## Grant access permissions through an access point using an ACL<a name="acl-permissions-ap"></a>

The following example applies an ACL to an existing object `my-image.jpg` through the access point `prod` owned by account ID `123456789012` in Region `us-west-2`\.

------
#### [ AWS CLI ]

```
aws s3api put-object-acl --bucket arn:aws:s3:us-west-2:123456789012:accesspoint/prod --key my-image.jpg --acl private
```

------