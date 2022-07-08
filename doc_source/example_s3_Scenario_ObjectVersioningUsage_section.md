# Work with Amazon S3 versioned objects using an AWS SDK<a name="example_s3_Scenario_ObjectVersioningUsage_section"></a>

The following code example shows how to work with Amazon S3 versioned objects\.

**Note**  
The source code for these examples is in the [AWS Code Examples GitHub repository](https://github.com/awsdocs/aws-doc-sdk-examples)\. Have feedback on a code example? [Create an Issue](https://github.com/awsdocs/aws-doc-sdk-examples/issues/new/choose) in the code examples repo\. 

------
#### [ Python ]

**SDK for Python \(Boto3\)**  
 To learn how to set up and run this example, see [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/python/example_code/s3/s3_versioning#code-examples)\. 
Call some of the functions defined in the API examples section to upload the stanza of a poem to a versioned object and perform a series of actions on it\.  

```
def usage_demo_single_object(obj_prefix='demo-versioning/'):
    """
    Demonstrates usage of versioned object functions. This demo uploads a stanza
    of a poem and performs a series of revisions, deletions, and revivals on it.

    :param obj_prefix: The prefix to assign to objects created by this demo.
    """
    with open('father_william.txt') as file:
        stanzas = file.read().split('\n\n')

    width = get_terminal_size((80, 20))[0]
    print('-'*width)
    print("Welcome to the usage demonstration of Amazon S3 versioning.")
    print("This demonstration uploads a single stanza of a poem to an Amazon "
          "S3 bucket and then applies various revisions to it.")
    print('-'*width)
    print("Creating a version-enabled bucket for the demo...")
    bucket = create_versioned_bucket('bucket-' + str(uuid.uuid1()), obj_prefix)

    print("\nThe initial version of our stanza:")
    print(stanzas[0])

    # Add the first stanza and revise it a few times.
    print("\nApplying some revisions to the stanza...")
    obj_stanza_1 = bucket.Object(f'{obj_prefix}stanza-1')
    obj_stanza_1.put(Body=bytes(stanzas[0], 'utf-8'))
    obj_stanza_1.put(Body=bytes(stanzas[0].upper(), 'utf-8'))
    obj_stanza_1.put(Body=bytes(stanzas[0].lower(), 'utf-8'))
    obj_stanza_1.put(Body=bytes(stanzas[0][::-1], 'utf-8'))
    print("The latest version of the stanza is now:",
          obj_stanza_1.get()['Body'].read().decode('utf-8'),
          sep='\n')

    # Versions are returned in order, most recent first.
    obj_stanza_1_versions = bucket.object_versions.filter(Prefix=obj_stanza_1.key)
    print(
        "The version data of the stanza revisions:",
        *[f"    {version.version_id}, last modified {version.last_modified}"
            for version in obj_stanza_1_versions],
        sep='\n'
    )

    # Rollback two versions.
    print("\nRolling back two versions...")
    rollback_object(bucket, obj_stanza_1.key, list(obj_stanza_1_versions)[2].version_id)
    print("The latest version of the stanza:",
          obj_stanza_1.get()['Body'].read().decode('utf-8'),
          sep='\n')

    # Delete the stanza
    print("\nDeleting the stanza...")
    obj_stanza_1.delete()
    try:
        obj_stanza_1.get()
    except ClientError as error:
        if error.response['Error']['Code'] == 'NoSuchKey':
            print("The stanza is now deleted (as expected).")
        else:
            raise

    # Revive the stanza
    print("\nRestoring the stanza...")
    revive_object(bucket, obj_stanza_1.key)
    print("The stanza is restored! The latest version is again:",
          obj_stanza_1.get()['Body'].read().decode('utf-8'),
          sep='\n')

    # Permanently delete all versions of the object. This cannot be undone!
    print("\nPermanently deleting all versions of the stanza...")
    permanently_delete_object(bucket, obj_stanza_1.key)
    obj_stanza_1_versions = bucket.object_versions.filter(Prefix=obj_stanza_1.key)
    if len(list(obj_stanza_1_versions)) == 0:
        print("The stanza has been permanently deleted and now has no versions.")
    else:
        print("Something went wrong. The stanza still exists!")

    print(f"\nRemoving {bucket.name}...")
    bucket.delete()
    print(f"{bucket.name} deleted.")
    print("Demo done!")
```

------

For a complete list of AWS SDK developer guides and code examples, see [Using this service with an AWS SDK](UsingAWSSDK.md#sdk-general-information-section)\. This topic also includes information about getting started and details about previous SDK versions\.