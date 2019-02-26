---
description: Send AWS CloudWatch Logs to Timber
---

# AWS CloudWatch Logs

Timber integrates with [AWS CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/WhatIsCloudWatchLogs.html) through its [lambda subscription feature](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/Subscriptions.html), making it easy to forward CloudWatch logs to Timber while capturing useful context along the way.

## Installation

{% tabs %}
{% tab title="AWS Console" %}
1. First, deploy Timber's Lambda function to forward your logs by following these instructions: 
   1. Navigate to the [`timber-logging` lambda function deploy page](%20https://console.aws.amazon.com/lambda/home#/create/app?applicationId=arn:aws:serverlessrepo:us-east-1:754402436383:applications/timber-logging).
   2. In the "Application Settings" panel enter your Timber API key and Timber source ID.
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

{% tab title="Terraform" %}
If you're using [Terraform](https://www.terraform.io/), Timber provides a [Terraform module](https://github.com/timberio/timber-cloudwatch-logs-lambda-function/tree/master/terraform) that make provisioning all of the AWS resources easy:

1. Define the Terraform module in your project:  


   ```coffeescript
   module "timber_log_forwarding" {
     source = "git::git@github.com:timberio/timber-cloudwatch-logs-lambda-function.git//terraform"
     enable_logging = "false"
  
     log_group_names = [
       "aws/lambda/hello_world"
     ]

     # Obtain both of these at https://app.timber.io
     api_key = "YOUR_API_KEY"
     source_id = "YOUR_SOURCE_ID"
   }
   ```

2. Run `terraform init`

### Multiple Sources

Many of our customers like to define individual Timber sources for each lambda function. If you're one of these customers simply define the Terraform module multiple times with the appropriate `source_id` for each.
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

## FAQs

### Is it possible to automatically forward all CloudWatch logs to Timber?

Unfortunately, AWS requires an individual [log subscription](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/Subscriptions.html) for each CloudWatch Logs group. Fortunately, infrastructure management tools like [Terraform](https://www.terraform.io/docs/providers/aws/r/cloudwatch_log_subscription_filter.html) make this process much easier, and we highly recommending adopting one if possible.

### How do I filter log messages sent to Timber?

AWS provide [log subscription filters](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/SubscriptionFilters.html) that make this process easy.

## Troubleshooting

To begin, please see our [log delivery troubleshooting guide](../../guides/troubleshooting-log-delivery.md). This covers the most common issues we see with log delivery:

{% page-ref page="../../guides/troubleshooting-log-delivery.md" %}

If the above troubleshooting guide does not resolve your issue we recommend enabling debug logging on the Timber Lambda forwarder function. You can do this by setting the `TIMBER_DEBUG` environment variable to `true` . The the Timber logging function will begin emitting debug logs which you can analyze in the AWS CloudWatch Logs console.

