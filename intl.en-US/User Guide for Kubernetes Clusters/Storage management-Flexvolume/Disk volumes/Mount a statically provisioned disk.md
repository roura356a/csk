# Mount a statically provisioned disk

This topic describes how to mount a statically provisioned disk as a volume or by using a persistent volume \(PV\) and a persistent volume claim \(PVC\).

To use a disk as a volume, you must first create the disk in the Elastic Compute Service \(ECS\) console. For more information, see [Create a disk](/intl.en-US/Block Storage/Cloud disks/Create a cloud disk/Create a disk.md).

**Note:** Your disk must meet the following requirements:

-   An ultra disk must have a minimum capacity of 20 GiB.
-   A standard SSD must have a minimum capacity of 20 GiB.
-   An enhanced SSD \(ESSD\) must have a minimum capacity of 20 GiB.

## Mount a statically provisioned disk as a volume

Use a disk-deploy.yaml file to create a pod.

1.  Create a disk-deploy.yaml file based on the following code:

    ```
    apiVersion: v1
    kind: Service
    metadata:
      name: nginx
      labels:
        app: nginx
    spec:
      ports:
      - port: 80
        name: web
      clusterIP: None
      selector:
        app: nginx
    ---
    apiVersion: apps/v1
    kind: StatefulSet
    metadata:
      name: web
    spec:
      selector:
        matchLabels:
          app: nginx
      serviceName: "nginx"
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
              name: web
            volumeMounts:
            - name: d-wz9f6l6nm0uvazgh****
              mountPath: /data
          volumes:
            - name: "d-wz9f6l6nm0uvazgh****"
              flexVolume:
                driver: "alicloud/disk"
                fsType: "ext4"
                options:
                  volumeId: "d-wz9f6l6nm0uvazgh****"
    ```

2.  Run the following command to create a pod:

    ```
    kubectl apply -f disk-deploy.yaml
    ```


**Note:**

In the `volumes` section, we recommend that you set `name` and `volumeId` to the same value.

If your cluster is deployed across multiple zones, schedule the pod to the zone where the disk is created. Add the following configuration to add a `node selector` to the pod configurations:

```
nodeSelector:
        failure-domain.beta.kubernetes.io/zone: cn-hangzhou-b
```

## Mount a statically provisioned disk by using a PV and a PVC

1.  Create a PV of the disk type.

    You can create a PV in the Container Service for Kubernetes \(ACK\) console or by using a YAML file.

    -   Create a PV by using a YAML file.
        1.  Create a disk-pv.yaml file based on the following code:

            ```
            apiVersion: v1
            kind: PersistentVolume
            metadata:
              name: d-bp1j17ifxfasvts3****
              labels:
                failure-domain.beta.kubernetes.io/zone: cn-hangzhou-b
                failure-domain.beta.kubernetes.io/region: cn-hangzhou
            spec:
              capacity:
                storage: 20Gi
              storageClassName: disk
              accessModes:
                - ReadWriteOnce
              flexVolume:
                driver: "alicloud/disk"
                fsType: "ext4"
                options:
                  volumeId: "d-bp1j17ifxfasvts3****"
            ```

            **Note:** The `name` of the PV must be the same as the disk ID that is specified by `volumeId`.

        2.  Run the following command to create a PV:

            ```
            kubectl apply -f disk-pv.yaml
            ```

    -   You can also create the PV in the ACK console.
        1.  Log on to the [ACK console](https://cs.console.aliyun.com).
        2.  In the left-side navigation pane of the ACK console, click **Clusters**.
        3.  On the Clusters page, find the cluster that you want to manage and click the name or click **Details** in the **Actions** column.
        4.  In the left-side navigation pane of the details page, choose **Volumes** \> **Persistent Volumes**.
        5.  In the upper-right corner of the **Persistent Volumes** page, click **Create**.
        6.  In the **Create PV** dialog box, set the parameters.

            |Parameter|Description|
            |---------|-----------|
            |**PV Type**|In this example, Cloud Disk is selected.|
            |**Volume Plug-in**|In this example, **Flexvolume** is selected.|
            |**Access Mode**|By default, this parameter is set to ReadWriteOnce.|
            |**Disk ID**|Select a mountable disk in the same region and zone as your cluster.|
            |**File System Type**|Select the file system type of the disk. Valid values: **ext4**, **ext3**, **xfs**, and **vfat**. Default value: **ext4**.|
            |**Label**|Add labels to the PV.|

        7.  After you complete the settings, click **Create**.
2.  Create a PVC.

    1.  Create a disk-pvc.yaml file based on the following code:

        ```
        kind: PersistentVolumeClaim
        apiVersion: v1
        metadata:
          name: pvc-disk
        spec:
          accessModes:
            - ReadWriteOnce
          storageClassName: disk
          resources:
            requests:
              storage: 20Gi
        ```

    2.  Run the following command to create a PVC:

        ```
        kubectl apply -f disk-pvc.yaml
        ```

3.  Create a pod.

    1.  Create a disk-pod.yaml file based on the following code:

        ```
        apiVersion: v1
        kind: Service
        metadata:
          name: nginx
          labels:
            app: nginx
        spec:
          ports:
          - port: 80
            name: web
          clusterIP: None
          selector:
            app: nginx
        ---
        apiVersion: apps/v1
        kind: StatefulSet
        metadata:
          name: web
        spec:
          selector:
            matchLabels:
              app: nginx
          serviceName: "nginx"
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
                  name: web
                volumeMounts:
                - name: pvc-disk
                  mountPath: /data
              volumes:
                - name: pvc-disk
                  persistentVolumeClaim:
                    claimName: pvc-disk
        ```

    2.  Run the following command to create a pod:

        ```
        kubectl apply -f disk-pod.yaml
        ```


