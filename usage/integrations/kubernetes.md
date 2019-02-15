---
description: Send Kubernetes logs to Timber
---

# Kubernetes

Timber integrates with [Kubernetes](https://kubernetes.io/) through the [Fluent Bit](fluent-bit.md) log forwarder. Fluent is the [recommended](https://kubernetes.io/docs/concepts/cluster-administration/logging/) log forwarder for Kubernetes since it's light weight, fast, and adds useful Kubernetes context to your logs. Our guide follows this best practice recommendation.

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

## Context

Every line emitted from your Kubernetes cluster will contain the following additional context fields. Helping you to segment and search your logs.

```javascript
{
    "stream": "stderr",
    "kubernetes": {
        "host": "minikube",
        "pod_name": "fluent-bit-nxznt",
        "pod_id": "821c15b8-1b49-11e9-a712-0800275bd70a"
        "namespace_name": "logging",
        "docker_id": "29f2394e1fac9aedb1942fcded230e3f566829e4c158b97713744b2826a4691d",
        "container_name": "name",
        "container_id": "id",
        "labels": {
            "version": "v1",
            "pod-template-generation": 1,
            "kubernetes.io/cluster-service": true,
            "k8s-app": "fluent-bit-logging",
            "controller-revision-hash": "795db8657b"
        }
    }
}
```

| Fields | Description |
| :--- | :--- |
| `stream` | The stream the logs were emitted from, usually `stdout.` |
| `kubernetes.host` | The host the logs originated from. |
| `kubernetes.pod_name` | The name of the pod the logs originated from. |
| `kubernetes.pod_id` | The ID of the pod the logs originated from. |
| `kubernetes.namespace_name` | The namespace the logs originated from. |
| `kubernetes.docker_id` | The ID of the docker machine the logs originated from. |
| `kubernetes.container_name` | The name of the container the logs originated from. |
| `kubernetes.container_id` | The ID of the container the logs originated from. |
| `labels` | The labels, in object form, assigned to your k8s cluster. |

