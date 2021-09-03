---
keyword: [ACK Windows nodes, SMB, storage resource, Kubernetes]
---

# Mount SMB file systems to Windows containers

You can mount Server Message Block \(SMB\) file systems of Apsara File Storage NAS \(NAS\) to Windows containers that run in a Container Service for Kubernetes \(ACK\) cluster. This topic describes how to mount SMB file systems to Windows containers.

-   [Create a Windows node pool](/intl.en-US/User Guide for Kubernetes Clusters/Windows container/Create a Windows node pool.md).
-   [Step 2: Select a type of cluster credentials](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to ACK clusters by using kubectl.md).
-   In the NAS console, create an SMB file system in the virtual private cloud \(VPC\) where the ACK cluster is deployed, and create a mount target for the SMB file system. For more information, see [Mount an SMB file system on Windows]().

## Step 1: Create a PV and a PVC

1.  Use the following YAML template to create a persistent volume \(PV\) and a persistent volume claim \(PVC\).



    The following table describes the required parameters in the PV template.

    |Parameter|Description|
    |---------|-----------|
    |driver|The driver that is used to mount the SMB file system. Set the value to alicloud/smb.exe.|
    |server|The domain name of the mount target for the SMB file system. The mount target must be in the same VPC as the ACK cluster.|
    |path|The path where the SMB file system is mounted. Set the value to \\myshare or a subdirectory that starts with \\myshare.|
    |user|The username that is used to log on to a node. We recommend that you use workgroup\\administrator.|
    |password|The password that is used to log on to a node.|

2.  Run the `kubectl get pvc |grep pvc-smb` command to view the newly created PVC.

    The following output is returned:

    ```
    pvc-smb                    Bound    pv-smb                   5Gi        RWX                                         24h
    ```


## Step 2: Deploy an application

1.  Use the following YAML template to deploy an application.

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nginx-smb
      namespace: default
    spec:
      selector:
        matchLabels:
          app: nginx-smb
      template:
        metadata:
          labels:
            app: nginx-smb
        spec:
          replicas: 2
          tolerations:
          - effect: NoSchedule
            key: os
            operator: Equal
            value: windows
          containers:
          - args:
            - -Command
            - start-sleep 10000
            command:
            - pwsh.exe
            image: registry.cn-hangzhou.aliyuncs.com/acs/flexvolume:v1.16.9.7be0fa0-windows1809
            imagePullPolicy: IfNotPresent
            name: nginx
            volumeMounts:
            - mountPath: /data
              name: pvc-nas
          restartPolicy: Always
          volumes:
          - name: pvc-nas
            persistentVolumeClaim:
              claimName: pvc-smb
    ```

2.  Run the `kubectl get pod` command to view the state of the application.

    The following output is returned:

    ```
    NAME                        READY   STATUS    RESTARTS   AGE
    nginx-smb-965fb4597-jz6fv   1/1     Running   0          95s
    nginx-smb-965fb4597-zvbhk   1/1     Running   0          42s
    ```

    If the application is in the **Running** state, the application is created.


