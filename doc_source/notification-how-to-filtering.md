--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Configuring notifications with object key name filtering<a name="notification-how-to-filtering"></a>

You can configure notifications to be filtered by the prefix and suffix of the key name of objects\. For example, you can set up a configuration so that you are sent a notification only when image files with a "`.jpg`" file name extension are added to a bucket\. Or, you can have a configuration that delivers a notification to an Amazon SNS topic when an object with the prefix "`images/`" is added to the bucket, while having notifications for objects with a "`logs/`" prefix in the same bucket delivered to an AWS Lambda function\. 

You can set up notification configurations that use object key name filtering in the Amazon S3 console and by using Amazon S3 APIs through the AWS SDKs or the REST APIs directly\. For information about using the console UI to set a notification configuration on a bucket, see [Enabling and configuring event notifications using the Amazon S3 console](enable-event-notifications.md)\. 

Amazon S3 stores the notification configuration as XML in the *notification* subresource associated with a bucket as described in [Enabling event notifications ](NotificationHowTo.md#how-to-enable-disable-notification-intro)\. You use the `Filter` XML structure to define the rules for notifications to be filtered by the prefix and/or suffix of an object key name\. For information about the details of the `Filter` XML structure, see [PUT Bucket notification](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketPUTnotification.html) in the *Amazon Simple Storage Service API Reference*\. 

Notification configurations that use `Filter` cannot define filtering rules with overlapping prefixes, overlapping suffixes, or prefix and suffix overlapping\. The following sections have examples of valid notification configurations with object key name filtering\. They also contain examples of notification configurations that are invalid because of prefix/suffix overlapping\. 

**Topics**
+ [Examples of valid notification configurations with object key name filtering](#notification-how-to-filtering-example-valid)
+ [Examples of notification configurations with invalid Prefix/Suffix overlapping](#notification-how-to-filtering-examples-invalid)

## Examples of valid notification configurations with object key name filtering<a name="notification-how-to-filtering-example-valid"></a>

The following notification configuration contains a queue configuration identifying an Amazon SQS queue for Amazon S3 to publish events to of the `s3:ObjectCreated:Put` type\. The events will be published whenever an object that has a prefix of `images/` and a `jpg` suffix is PUT to a bucket\. 

```
<NotificationConfiguration>
  <QueueConfiguration>
      <Id>1</Id>
      <Filter>
          <S3Key>
              <FilterRule>
                  <Name>prefix</Name>
                  <Value>images/</Value>
              </FilterRule>
              <FilterRule>
                  <Name>suffix</Name>
                  <Value>jpg</Value>
              </FilterRule>
          </S3Key>
     </Filter>
     <Queue>arn:aws:sqs:us-west-2:444455556666:s3notificationqueue</Queue>
     <Event>s3:ObjectCreated:Put</Event>
  </QueueConfiguration>
</NotificationConfiguration>
```

The following notification configuration has multiple non\-overlapping prefixes\. The configuration defines that notifications for PUT requests in the `images/` folder go to queue\-A, while notifications for PUT requests in the `logs/` folder go to queue\-B\.

```
<NotificationConfiguration>
  <QueueConfiguration>
     <Id>1</Id>
     <Filter>
            <S3Key>
                <FilterRule>
                    <Name>prefix</Name>
                    <Value>images/</Value>
                </FilterRule>
            </S3Key>
     </Filter>
     <Queue>arn:aws:sqs:us-west-2:444455556666:sqs-queue-A</Queue>
     <Event>s3:ObjectCreated:Put</Event>
  </QueueConfiguration>
  <QueueConfiguration>
     <Id>2</Id>
     <Filter>
            <S3Key>
                <FilterRule>
                    <Name>prefix</Name>
                    <Value>logs/</Value>
                </FilterRule>
            </S3Key>
     </Filter>
     <Queue>arn:aws:sqs:us-west-2:444455556666:sqs-queue-B</Queue>
     <Event>s3:ObjectCreated:Put</Event>
  </QueueConfiguration>
</NotificationConfiguration>
```

The following notification configuration has multiple non\-overlapping suffixes\. The configuration defines that all `.jpg` images newly added to the bucket are processed by Lambda cloud\-function\-A, and all newly added `.png` images are processed by cloud\-function\-B\. The `.png` and `.jpg` suffixes are not overlapping even though they have the same last letter\. Two suffixes are considered overlapping if a given string can end with both suffixes\. A string cannot end with both `.png` and `.jpg`, so the suffixes in the example configuration are not overlapping suffixes\. 

```
<NotificationConfiguration>
  <CloudFunctionConfiguration>
     <Id>1</Id>
     <Filter>
            <S3Key>
                <FilterRule>
                    <Name>suffix</Name>
                    <Value>.jpg</Value>
                </FilterRule>
            </S3Key>
     </Filter>
     <CloudFunction>arn:aws:lambda:us-west-2:444455556666:cloud-function-A</CloudFunction>
     <Event>s3:ObjectCreated:Put</Event>
  </CloudFunctionConfiguration>
  <CloudFunctionConfiguration>
     <Id>2</Id>
     <Filter>
            <S3Key>
                <FilterRule>
                    <Name>suffix</Name>
                    <Value>.png</Value>
                </FilterRule>
            </S3Key>
     </Filter>
     <CloudFunction>arn:aws:lambda:us-west-2:444455556666:cloud-function-B</CloudFunction>
     <Event>s3:ObjectCreated:Put</Event>
  </CloudFunctionConfiguration>
</NotificationConfiguration>
```

Your notification configurations that use `Filter` cannot define filtering rules with overlapping prefixes for the same event types, unless the overlapping prefixes are used with suffixes that do not overlap\. The following example configuration shows how objects created with a common prefix but non\-overlapping suffixes can be delivered to different destinations\.

```
<NotificationConfiguration>
  <CloudFunctionConfiguration>
     <Id>1</Id>
     <Filter>
            <S3Key>
                <FilterRule>
                    <Name>prefix</Name>
                    <Value>images</Value>
                </FilterRule>
                <FilterRule>
                    <Name>suffix</Name>
                    <Value>.jpg</Value>
                </FilterRule>
            </S3Key>
     </Filter>
     <CloudFunction>arn:aws:lambda:us-west-2:444455556666:cloud-function-A</CloudFunction>
     <Event>s3:ObjectCreated:Put</Event>
  </CloudFunctionConfiguration>
  <CloudFunctionConfiguration>
     <Id>2</Id>
     <Filter>
            <S3Key>
                <FilterRule>
                    <Name>prefix</Name>
                    <Value>images</Value>
                </FilterRule>
                <FilterRule>
                    <Name>suffix</Name>
                    <Value>.png</Value>
                </FilterRule>
            </S3Key>
     </Filter>
     <CloudFunction>arn:aws:lambda:us-west-2:444455556666:cloud-function-B</CloudFunction>
     <Event>s3:ObjectCreated:Put</Event>
  </CloudFunctionConfiguration>
</NotificationConfiguration>
```

## Examples of notification configurations with invalid Prefix/Suffix overlapping<a name="notification-how-to-filtering-examples-invalid"></a>

For the most part, your notification configurations that use `Filter` cannot define filtering rules with overlapping prefixes, overlapping suffixes, or overlapping combinations of prefixes and suffixes for the same event types\. \(You can have overlapping prefixes as long as the suffixes do not overlap\. For an example, see [Configuring notifications with object key name filtering](#notification-how-to-filtering)\.\)

You can use overlapping object key name filters with different event types\. For example, you could create a notification configuration that uses the prefix `image/` for the `ObjectCreated:Put` event type and the prefix `image/` for the `ObjectRemoved:*` event type\. 

You get an error if you try to save a notification configuration that has invalid overlapping name filters for the same event types when using the Amazon S3 console or API\. This section shows examples of notification configurations that are not valid because of overlapping name filters\. 

Any existing notification configuration rule is assumed to have a default prefix and suffix that match any other prefix and suffix respectively\. The following notification configuration is not valid because it has overlapping prefixes, where the root prefix overlaps with any other prefix\. \(The same thing would be true if you were using a suffix instead of a prefix in this example\. The root suffix overlaps with any other suffix\.\)

```
<NotificationConfiguration>
     <TopicConfiguration>
         <Topic>arn:aws:sns:us-west-2:444455556666:sns-notification-one</Topic>
         <Event>s3:ObjectCreated:*</Event>
    </TopicConfiguration>
    <TopicConfiguration>
         <Topic>arn:aws:sns:us-west-2:444455556666:sns-notification-two</Topic>
         <Event>s3:ObjectCreated:*</Event>
         <Filter>
             <S3Key>
                 <FilterRule>
                     <Name>prefix</Name>
                     <Value>images</Value>
                 </FilterRule>
            </S3Key>
        </Filter>
    </TopicConfiguration>             
</NotificationConfiguration>
```

The following notification configuration is not valid because it has overlapping suffixes\. Two suffixes are considered overlapping if a given string can end with both suffixes\. A string can end with `jpg` and `pg`, so the suffixes are overlapping\. \(The same is true for prefixes; two prefixes are considered overlapping if a given string can begin with both prefixes\.\)

```
 <NotificationConfiguration>
     <TopicConfiguration>
         <Topic>arn:aws:sns:us-west-2:444455556666:sns-topic-one</Topic>
         <Event>s3:ObjectCreated:*</Event>
         <Filter>
             <S3Key>
                 <FilterRule>
                     <Name>suffix</Name>
                     <Value>jpg</Value>
                 </FilterRule>
            </S3Key>
        </Filter>
    </TopicConfiguration>
    <TopicConfiguration>
         <Topic>arn:aws:sns:us-west-2:444455556666:sns-topic-two</Topic>
         <Event>s3:ObjectCreated:Put</Event>
         <Filter>
             <S3Key>
                 <FilterRule>
                     <Name>suffix</Name>
                     <Value>pg</Value>
                 </FilterRule>
            </S3Key>
        </Filter>
    </TopicConfiguration>
</NotificationConfiguration
```

The following notification configuration is not valid because it has overlapping prefixes and suffixes\. 

```
<NotificationConfiguration>
     <TopicConfiguration>
         <Topic>arn:aws:sns:us-west-2:444455556666:sns-topic-one</Topic>
         <Event>s3:ObjectCreated:*</Event>
         <Filter>
             <S3Key>
                 <FilterRule>
                     <Name>prefix</Name>
                     <Value>images</Value>
                 </FilterRule>
                 <FilterRule>
                     <Name>suffix</Name>
                     <Value>jpg</Value>
                 </FilterRule>
            </S3Key>
        </Filter>
    </TopicConfiguration>
    <TopicConfiguration>
         <Topic>arn:aws:sns:us-west-2:444455556666:sns-topic-two</Topic>
         <Event>s3:ObjectCreated:Put</Event>
         <Filter>
             <S3Key>
                 <FilterRule>
                     <Name>suffix</Name>
                     <Value>jpg</Value>
                 </FilterRule>
            </S3Key>
        </Filter>
    </TopicConfiguration>
</NotificationConfiguration>
```