# Install plug-ins

This topic describes how to use YAML templates to install volume plug-ins provided by Container Service for Kubernetes \(ACK\).

**Note:** If your ACK cluster was created before February 6, 2018, you must install the volume plug-ins before you can use volumes. If your ACK cluster was created after February 6, 2018, you can directly use volumes. You do not need to install the volume plug-ins.

## Prerequisites

-   [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)
-   [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md)

## Limits

Only the CentOS 7 operating system is supported.

## Precautions

-   To use FlexVolume, set the `--enable-controller-attach-detach` parameter to false for kubelet. By default, this parameter is set to false for ACK clusters.
-   Install the FlexVolume plug-in in the kube-system namespace.

## Verify the installation

On the master node:

-   Run the following command to generate several lists of pods that are in the Running state. The number of lists is determined by the number of nodes.

    ```
    kubectl get pod -n kube-system | grep flexvolume
    ```

-   Run the following command to generate a list of pods that are in the Running state:

    ```
    kubectl get pod -n kube-system | grep alicloud-disk-controller
    ```


## Install FlexVolume

By default, FlexVolume is installed in ACK clusters.

If FlexVolume is not installed in your cluster, you can install it by using the following YAML template:

```
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: flexvolume
  namespace: kube-system
  labels:
    k8s-volume: flexvolume
spec:
  selector:
    matchLabels:
      name: acs-flexvolume
  template:
    metadata:
      labels:
        name: acs-flexvolume
    spec:
      hostPID: true
      hostNetwork: true
      tolerations:
      - operator: Exists
      priorityClassName: system-node-critical
      containers:
      - name: acs-flexvolume
        image: registry.cn-hangzhou.aliyuncs.com/acs/flexvolume:v1.14.8.88-00a6585-aliyun
        imagePullPolicy: Always
        securityContext:
          privileged: true
        env:
        - name: ACS_DISK
          value: "true"
        - name: ACS_NAS
          value: "true"
        - name: ACS_OSS
          value: "true"
        livenessProbe:
          exec:
            command:
            - sh
            - -c
            - ls /acs/flexvolume
          failureThreshold: 8
          initialDelaySeconds: 15
          periodSeconds: 60
          successThreshold: 1
          timeoutSeconds: 15
        volumeMounts:
        - name: usrdir
          mountPath: /host/usr/
        - name: etcdir
          mountPath: /host/etc/
        - name: logdir
          mountPath: /var/log/alicloud/
        - mountPath: /var/lib/kubelet
          mountPropagation: Bidirectional
          name: kubeletdir
      volumes:
      - name: usrdir
        hostPath:
          path: /usr/
      - name: etcdir
        hostPath:
          path: /etc/
      - name: logdir
        hostPath:
          path: /var/log/alicloud/
      - hostPath:
          path: /var/lib/kubelet
          type: Directory
        name: kubeletdir
  updateStrategy:
    rollingUpdate:
      maxUnavailable: 10%
    type: RollingUpdate
```

## Install the disk controller

By default, the disk controller is installed in ACK clusters.

If FlexVolume is not installed in your cluster, you can install it by using the following YAML template:

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

## Install the NAS controller

By default, the NAS controller is not installed. You can install it by using the following YAML template:

```
kind: Deployment
apiVersion: extensions/v1beta1
metadata:
  name: alicloud-nas-controller
  namespace: kube-system
spec:
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        app: alicloud-nas-controller
    spec:
      tolerations:
      - operator: Exists
      affinity:
        nodeAffinity:
          preferredDuringSchedulingIgnoredDuringExecution:
          - weight: 1
            preference:
              matchExpressions:
              - key: node-role.kubernetes.io/master
                operator: Exists
      priorityClassName: system-node-critical
      serviceAccount: admin
      hostNetwork: true
      containers:
        - name: nfs-provisioner
          image: registry.cn-hangzhou.aliyuncs.com/acs/alicloud-nas-controller:v1.14.3.8-58bf821-aliyun
          env:
          - name: PROVISIONER_NAME
            value: alicloud/nas
          securityContext:
            privileged: true
          volumeMounts:
          - mountPath: /var/log
            name: log
      volumes:
      - hostPath:
          path: /var/log
        name: log
```

