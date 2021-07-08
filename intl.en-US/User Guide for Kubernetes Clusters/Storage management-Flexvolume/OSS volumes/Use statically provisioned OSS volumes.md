---
keyword: [statically provisioned OSS volumes, K8s]
---

# Use statically provisioned OSS volumes

This topic describes how to use statically provisioned Object Storage Service \(OSS\) volumes in Container Service for Kubernetes \(ACK\).

## Use an OSS bucket as a statically provisioned volume

1.  Create a file named oss-deploy.yaml and copy the following content to the file.

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

2.  Run the following command to create a pod:

    ```
    kubectl apply -f oss-deploy.yaml
    ```


## Use an OSS bucket to create a PV and a PVC

1.  Create a persistent volume \(PV\).

    You can create a PV in the ACK console or by using a YAML file.

    -   Create a PV by using a YAML file

        Use the following oss-pv.yaml file to create a PV.

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

    -   Create a PV in the ACK console
        1.  Log on to the [ACK console](https://cs.console.aliyun.com).
        2.  In the left-side navigation pane, click **Clusters**.
        3.  On the Clusters page, find the cluster that you want to manage, and click the name of the cluster or click **Details** in the **Actions** column.
        4.  In the left-side navigation pane of the details page, choose **Volumes** \> **Persistent Volumes**.
        5.  Click the **Persistent Volumes** tab and click **Create**.
        6.  In the **Create PV** dialog box, set the required parameters.

            |Parameter|Description|
            |---------|-----------|
            |**PV Type**|In this example, OSS is selected.|
            |**Volume Name**|The name of the PV that you want to create. The name must be unique in the cluster. In this example, pv-oss is used.|
            |**Volume Plug-in**|In this example, Flexvolume is selected. For more information about volume plug-ins, see [Differences between FlexVolume and CSI](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/Volume plug-ins.md).|
            |**Capacity**|The capacity of the PV.|
            |**Access Mode**|Default value: ReadWriteMany.|
            |**AccessKey ID**|The AccessKey pair that is required to access the OSS bucket.|
            |**AccessKey Secret**|
            |**Optional Parameters**|Enter custom parameters in the format of `-o *** -o ***`.|
            |**Bucket ID**|The name of the OSS bucket that you want to mount. Click **Select Bucket**. In the dialog box that appears, select the OSS bucket that you want to mount and click **Select**.|
            |**Endpoint**|Select **Public Endpoint** if the OSS bucket and the Elastic Compute Service \(ECS\) instances in the cluster are deployed in different regions.**** Select **Internal Endpoint** if the OSS bucket is deployed in a classic network.|
            |**Label**|Add labels to the PV.|

        7.  Click **Create**.
2.  Create a PVC.

    Use the following oss-pvc.yaml file to create a PVC.

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

3.  Create a pod.

    Use the following oss-deploy.yaml file to create a pod.

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: oss-static
      labels:
        app: nginx
    spec:
      replicas: 1
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
            image: nginx
            ports:
            - containerPort: 80
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


## Use a Secret to provide AccessKey information

1.  Run the following command to create a Secret:

    ```
    kubectl create secret generic osssecret --from-literal=akId='111111' --from-literal=akSecret='2222222' --type=alicloud/oss -n default
    ```

    -   osssecret: The name of the Secret.
    -   akId: The AccessKey ID.
    -   akSecret: The AccessKey secret.
    -   type: Set this parameter to alicloud/oss. The Secret and the pod that uses the Secret must belong to the same namespace.
2.  Use the Secret in a PV.

    Specify the Secret in the secretRef field of the PV.

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
        secretRef:
          name: "osssecret"
        options:
          bucket: "docker"
          url: "oss-cn-hangzhou.aliyuncs.com"
          otherOpts: "-o max_stat_cache_size=0 -o allow_other"
    ```

3.  Use the Secret in a volume.

    Specify the Secret in the secretRef field of the volume.

    ```
    apiVersion: extensions/v1beta1
    kind: Deployment
    metadata:
      name: nginx-oss-deploy1
    spec:
      replicas: 3
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
                subPath: "hello"
          volumes:
            - name: "oss1"
              flexVolume:
                driver: "alicloud/oss"
                secretRef:
                  name: "osssecret"
                options:
                  bucket: "aliyun-docker"
                  url: "oss-cn-hangzhou.aliyuncs.com"
                  otherOpts: "-o max_stat_cache_size=0 -o allow_other"
    ```

    **Note:** When you use a Secret to configure the AccessKey pair, the Secret and the pod that uses the Secret must belong to the same namespace.


