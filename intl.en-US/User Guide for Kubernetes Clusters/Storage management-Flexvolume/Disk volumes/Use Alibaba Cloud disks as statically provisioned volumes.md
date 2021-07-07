# Use Alibaba Cloud disks as statically provisioned volumes

You can use Alibaba Cloud disks as volumes. You can also use disks to create persistent volumes \(PVs\) or persistent volume claims \(PVCs\).

To use a disk as a volume, you must first create the disk in the Elastic Compute Service \(ECS\) console. For more information, see [Create a disk](/intl.en-US/Block Storage/Cloud disks/Create a cloud disk/Create a disk.md).

## Use a disk as a volume

Use the following disk-deploy.yaml file to create a pod.

1.  Create the disk-deploy.yaml file and copy the following content to the file.

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
            - name: d-wz9f6l6nm0uvazgh7y17
              mountPath: /data
          volumes:
            - name: "d-wz9f6l6nm0uvazgh7y17"
              flexVolume:
                driver: "alicloud/disk"
                fsType: "ext4"
                options:
                  volumeId: "d-wz9f6l6nm0uvazgh7y17"
    ```

2.  Run the following command to create a pod:

    ```
    kubectl apply -f disk-deploy.yaml
    ```


**Note:**

In the `volumes` section, we recommend that you set `name` and `volumeId` to the same value.

If your cluster is deployed across multiple zones, schedule the pod to the zone where the disk is provisioned. Run the following command to add a nodeSelector field to the pod:

```
nodeSelector:
        failure-domain.beta.kubernetes.io/zone: cn-hangzhou-b
```

## Use disks to create PVs or PVCs

1.  Use a disk to create a PV.

    You can create a PV in the Container Service for Kubernetes \(ACK\) console or by using a YAML file.

    -   Create a PV by using a YAML file

        Use the following disk-pv.yaml file to create a PV.

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

        **Note:** The `name` field value of the PV must be the same as the disk ID that is specified by `volumeId`.

    -   Create a PV in the ACK console
        1.  Log on to the [ACK console](https://cs.console.aliyun.com).
        2.  In the left-side navigation pane, click **Clusters**.
        3.  On the Clusters page, find the cluster that you want to manage and click **Details** in the **Actions** column of the cluster.
        4.  The Cluster Information page appears. In the left-side navigation pane, click **Persistent Volumes**.
        5.  On the Persistent Volumes page, click the **Persistent Volumes** tab and click **Create** in the upper-right corner of the page.
        6.  Set the parameters in the **Create PV** dialog box.

            |Parameter|Description|
            |---------|-----------|
            |**PV Type**|In this example, Cloud Disk is selected.|
            |**Volume Plug-in**|In this example, **Flexvolume** is selected.|
            |**Access Mode**|By default, ReadWriteOnce is used.|
            |**Disk ID**|Select a disk that is in the same region and zone as your cluster.|
            |**File System Type**|Select the file system of the disk. Supported file systems include ext4, ext3, xfs, and vfat. Default value: ext4.|
            |**Label**|Add labels to the PV.|

        7.  After you complete the settings, click **Create**.
2.  Create a PVC.

    Use the following disk-pvc.yaml file to create a PVC:

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

3.  Create a pod.

    Use the following disk-pod.yaml file to create a pod:

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


