---
description: Send AWS Lambda logs to Timber
---

# AWS Lambda

Timber integrates with [AWS Lambda](https://aws.amazon.com/lambda/) through the [AWS CloudWatch Logs subscriptions feature](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/Subscriptions.html), making it simple to forward your Lambda log to Timber while still capturing useful context and platform events.

## Installation

By default, your AWS Lambda function will create a dedicated CloudWatch Logs stream. You can simply forward this stream to Timber by following the [AWS CloudWatch Logs instructions](aws-cloudwatch/#installation). Be sure to select your AWS Lambda function's stream during the process:

{% page-ref page="aws-cloudwatch/" %}

## Automatic Events

AWS Lambda logs platform events that contain useful information about the execution of your Lambda function. Timber automatically parses these events making them easier to search and graph.

### Request Start

The request start event is emitted immediately before a Lambda invocation.

{% tabs %}
{% tab title="Before" %}
```text
START RequestId: 84f70cff-ab16-4f17-a729-91cc92530be4 Version: $LATEST
```
{% endtab %}

{% tab title="After" %}
```javascript
{
    "type": "END",
    "request_id": "84f70cff-ab16-4f17-a729-91cc92530be4"
}
```
{% endtab %}
{% endtabs %}

### Request End

The request end event is emitted immediately when you lambda invocation ends.

{% tabs %}
{% tab title="Before" %}
```
END RequestId: 84f70cff-ab16-4f17-a729-91cc92530be4
```
{% endtab %}

{% tab title="After" %}
```javascript
{
    "type": "START",
    "request_id": "84f70cff-ab16-4f17-a729-91cc92530be4",
    "version": "$LATEST"
}
```
{% endtab %}
{% endtabs %}

### Report

The report event is emitted immediately after a Lambda invocation, it contains useful summary information.

{% tabs %}
{% tab title="Before" %}
```text
REPORT RequestId: 84f70cff-ab16-4f17-a729-91cc92530be4	Duration: 10028.34 ms	Billed Duration: 10100 ms Memory Size: 1280 MB	Max Memory Used: 173 MB
```
{% endtab %}

{% tab title="After" %}
```javascript
{
    "type": "REPORT",
    "request_id": "84f70cff-ab16-4f17-a729-91cc92530be4",
    "duration_ms": 10028.34,
    "billed_duration_ms": 10100,
    "memory_size_mb": 1280,
    "max_memory_used_mb": 173
}
```
{% endtab %}
{% endtabs %}

## Troubleshooting

Because the AWS Lambda integration relies on CloudWatch Logs we recommend viewing the CloudWatch logs troubleshooting guide:

{% page-ref page="aws-cloudwatch/" %}

