# Provision OSS buckets as static PVs by using YAML files

This topic describes how to provision Object Storage Service \(OSS\) buckets as static persistent volumes \(PVs\) by using YAML files.

[Create buckets](/intl.en-US/Quick Start/OSS console/Create buckets.md)

**Note:** If a node and an OSS bucket are deployed in the same region, you can use the internal endpoint of the OSS bucket to mount the bucket to pods on the node.

## Use a Secret to specify your AccessKey pair in a static PV

1.  Create a Secret.

    The following YAML file is an example of how to specify your AccessKey pair in a Secret:

    ```
    apiVersion: v1
    kind: Secret
    metadata:
      name: oss-secret
      namespace: default
    stringData:
      akId: ***
      akSecret: ***
    ```

    **Note:** The Secret must be created in the same namespace where the application is deployed.

    Replace the values of `akId` and `akSecret` with your AccessKey ID and AccessKey secret.

2.  Create a static PV.

    The following YAML file is an example of how to create a PV and a persistent volume claim \(PVC\):

    ```
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: oss-pvc
    spec:
      accessModes:
      - ReadWriteMany
      resources:
        requests:
          storage: 5Gi
    ---
    apiVersion: v1
    kind: PersistentVolume
    metadata:
      name: oss-csi-pv
    spec:
      capacity:
        storage: 5Gi
      accessModes:
        - ReadWriteMany
      persistentVolumeReclaimPolicy: Retain
      csi:
        driver: ossplugin.csi.alibabacloud.com
        volumeHandle: oss-csi-pv // The specified value must be the same as the name of the PV.
        nodePublishSecretRef:
          name: oss-secret
          namespace: default
        volumeAttributes:
          bucket: "oss"
          url: "oss-cn-hangzhou.aliyuncs.com"
          otherOpts: "-o max_stat_cache_size=0 -o allow_other"
    ```

    |Parameter|Description|
    |---------|-----------|
    |`nodePublishSecretRef`|Specifies that a Secret is used to obtain the AccessKey pair when an OSS bucket is mounted as a PV.|
    |`volumeHandle`|Specifies the name of the PV.|
    |`bucket`|Specifies the OSS bucket to be mounted.|
    |`url`|Specifies the endpoint of the OSS bucket. If the OSS bucket and the node to which the OSS bucket is mounted are deployed in the same region, you can use the internal endpoint of the OSS bucket.|
    |`otherOpts`|You can enter custom parameters in the format of `-o *** -o ***`.|


## Specify your AccessKey pair in a static PV

[Use a Secret to specify your AccessKey pair in a static PV](#section_obo_kuo_v5v) describes how to use a Secret to provide your AccessKey information to the CSI plug-in. This section describes how to specify your AccessKey pair in a static PV. The following YAML template is an example:

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: oss-csi-pv
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  csi:
    driver: ossplugin.csi.alibabacloud.com
    volumeHandle: oss-csi-pv // The specified value must be the same as the name of the PV.
    volumeAttributes:
      bucket: "oss"
      url: "oss-cn-hangzhou.aliyuncs.com"
      otherOpts: "-o max_stat_cache_size=0 -o allow_other"
      akId: "***"
      akSecret: "***"
```

## Configure security token-based authentication in a static PV

[Use a Secret to specify your AccessKey pair in a static PV](#section_obo_kuo_v5v) and [Specify your AccessKey pair in a static PV](#section_de7_06v_8wy) describe how to use a Secret or directly specify an AccessKey pair for access authentication. In addition, you can configure security token-based authentication in a PV. The following YAML template is an example:

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: oss-csi-pv
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  csi:
    driver: ossplugin.csi.alibabacloud.com
    volumeHandle: oss-csi-pv // The specified value must be the same as the name of the PV.
    volumeAttributes:
      bucket: "oss"
      url: "oss-cn-hangzhou.aliyuncs.com"
      otherOpts: "-o max_stat_cache_size=0 -o allow_other"
      authType: "sts"
```

## Create an application

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80
        volumeMounts:
          - name: oss-pvc
            mountPath: "/data"
      volumes:
        - name: oss-pvc
          persistentVolumeClaim:
            claimName: oss-pvc
```

You can also provision an OSS bucket as a staic PV in the console. For more information, see [Use an OSS bucket in the ACK console](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/OSS volumes/Use an OSS bucket in the ACK console.md).

