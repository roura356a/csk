# Downward API

Standard Kubernetes clusters support the following ways of exposing pod and container fields to a running container:

-   Environment variable
-   DownwardAPIVolumeFile

These two ways of exposing pod and container fields are together called the [Downward API](https://kubernetes.io/zh/docs/tasks/inject-data-application/downward-api-volume-expose-pod-information/).

Currently, when Elastic Container Instance \(ECI\) is used with Alibaba Cloud Container Service for Kubernetes and Alibaba Cloud Serverless Kubernetes, ECI supports most of the common fields that can be exposed through the Downward API. This topic describes how to use the Downward API in an ECI.

## Environment variable

You can use the `fieldRef` field to assign the values of the following ECI fields to environment variables: `metadata.name`, `metadata.namespace`, `metadata.uid`, `metadata.labels['<KEY>']`, and `metadata.annotations['<KEY>']`. These fields respectively indicate the ECI name, ECI namespace, ECI UID, key of a single label \(for example, `mylabel`\), and key of a single annotation.````

You can also use the fieldRef field to assign the values of the following spec and status fields to environment variables:

spec.serviceAccountName

spec.nodeName

status.podIP

The following configuration file of a sample deployment shows how to use the fieldRef field to assign the values of ECI fields to environment variables:

```
apiVersion: apps/v1beta2 # For versions earlier than 1.8.0, use apps/v1beta1.
kind: Deployment
metadata:
  name: vk-downward-env
  labels:
    app: nginx
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
        annotations: 
            regionId: cn-beijing
            platform: Aliyun ECI
        labels:
            app: nginx
            env: test
    spec:
      containers:
      - name: nginx
        image: nginx
        env:
        - name: MY_metadata.name
          valueFrom:
            fieldRef:
              fieldPath: metadata.name
        - name: MY_metadata.namespace
          valueFrom:
            fieldRef:
              fieldPath: metadata.namespace
        - name: MY_metadata.uid
          valueFrom:
            fieldRef:
              fieldPath: metadata.uid
        - name: MY_metadata.labels
          valueFrom:
            fieldRef:
              fieldPath: metadata.labels['env']
        - name: MY_metadata.annotations
          valueFrom:
            fieldRef:
              fieldPath: metadata.annotations['regionId']
        - name: MY_status.podIP
          valueFrom:
            fieldRef:
              fieldPath: status.podIP
        - name: MY_spec.serviceAccountName
          valueFrom:
            fieldRef:
              fieldPath: spec.serviceAccountName
        - name: MY_spec.nodeName
          valueFrom:
            fieldRef:
              fieldPath: spec.nodeName
      nodeName: virtual-node-eci-0
```

Log on to the container and check the values of environment variables. You can find that all the environment variables are assigned the values specified by the fieldRef field.

```
root@default-vk-downward-env:/# env
MY_spec.nodeName=virtual-kubelet
MY_spec.serviceAccountName=default
MY_metadata.annotations=cn-beijing
MY_metadata.namespace=default
MY_metadata.uid=f4881309-f3dd-11e9-bcf9-9efaf54dcfa7
MY_metadata.name=vk-downward-env
MY_metadata.labels=test
MY_status.podIP=192.168.6.245
KUBERNETES_SERVICE_PORT_HTTPS=443
KUBERNETES_SERVICE_PORT=6443
PWD=/
PKG_RELEASE=1~buster
HOME=/root
KUBERNETES_PORT_443_TCP=tcp://172.22.128.1:443
NJS_VERSION=0.3.5
TERM=xterm
SHLVL=1
KUBERNETES_PORT_443_TCP_PROTO=tcp
KUBERNETES_PORT_443_TCP_ADDR=172.22.128.1
KUBERNETES_SERVICE_HOST=192.168.6.120
KUBERNETES_PORT=tcp://172.22.128.1:443
KUBERNETES_PORT_443_TCP_PORT=443
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
NGINX_VERSION=1.17.4
_=/usr/bin/env
```

****

## DownwardAPIVolumeFile

You can use the `fieldRef` field to pass the values of the following ECI fields to DownwardAPIVolumeFiles: `metadata.name`, `metadata.namespace`, `metadata.uid`, `metadata.labels['<KEY>']`, and `metadata.annotations['<KEY>']`. These fields respectively indicate the ECI name, ECI namespace, ECI UID, key of a single label \(for example, `mylabel`\), and key of a single annotation. The fields are the same as those whose values can be assigned to environment variables. The method of passing the values of these fields to DownwardAPIVolumeFiles is the same as that for assigning the values to environment variables.

In addition, a DownwardAPIVolumeFile can obtain all the labels or annotations of an ECI through the fieldRef field.

metadata.labels: all the labels of the ECI. Each label is stored in the format of `label-key="label-value"` and occupies one row.

metadata.annotations: all the annotations of the ECI. Each annotation is stored in the format of `annotation-key="annotation-value"` and occupies one row.

The following configuration file of a sample deployment shows how to use the fieldRef field to assign the values of ECI fields to DownwardAPIVolumeFiles:

```
apiVersion: apps/v1beta2 # For versions earlier than 1.8.0, use apps/v1beta1.
kind: Deployment
metadata:
  name: vk-downward-down-volume
  labels:
    app: nginx
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
        annotations: 
            regionId: cn-beijing
            platform: Aliyun ECI
        labels:
            app: nginx
            env: test
    spec:
      containers:
      - name: nginx
        image: nginx
        volumeMounts:
        - name: podinfo
          mountPath: /etc/podinfo
          readOnly: false
      volumes:
      - name: podinfo
        downwardAPI:
          items:
            - path: "metadata.name"
              fieldRef:
                fieldPath: metadata.name
            - path: "metadata.namespace"
              fieldRef:
                fieldPath: metadata.namespace
            - path: "metadata.uid"
              fieldRef:
                fieldPath: metadata.uid
            - path: "metadata.labels"
              fieldRef:
                fieldPath: metadata.labels
            - path: "metadata.annotations"
              fieldRef:
                fieldPath: metadata.annotations
      nodeName: virtual-node-eci-0
```

Log on to the container and go to the directory to which the Downward API volume is mounted. You can find that all the specified DownwardAPIVolumeFiles are generated in the directory and they store the values specified by the fieldRef field.

```
Welcome to Alibaba Cloud Elastic Container Instance!
root@default-vk-downward-down-volume:/# cd /etc/podinfo/
root@default-vk-downward-down-volume:/etc/podinfo# ls
metadata.annotations  metadata.labels  metadata.name  metadata.namespace  metadata.uid
root@default-vk-downward-down-volume:/etc/podinfo# cat metadata.namespace 
default
root@default-vk-downward-down-volume:/etc/podinfo# cat metadata.name
vk-downward-down-volume
root@default-vk-downward-down-volume:/etc/podinfo# cat metadata.uid 
fa50b2b2-f3e3-11e9-bcf9-9efaf54dcfa7
root@default-vk-downward-down-volume:/etc/podinfo# cat metadata.annotations 
platform="Aliyun ECI"
regionId="cn-beijing"
root@default-vk-downward-down-volume:/etc/podinfo# cat metadata.labels 
app="nginx"
env="test"
root@default-vk-downward-down-volume:/etc/podinfo# 
```

Currently, you can only pass the values of ECI fields to DownwardAPIVolumeFiles. You cannot pass the values of container fields to DownwardAPIVolumeFiles. For example, the values of the following container fields cannot be passed to DownwardAPIVolumeFiles:

```
limits.cpu
requests.cpu
limits.memory
requests.memory
limits.ephemeral-storage
requests.ephemeral-storage
```

