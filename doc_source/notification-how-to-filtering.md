# Configuring event notifications using object key name filtering<a name="notification-how-to-filtering"></a>

When configuring an Amazon S3 event notification, you must specify which supported Amazon S3 event types cause Amazon S3 to send the notification\. If an event type that you didn't specify occurs in your S3 bucket, Amazon S3 doesn't send the notification\.

You can configure notifications to be filtered by the prefix and suffix of the key name of objects\. For example, you can set up a configuration where you're sent a notification only when image files with a "`.jpg`" file name extension are added to a bucket\. Or, you can have a configuration that delivers a notification to an Amazon SNS topic when an object with the prefix "`images/`" is added to the bucket, while having notifications for objects with a "`logs/`" prefix in the same bucket delivered to an AWS Lambda function\. 

You can set up notification configurations that use object key name filtering in the Amazon S3 console\. You can do so by using Amazon S3 APIs through the AWS SDKs or the REST APIs directly\. For information about using the console UI to set a notification configuration on a bucket, see [Enabling and configuring event notifications using the Amazon S3 consoleEnabling Amazon EventBridge](enable-event-notifications.md)\. 

Amazon S3 stores the notification configuration as XML in the *notification* subresource associated with a bucket as described in [Using Amazon SQS, Amazon SNS, and Lambda](how-to-enable-disable-notification-intro.md)\. You use the `Filter` XML structure to define the rules for notifications to be filtered by the prefix or suffix of an object key name\. For information about the `Filter` XML structure, see [PUT Bucket notification](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketPUTnotification.html) in the *Amazon Simple Storage Service API Reference*\. 

If an Amazon S3 event notification is configured to use object key name filtering, notifications are only published for objects with a certain key name prefix or suffix\. A wild\-card character \("\*"\) can't be used in filters as a prefix or suffix to represent any character\. If your prefix or suffix contains a space, you must replace it with the "\+" character\. If you use any other special characters in the value of the prefix or suffix, you must enter them in [URL\-encoded \(percent\-encoded\) format](https://en.wikipedia.org/wiki/Percent-encoding)\. For more information, see [Object key naming guidelines](object-keys.md#object-key-guidelines)\.

Notification configurations that use `Filter` cannot define filtering rules with overlapping prefixes, overlapping suffixes, or prefix and suffix overlapping\. The following sections have examples of valid notification configurations with object key name filtering\. They also contain examples of notification configurations that are not valid because of prefix and suffix overlapping\. 

**Topics**
+ [Examples of valid notification configurations with object key name filtering](#notification-how-to-filtering-example-valid)
+ [Examples of notification configurations with invalid prefix and suffix overlapping](#notification-how-to-filtering-examples-invalid)

## Examples of valid notification configurations with object key name filtering<a name="notification-how-to-filtering-example-valid"></a>

The following notification configuration contains a queue configuration identifying an Amazon SQS queue for Amazon S3 to publish events to of the `s3:ObjectCreated:Put` type\. The events are published whenever an object that has a prefix of `images/` and a `jpg` suffix is PUT to a bucket\. 

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

The following notification configuration has multiple non\-overlapping suffixes\. The configuration defines that all `.jpg` images newly added to the bucket are processed by Lambda cloud\-function\-A, and all newly added `.png` images are processed by cloud\-function\-B\. The `.png` and `.jpg` suffixes aren't overlapping even though they have the same last letter\. If a given string can end with both suffixes, the two suffixes are considered overlapping\. A string can't end with both `.png` and `.jpg`, so the suffixes in the example configuration aren't overlapping suffixes\. 

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

Your notification configurations that use `Filter` can't define filtering rules with overlapping prefixes for the same event types\. They can only do so, if the overlapping prefixes that are used with suffixes that don't overlap\. The following example configuration shows how objects created with a common prefix but non\-overlapping suffixes can be delivered to different destinations\.

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

## Examples of notification configurations with invalid prefix and suffix overlapping<a name="notification-how-to-filtering-examples-invalid"></a>

For the most part, your notification configurations that use `Filter` can't define filtering rules with overlapping prefixes, overlapping suffixes, or overlapping combinations of prefixes and suffixes for the same event types\. You can have overlapping prefixes as long as the suffixes don't overlap\. For an example, see [Configuring event notifications using object key name filtering](#notification-how-to-filtering)\.

You can use overlapping object key name filters with different event types\. For example, you can create a notification configuration that uses the prefix `image/` for the `ObjectCreated:Put` event type and the prefix `image/` for the `ObjectRemoved:*` event type\. 

You get an error if you try to save a notification configuration that has invalid overlapping name filters for the same event types when using the Amazon S3 console or API\. This section shows examples of notification configurations that aren't valid because of overlapping name filters\. 

Any existing notification configuration rule is assumed to have a default prefix and suffix that match any other prefix and suffix, respectively\. The following notification configuration isn't valid because it has overlapping prefixes\. Specifically, the root prefix overlaps with any other prefix\. The same thing is true if you use a suffix instead of a prefix in this example\. The root suffix overlaps with any other suffix\.

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

The following notification configuration isn't valid because it has overlapping suffixes\. If a given string can end with both suffixes, the two suffixes are considered overlapping\. A string can end with `jpg` and `pg`\. So, the suffixes overlap\. The same is true for prefixes\. If a given string can begin with both prefixes, the two prefixes are considered overlapping\.

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

The following notification configuration isn't valid because it has overlapping prefixes and suffixes\. 

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