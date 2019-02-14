---
description: Send AWS Lambda logs to Timber
---

# AWS Lambda

Timber integrates with [AWS Lambda](https://aws.amazon.com/lambda/) through the [AWS CloudWatch Logs subscriptions feature](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/Subscriptions.html), making it simple to forward your Lambda log to Timber while capturing useful context and Lambda platform events.

## Installation

By default, your AWS Lambda function will create a dedicated CloudWatch Logs stream. You can simply forward this stream to Timber by following the [AWS CloudWatch Logs instructions](aws-cloudwatch/#installation), selecting your AWS Lambda function's stream during the process:

{% page-ref page="aws-cloudwatch/" %}

## Events

AWS Lambda automatically logs platform events that contain useful information about the execution of your Lambda function. The only way to obtain these events is through CloudWatch Logs, which the above installation instructions use CloudWatch.

### Request Start

The request start event is emitted immediately before a Lambda invocation.

```text
START RequestId: 84f70cff-ab16-4f17-a729-91cc92530be4 Version: $LATEST
```

Is parsed into:

```javascript
{
    "type": "END",
    "request_id": "84f70cff-ab16-4f17-a729-91cc92530be4"
}
```

### Request End

The request end event is emitted immediate after a Lambda invocation.

```
END RequestId: 84f70cff-ab16-4f17-a729-91cc92530be4
```

Is parsed into:

```javascript
{
    "type": "START",
    "request_id": "84f70cff-ab16-4f17-a729-91cc92530be4",
    "version": "$LATEST"
}
```

### Report

The report event is emitted immediately after a Lambda invocation, it contains useful summary information.

```text
REPORT RequestId: 84f70cff-ab16-4f17-a729-91cc92530be4	Duration: 10028.34 ms	Billed Duration: 10100 ms Memory Size: 1280 MB	Max Memory Used: 173 MB
```

Is parsed into:

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

