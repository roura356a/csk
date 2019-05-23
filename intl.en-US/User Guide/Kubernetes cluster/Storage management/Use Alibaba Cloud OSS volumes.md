# Use Alibaba Cloud OSS volumes {#concept_h11_5bt_vdb .concept}

You can use Alibaba Cloud OSS volumes in a Kubernetes cluster of Alibaba Cloud Container Service.

Specifically, you can only use static OSS volumes. Dynamic OSS volumes are not supported. You can use a static OSS volume in either of the following two ways:

-   Use an OSS bucket through a volume.
-   Use an OSS bucket through a Persistent Volume \(PV\) and a Persistent Volume Claim \(PVC\).

## Prerequisites {#section_dyz_ybt_vdb .section}

You have created a bucket in the OSS console.

## OSS parameter setting {#section_dpz_zbt_vdb .section}

-   OSS: OSS is a shared storage system that can provide storage services to multiple pods at the same time.
-   bucket: Only buckets can be mounted to a Kubernetes cluster. The sub-directories or files under a bucket cannot be mounted to a Kubernetes cluster.
-   url: Specify an OSS endpoint, namely, the domain name used to mount an OSS bucket to a cluster.
-   akId: Enter your Access Key ID.
-   akSecret: Enter your Access Key Secret.
-   otherOpts: Customize other parameters in the format of `-o *** -o ***`.

## Notices {#section_gyz_ybt_vdb .section}

-   If your Kubernetes cluster is created before February 6th, 2018, [Install the plug-in](reseller.en-US/User Guide/Kubernetes cluster/Storage management/Install the plug-in.md#) before using a volume. Before you can use the OSS volume, you must first create a secret and then enter your Access Key information into the secret when you deploy the flexvolume service.

-   If you use the flexvolume component of an earlier version, we recommend that you upgrade it to the latest version.

    **Note:** When you upgrade a Kubernetes cluster or restart a kubelet, an OSS volume mounted by the flexvolume component of an earlier version will cause the OSSFS driver to restart. To solve the exception caused by this event, you must recreate the pod that used the OSS volume to remount the OSS volume to the cluster. However, you can solve this issue more easily by upgrading the flexvolume component to the latest version.


## Use a static OSS volume {#section_d13_bct_vdb .section}

**Use an OSS bucket through a volume**

Use a oss-deploy.yaml file to create a pod.

```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: nginx-oss-deploy
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx-flexvolume-oss
        image: nginx
        volumeMounts:
          - name: "oss1"
            mountPath: "/data"
        livenessProbe:
          exec:
            command:
            - sh
            - -c
            - cd /data
          initialDelaySeconds: 30
          periodSeconds: 30
      volumes:
        - name: "oss1"
          flexVolume:
            driver: "alicloud/oss"
            options:
              bucket: "docker"
              url: "oss-cn-hangzhou.aliyuncs.com"
              akId: ***
              akSecret: ***
              otherOpts: "-o max_stat_cache_size=0 -o allow_other"
```

**Use a PV and a PVC**

**Step 1: Create a PV**

You can create a PV by using a YAML file or the Container Service console.

**Use a YAML file to create a PV**

Use a oss-pv.yaml file to create a PV as follows:

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-oss
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  storageClassName: oss
  flexVolume:
    driver: "alicloud/oss"
    options:
      bucket: "docker"
      url: "oss-cn-hangzhou.aliyuncs.com"
      akId: ***
      akSecret: ***
      otherOpts: "-o max_stat_cache_size=0 -o allow_other"
```

**Create an OSS volume in the Container Service console**

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane under Kubernetes, choose **Clusters** \> **Volumes**.
3.  Select the target cluster from the cluster drop-down list and then click **Create** in the upper-right corner.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16689/155859175810740_en-US.png)

4.  In the displayed dialog box, set the volume parameters.

    -   **Storage type**: OSS is selected in this example.
    -   **Name**: Customize a volume name. The volume name must be unique in the cluster. In this example, pv-oss is set as the volume name.
    -   **Capacity**: Set the volume capacity.
    -   **Access Mode**: By default, it is set to ReadWriteMany.
    -   **AccessKey ID** and **AccessKey Secret**: Use these two parameters to specify the Access Key used to access OSS.
    -   **Bucket ID**: Select an OSS bucket name. Click **Select Bucket**. In the displayed dialog box, select the target bucket and click**Select**.
    -   **Access Domain Name**. If the selected bucket and the cluster ECS instances are in different regions, you need to select**Internet**. If they are in the same region, your choice is dependent on your cluster network type. If your cluster uses a VPC, you need to select **VPC**; if your cluster uses a classic network, you need to select **Intranet**.
    -   **Tag**: Add tags to the volume.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16689/155859175810741_en-US.png)

5.  Click **Create**.

**Step 2: Create a PVC**

Use a oss-pvc.yaml file to create a PVC as follows:

```
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: pvc-oss
spec:
  storageClassName: oss
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 5Gi
```

**Step 3: Create a pod**

Use a oss-pod.yaml file to create a pod.

```
apiVersion: v1
kind: Pod
metadata:
  name: "flexvolume-oss-example"
spec:
  containers:
    - name: "nginx"
      image: "nginx"
      volumeMounts:
          - name: pvc-oss
            mountPath: "/data"
      livenessProbe:
        exec:
          command:
          - sh
          - -c
          - cd /data
        initialDelaySeconds: 30
        periodSeconds: 30
  volumes:
  - name: pvc-oss
    persistentVolumeClaim:
        claimName: pvc-oss
```

