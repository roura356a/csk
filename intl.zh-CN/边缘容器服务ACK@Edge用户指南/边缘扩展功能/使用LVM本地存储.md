---
keyword: [LVM本地存储, CSI]
---

# 使用LVM本地存储

ACK@Edge Pro版集群支持LVM（Logical Volume Manager）本地存储，提供自动化的逻辑卷生命周期管理能力，且能根据节点LVM本地存储容量进行调度。您只需定义节点本地盘的拓扑关系，即可通过原生PVC/PV方式使用LVM本地存储。本文介绍边缘Pro版集群如何使用LVM本地存储。

集群的节点有可用的本地数据盘。

## 安装node-resource-manager和csi-local-plugin组件

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面，选择目标集群，并在目标集群右侧**操作**列下，选择**更多** \> **系统组件管理**。

4.  在**组件管理**页面的存储区域，找到**node-resource-manager**和**csi-local-plugin**组件，单击**安装**。

5.  在**提示**对话框中单击**确定**。


## 配置节点VolumeGroup

**说明：** 为了保障数据安全，组件不会对VolumeGroup和Physical Volume进行删除。如果需要重新定义VolumeGroup，您需要先自行清理。

1.  使用如下YAML文件样例配置ConfigMap，指定节点VolumeGroup拓扑配置。

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

    参数解释如下：

    |参数|说明|
    |--|--|
    |name|VolumeGroup的名字。|
    |key|匹配集群节点标签中的key的值。|
    |operator|集群定义的`Labels selector operator`，主要包含如下四种操作符：    -   In：只有`value`的值与集群节点标签中的key对应的`value`值相同时才会匹配。
    -   NotIn：只有`value`的值与集群节点标签中的key对应的`value`值不相同时才会匹配。
    -   Exists：集群节点标签存在key就会匹配。
    -   DoesNotExist：集群节点标签不存在key就会匹配。 |
    |value|匹配Kubernetes Node Labels的key对应的`value`的值。|
    |topology|节点上设备拓扑关系，其中`topology.devices`指定节点上的本地盘路径，该设备将被加到VolumeGroup中。|

2.  给节点打标。

    -   按步骤1中的标签规则给相应存储节点添加对应自定义标签，以指定符合相应拓扑的节点类型。如步骤1中对应的标签为：`kubernetes.io/storagetype=lvm`。
    -   给存储节点添加固定标签：`alibabacloud.com/edge-enable-localstorage='true'`，使本地存储管理组件能调度到该节点。
    节点上的**node-resource-manager**组件将根据以上配置，自动创建对应的Physical Volume，并将其加入到VolumeGroup中。


## 使用LVM本地存储

使用以下YAML文件样例创建PVC指定StorageClass，并执行`kubectl apply -f ****.yaml`命令。一个PVC对应节点上一块逻辑卷，Pod创建成功后将挂载该逻辑卷。

**说明：** 集群默认的`storageClassName`为`csi-local-lvm`。

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

执行以下命令，查看逻辑卷是否挂载成功。

```
kubectl exec -it local-test-564dfcf6dc-qhfsf sh
/ # ls /data
```

预期输出：

```
lost+found
```

从上述输出信息，可以知道逻辑卷已成功挂载在Pod上。

