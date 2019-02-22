---
description: Send AWS CloudWatch Logs to Timber
---

# AWS CloudWatch Logs

Timber integrates with [AWS CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/WhatIsCloudWatchLogs.html) through its [lambda subscription feature](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/Subscriptions.html), making it easy to forward CloudWatch logs to Timber while capturing useful context along the way.

## Installation

{% tabs %}
{% tab title="Manual" %}
1. First, deploy Timber's Lambda function to forward your logs by following these instructions: 
   1. Navigate to the [`timber-logging` lambda function deploy page](%20https://console.aws.amazon.com/lambda/home#/create/app?applicationId=arn:aws:serverlessrepo:us-east-1:754402436383:applications/timber-logging).
   2. In the "Application Settings" panel enter your Timber API key.
   3. Click the "Deploy" button. 
2. Next, select the CloudWatch log group that you'd like to forward by following these instructions: 
   1. Navigate to your [CloudWatch log groups page](%20https://console.aws.amazon.com/cloudwatch/home#logs:).
   2. Select the desired CloudWatch log group with the radio button.
   3. Click the "Actions" menu at the top and select "Stream to AWS Lambda"
   4. For the "Lambda Function" field, select the Timber Lambda function that you previously deployed. Click "Next".
   5. For the "Log Format" field, select "other" and leave the rest of the fields blank. Click "Next".
   6. Review the details and click "Start Streaming". 
3. Repeat this process for any other CloudWatch log groups you wish to forward.
{% endtab %}
{% endtabs %}

## Automatic Context

The [`timber-logging` Lambda function](%20https://serverlessrepo.aws.amazon.com/applications/arn:aws:serverlessrepo:us-east-1:754402436383:applications~timber-logging) automatically adds context to your logs, helping you to search and use your logs. The following is merged into every log line shipped to Timber:

```javascript
{
    "context": {
        "cloudwatch": {
            "log_group": "string",
            "log_stream": "string",
            "owner": "string"
        }
    }
}
```

| Field | Description |
| :--- | :--- |
| `context.cloudwatch.log_group` | The log group name of the originating data. |
| `context.cloudwatch.log_stream` | The log stream name of the originating log data. |
| `context.cloudwatch.owner` | The AWS account ID of the originating log data. |

## Troubleshooting

To begin, please see our [log delivery troubleshooting guide](../../guides/troubleshooting-log-delivery.md). This covers the most common issues we see with log delivery:

{% page-ref page="../../guides/troubleshooting-log-delivery.md" %}

If the above troubleshooting guide does not resolve your issue we recommend enabling debug logging on the Timber Lambda forwarder function. You can do this by setting the `TIMBER_DEBUG` environment variable to `true` . The the Timber logging function will begin emitting debug logs which you can analyze in the AWS CloudWatch Logs console.

