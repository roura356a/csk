---
keyword: [kernel parameters of sandboxed pod, privileged container, annotation]
---

# Set kernel parameters for a sandboxed pod

Typically, the default kernel parameters provided by the containers in a pod can meet the requirements of most scenarios. However, in some scenarios, optimized kernel parameters may be required for better application performance. The Sandbox-Container runtime allows you to use annotations to set custom kernel parameters for a pod. You can also use the sysctl interface to set kernel parameters for containers that are in privileged mode. This topic describes how to set kernel parameters for a sandboxed pod.

-   [Create a managed Kubernetes cluster that runs sandboxed containers](/intl.en-US/User Guide for Kubernetes Clusters/Sandboxed-Container management/Create a security sandbox cluster/Create a managed Kubernetes cluster that runs sandboxed containers.md).
-   [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md).

In a Kubernetes cluster, you can configure a security context and annotations for a pod to define safe and unsafe namespaced sysctls. You can also start a privileged container and configure unnamespaced sysctls.

Pods that use the runC runtime share kernels with the hosts. In this case, the following issues may arise:

-   Privileged containers have almost full access permissions as the hosts. However, if you modify sysctls or unsafe sysctls, other pods may be affected or the nodes may become unavailable.
-   You cannot set unnamespaced kernel parameters for different pods that are deployed on the same node.

## Features of Sandbox-Container

A sandboxed pod has an exclusive kernel. You can use annotations to set custom kernel parameters for a pod or set containers to run in privileged mode. This addresses the issues caused by kernel sharing between hosts and pods that use the runC runtime.

-   You can use annotations to set valid sysctls including namespaced and unnamespaced sysctls for a pod. The sysctls apply to only the kernel used by the current pod. Other pods or host sysctls are not affected.
-   You can set a container to run in privileged mode. This grants the container almost full access permissions on the sandbox kernel. A sandbox has an exclusive kernel. The access permissions of the container are valid only within the sandbox. Other pods or host kernels are not affected.

## Use annotations to configure sysctls for a pod

You can use the `securecontainer.alibabacloud.com/sysctls` annotation to configure sysctls for a pod. You can set one or more sysctls. Separate multiple sysctls with commas \(,\).

For example, if you want to enable packet forwarding, use the following annotation to configure sysctls:

```
annotations:
  securecontainer.alibabacloud.com/sysctls: "net.bridge.bridge-nf-call-ip6tables=1,net.bridge.bridge-nf-call-iptables=1,net.ipv4.ip_forward=1"
```

The following file is an example of a complete YAML file:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: busybox
  name: busybox
  namespace: default
spec:
  replicas: 2
  selector:
    matchLabels:
      app: busybox
  template:
    metadata:
      labels:
        app: busybox
      annotations:
        securecontainer.alibabacloud.com/sysctls: "net.bridge.bridge-nf-call-ip6tables=1,net.bridge.bridge-nf-call-iptables=1,net.ipv4.ip_forward=1"
    spec:
      containers:
        - command:
            - sleep
            - infinity
          image: 'busybox:latest'
          imagePullPolicy: IfNotPresent
          name: busybox
          resources:
            limits:
              cpu: '1'
              memory: 1Gi
            requests:
              cpu: 500m
              memory: 512Mi
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      runtimeClassName: runv
```

## Configure a privileged container

You can use the same method to provision privileged containers in a sandboxed pod and a pod that uses the runC runtime. However, you must configure the kernel host for a pod that uses the runC runtime but configure the sandbox kernel for a sandboxed pod.

You can run the following command to configure a privileged container:

```
containers:
  - name: busybox
    securityContext:
      privileged: true
```

The following file is an example of a complete YAML file:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: busybox
  name: busybox
  namespace: default
spec:
  replicas: 2
  selector:
    matchLabels:
      app: busybox
  template:
    metadata:
      labels:
        app: busybox
    spec:
      containers:
        - command:
            - sh
            - -c 
            - "sysctl -w fs.inotify.max_user_watches=524286 && tail -f /dev/null"
          image: 'busybox:latest'
          imagePullPolicy: IfNotPresent
          name: busybox
          securityContext:
            privileged: true
          resources:
            limits:
              cpu: '1'
              memory: 1Gi
            requests:
              cpu: 500m
              memory: 512Mi
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      runtimeClassName: runv
```

[Create a managed Kubernetes cluster that runs sandboxed containers](/intl.en-US/User Guide for Kubernetes Clusters/Sandboxed-Container management/Create a security sandbox cluster/Create a managed Kubernetes cluster that runs sandboxed containers.md)

[Differences between runC and runV](/intl.en-US/User Guide for Kubernetes Clusters/Sandboxed-Container management/Differences between runC and runV.md)

[Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md)

