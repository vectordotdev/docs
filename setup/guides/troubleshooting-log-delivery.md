---
description: Tips on troubleshooting log delivery
---

# Troubleshooting Log Delivery

If you're having trouble viewing logs within your Timber account it's most likely due to a log delivery issue. This guide will walk through common problems and how to resolve them.

## 1. Check The Timber Status Page

To rule out an issue with the Timber service, please see the [Timber status page](https://status.timber.io) to ensure that there are not any known issues.

## 2. Test The Pipes

To rule out log delivery issues we can "test the pipes" to Timber with a simple `curl` command. This proves that log delivery is possible to your account. You can test this from your local machine but you should do this on an actual host that is sending logs.

{% hint style="info" %}
Please run this command on your actual host, this ensures that the machine sending logs can properly communicate with Timber.
{% endhint %}

```bash
curl \
    --user '{{your-timber-api-key}}' \
    --request POST \
    --header "Content-Type: text/plain" \
    --data 'debug: Testing the pipes' \
    --verbose \
    https://logs.timber.io/frames
```

You should see the following response:

```text
...TLS info...

< HTTP/1.1 202 Accepted
< server: Cowboy
< date: Thu, 21 Feb 2019 20:47:39 GMT
< content-length: 13
< cache-control: max-age=0, private, must-revalidate
< x-request-id: 2m2no48ii79n4hq984h17d52
< content-type: text/plain; charset=utf-8
< Connection: Keep-Alive
< Keep-Alive: timeout=75000 max=500
< 
* Connection #0 to host logs.timber.io left intact
Accepted logs
```

If you do not receive a `202` response then there is an issue with your account or the Timber service as a whole. You can refer to the [response codes documentation](../sources/http-api/#expected-response-codes) for explanation on each code. Although, Timber should respond with a body that describes the error.

## 3. Enable Debug Logs

Head back to your [integration's page](../sources/), at the bottom of every page is a "Troubleshooting" section that walks through enabling debug logs for that specific integration \(if possible\).

## 4. Contact Support

If nothing shows up during this process, or you found an issue, please contact support.





