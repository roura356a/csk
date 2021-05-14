---
keyword: [use LVM to manage local storage, CSI]
---

# Use LVM to manage local storage

Professional edge Kubernetes clusters allow you to use Logical Volume Manager \(LVM\) to manage local storage. LVM can automatically manage the lifecycle of logical volumes and schedule volumes based on the storage capacity of nodes. To use LVM to manage local storage as persistent volumes \(PVs\) and persistent volume claims \(PVCs\), you need only to define the topological relationship of local disks on the nodes. This topic describes how to use LVM to manage local storage in professional edge Kubernetes clusters.

Local disks are available in cluster nodes.

## Install node-resource-manager and csi-local-plugin

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and choose **More** \> **Manage System Components** in the **Actions** column.

4.  On the **Add-ons** page, select **node-resource-manager** and **csi-local-plugin** in the Storage section and click **Install** to install the components.

5.  In the **Note** dialog box, click **OK**.


## Configure the VolumeGroup

**Note:** To ensure data security, the components do not delete VolumeGroups or physical volumes. Before you can redefine a VolumeGroup, you must delete the existing VolumeGroup.

1.  Use the following YAML template to configure a ConfigMap that defines the topology for the VolumeGroup:

    ```
    apiVersion: v1
    kind: ConfigMap
    metadata:
      name: node-resource-topo
      namespace: kube-system
    data:
      volumegroup: |-
        volumegroup:
        - name: volumegroup1
          key: kubernetes.io/storagetype
          operator: In
          value: lvm
          topology:
            type: device
            devices:
            - /dev/sdb1
            - /dev/sdb2
            - /dev/sdc
    ```

    The following table describes the parameters.

    |Parameter|Description|
    |---------|-----------|
    |name|The name of the VolumeGroup.|
    |key|Specifies the key that is used to match the key of the label on the nodes.|
    |operator|Specifies the `operator that is used in the label selector`. Valid values:    -   In: A match is found only when the `value` is the same as the `value` of the label that has the specified key.
    -   NotIn: A match is found only when the `value` is different from the `value` of the label that has the specified key.
    -   Exists: A match is found when the node has a label that has the specified key.
    -   DoesNotExist: A match is found when the node does not have a label that has the specified key. |
    |value|Specifies the value that is used to match the `value` of the label that has the specified key.|
    |topology|Specifies the topology of devices on the node. `topology.devices` specifies the paths of local disks on the node. The specified disks are added to the VolumeGroup.|

2.  Add labels to the nodes.

    -   Add a custom label to storage nodes based on the label that is specified in Step 1. This allows you to select nodes that meet the topological requirements. The label that is specified in Step 1 is as follows: `kubernetes.io/storagetype=lvm`.
    -   Add the `alibabacloud.com/edge-enable-localstorage='true'` label to storage nodes. This allows the local storage manager to schedule the nodes.
    The **node-resource-manager** component on the node automatically creates a physical volume based on the preceding configurations and adds the physical volume to the VolumeGroup.


## Use LVM to manage local storage

Use the following YAML file to define a PVC that specifies the StorageClass. Run the `kubectl apply -f ****.yaml` command to create the PVC. One PVC corresponds to one logical volume on the node. After the pod is created, the logical volume is mounted on the pod.

**Note:** In this example, the default `storageClassName` is `csi-local-lvm`.

```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: lvm-pvc-test
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 50Mi
  storageClassName: csi-local-lvm

---
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    k8s-app: local-test
  name: local-test
  namespace: default
spec:
  replicas: 1
  selector:
    matchLabels:
      k8s-app: local-test
  template:
    metadata:
      labels:
        k8s-app: local-test
    spec:
      hostNetwork: true
      containers:
      - image: nginx:1.15.7-alpine
        imagePullPolicy: IfNotPresent
        name: nginx
        resources: {}
        volumeMounts:
          - name: local-pvc
            mountPath: /data
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      tolerations:
      - operator: Exists
      nodeSelector:
        alibabacloud.com/is-edge-worker: "true"
      volumes:
      - name: local-pvc
        persistentVolumeClaim: 
          claimName: lvm-pvc-test
```

Run the following command to check whether the logical volume is mounted:

```
kubectl exec -it local-test-564dfcf6dc-qhfsf sh
/ # ls /data
```

Expected output:

```
lost+found
```

The output indicates that the logical volume is mounted on the pod.

