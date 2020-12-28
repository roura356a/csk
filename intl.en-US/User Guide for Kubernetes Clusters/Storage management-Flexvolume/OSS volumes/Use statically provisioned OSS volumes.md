# Use statically provisioned OSS volumes

This topic describes how to use statically provisioned Object Storage Service \(OSS\) volumes in Container Service for Kubernetes \(ACK\).

## Use an OSS bucket as a statically provisioned volume

1.  Create the oss-deploy.yaml file and copy the following content to the file.

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

1.  Create a PV.

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
        3.  On the Clusters page, find the cluster that you want to manage and click **Details** in the **Actions** column of the cluster.
        4.  The Cluster Information page appears. In the left-side navigation pane, click **Persistent Volumes**. The Persistent Volume Claims tab appears.
        5.  Click the **Persistent Volumes** tab and click **Create**.
        6.  In the **Create PV** dialog box, set the required parameters.

            |Parameter|Description|
            |---------|-----------|
            |**PV Type**|In this example, OSS is selected.|
            |**Volume Plug-in**|By default, Flexvolume is selected. For more information about volume plug-ins, see [Differences between Flexvolume and CSI](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/Volume plug-ins.md).|
            |**Access Mode**|By default, ReadWriteMany is selected.|
            |**Cloud ID**|In the **Select Disk** dialog box, find the disk that you want to use and click Select in the Actions column of the disk.|
            |**File System Type**|The type of the file system.|
            |**Labels**|Add labels to the PV.|

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


## Use a secret to provide your AccessKey information

1.  Run the following command to create a secret:

    ```
    kubectl create secret generic osssecret --from-literal=akId='111111' --from-literal=akSecret='2222222' --type=alicloud/oss -n default
    ```

    -   osssecret: the name of the secret. You can specify a secret name.
    -   akId: your AccessKey ID.
    -   akSecret: your AccessKey secret.
    -   type: Set this parameter to alicloud/oss. This is the namespace to which the required pod belongs.
2.  Use the secret in a PV.

    Specify the secret in the secretRef field of the PV.

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

3.  Use the secret in a volume.

    Specify the secret in the secretRef field of the volume.

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

    **Note:** When you use a secret to configure your AccessKey information, the secret and the required pod must belong to the same namespace.


