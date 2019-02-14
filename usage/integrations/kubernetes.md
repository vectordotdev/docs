---
description: Send Kubernetes logs to Timber
---

# Kubernetes

Timber integrates with Kubernetes through the Fluent Bit log forwarder.

## Installation

To forward logs from your kubernetes cluster, we recommend deploying fluentbit as a daemonset to forward all container logs with Kubernetes annotations such as pod, namespace, and host.

1. To get started run the following commands to create the namespace, service account and role setup:  


   ```bash
   kubectl create namespace logging
   kubectl create -f https://raw.githubusercontent.com/fluent/fluent-bit-kubernetes-logging/master/fluent-bit-service-account.yaml
   kubectl create -f https://raw.githubusercontent.com/fluent/fluent-bit-kubernetes-logging/master/fluent-bit-role.yaml
   kubectl create -f https://raw.githubusercontent.com/fluent/fluent-bit-kubernetes-logging/master/fluent-bit-role-binding.yaml
   ```

2. Then create the config map:  


   ```bash
   kubectl create -f <the attached fluent-bit-configmap.yaml>
   ```

3. Finally, deploy the daemonset to start forwarding logs:



## Configuration

See the [Fluent Bit configuration docs](https://docs.fluentbit.io/manual/configuration/file) for additional customizations you can make in Fluent Bit to capture more metadata, parse lines into structured messages, and filter the logs you forward.

