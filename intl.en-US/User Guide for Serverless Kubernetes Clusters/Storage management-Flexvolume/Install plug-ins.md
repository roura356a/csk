# Install plug-ins

This topic describes how to use YAML templates to install volume plug-ins provided by Container Service for Kubernetes \(ACK\).

## Prerequisites

-   [Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md)
-   [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md)

## Precautions

-   To use FlexVolume, set the `--enable-controller-attach-detach` parameter to false for kubelet. By default, this parameter is set to false for ACK clusters.
-   Install the FlexVolume plug-in in the kube-system namespace.

## Install the disk controller

By default, the disk controller is installed in ACK clusters. If the disk controller is not installed in your cluster, you can manually install it. For more information, see [alicloud-disk-controller](https://github.com/AliyunContainerService/serverless-k8s-examples/blob/master/volumes/alicloud-disk-controller.yaml). The following YAML template provides an example on how to manually install the disk controller:

```
---
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: alicloud-disk-essd
provisioner: alicloud/disk
parameters:
  type: cloud_essd

---
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: alicloud-disk-efficiency
provisioner: alicloud/disk
parameters:
  type: cloud_efficiency

---
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: alicloud-disk-ssd
provisioner: alicloud/disk
parameters:
  type: cloud_ssd

---
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: alicloud-disk-available
provisioner: alicloud/disk
parameters:
  type: available

---
kind: Deployment
apiVersion: extensions/v1beta1
metadata:
  name: alicloud-disk-controller
  namespace: kube-system
spec:
  replicas: 1
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        app: alicloud-disk-controller
    spec:
      tolerations:
      - operator: Exists
      priorityClassName: system-node-critical
      nodeSelector:
         node-role.kubernetes.io/master: 
      serviceAccount: admin
      serviceAccountName: admin
      containers:
        - name: alicloud-disk-controller
          image: registry.cn-hangzhou.aliyuncs.com/acs/alicloud-disk-controller:v1.12.6.21-54d91d6-aliyun
          volumeMounts:
            - name: cloud-config
              mountPath: /etc/kubernetes/
            - name: logdir
              mountPath: /var/log/alicloud/
          resources:
            limits:
              cpu: 200m
              memory: 200Mi
            requests:
              cpu: 100m
              memory: 100Mi
          livenessProbe:
            exec:
              command:
              - sh
              - -c
              - ps -ef | grep /alicloud-disk-controller | grep -v grep
            failureThreshold: 8
            initialDelaySeconds: 15
            periodSeconds: 10
            successThreshold: 1
            timeoutSeconds: 15
      volumes:
        - name: cloud-config
          hostPath:
            path: /etc/kubernetes/
        - name: logdir
          hostPath:
            path: /var/log/alicloud/
```

## Verify the installation

You can connect to a master node of your ACK cluster to check whether the plug-in is installed. For more information about how to connect to a master node, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md).

Run the following command. If a list of pods that are in the Running state is returned, it indicates that the disk controller is installed.

```
kubectl get pod -n kube-system | grep alicloud-disk-controller
```

