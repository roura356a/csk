---
keyword: [Storage Operator, 存储组件的安装, 存储组件的升级]
---

# 使用Storage Operator进行存储组件的部署与升级

Storage Operator组件负责阿里云ACK存储组件的安装与升级。组件的具体配置，可以通过修改ConfigMap来修改。本文档介绍如何配置组件信息及部署Storage-Operator组件。

## 组件介绍

storage-operator组件用于管理存储组件的生命周期。storage-operator部署为Deployment，依赖自身镜像中的默认组件配置和通过ConfigMap自定义的组件配置，进行存储组件的部署与升级操作，进而降低容器研发与运维的复杂性。

-   默认组件配置：每个发布的storage-operator组件都有对应的默认配置。
-   通过ConfigMap自定义配置：通过ConfigMap自定义组件配置，可配置版本信息、是否部署组件等信息。

Storage-Operator会优先使用自定义的组件配置，只有没有指定具体配置时，才使用镜像默认配置。

![Storage-Operator](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9986656161/p253634.png)

**说明：**

-   Storage-Operator镜像发布时，都会有默认存储组件配置文件。
-   Storage-Operator运行时，会挂载一个对存储组件配置的ConfigMap文件，且读取该配置。
-   Storage-Operator会整合自定义配置和默认配置，综合判断是否部署与升级组件。

## 步骤一：配置组件信息

**方式一：镜像默认配置**

目前默认的存储组件有：storage-snapshot-manager、storage-analyzer、storage-auto-expander及storage-monitor。

每个版本的storage-operator都有默认的组件配置，以v1.18.8.0版本为例，配置示例如下：

```
{
  "storage-snapshot-manager": {
    "install":  "true",
    "imageTag": "v1.18.8.0-81508da-aliyun",
    "imageRep": "acs/storage-snapshot-manager",
    "template": "/acs/templates/storage-snapshot-manager/install.yaml"
  },
  "storage-analzyer": {
    "install":  "false",
    "imageTag": "v1.18.8.0-06c5560-aliyun",
    "imageRep": "acs/storage-analyzer",
    "template": "/acs/templates/storage-analyzer/install.yaml"
  },
  "storage-auto-expander": {
    "install":  "false",
    "imageTag": "v1.18.8.0-4852fd4-aliyun",
    "imageRep": "acs/storage-auto-expander",
    "crdTmpl":  "/acs/templates/storage-auto-expander/crd.yaml",
    "template": "/acs/templates/storage-auto-expander/install.yaml"
  },
  "storage-monitor": {
    "install":  "true",
    "imageTag": "v1.18.8.0-c4744b6-aliyun",
    "imageRep": "acs/storage-monitor",
    "template": "/acs/templates/storage-monitor/install.yaml",
    "svcTmpl":  "/acs/templates/storage-monitor/service.yaml"
  }
}
```

默认配置中各组件定义的参数说明如下：

|参数|说明|
|--|--|
|install|表示该组件是否需要部署，默认为`false`。|
|imageTag|表示如果部署该组件，默认使用的镜像版本。|
|imageRep|表示如果部署该组件，默认使用的镜像名。|
|crdTmpl|表示是否需要部署CRD，该值表示CRD模板地址。为空时表示不部署CRD。|
|svcTmpl|表示是否需要部署Service，该值表示Service模板地址。|
|template|表示该组件的部署模板地址。|

**说明：** 各组件的参数可以在配置（Configmap）中修改。

**方式二：自定义配置（ConfigMap）**

ConfigMap示例模板如下：

```
kind: ConfigMap
apiVersion: v1
metadata:
  name: storage-operator
  namespace: kube-system
data:
  storage-snapshot-manager: |
    # deploy config
    install:   false
    imageTag:  v1.16.aaaa
    imageRep:  acs/storage-snapshot-manager
    template:  /acs/templates/storage-snapshot-manager/install.yaml
    # env config
    SNAPSHOT_INTERVAL: 30
  storage-analyzer: |
    # deploy config
    install:   false
    imageTag:  v1.16.bbbb
    imageRep:  acs/storage-analyzer
    template:  /acs/templates/storage-analyzer/install.yaml
    # env config
    LOOP_INTERVAL: 30
  storage-auto-expander: |
    # deploy config
    install:   false
    imageTag:  v1.16.3adsadfs
    imageRep:  acs/storage-auto-expander
    crdTmpl:   /acs/templates/storage-auto-expander/crd.yaml
    template:  /acs/templates/storage-auto-expander/install.yaml
    # env config
    POLLING_INTERVAL_SECONDS: 60
```

自定义配置需要注意以下几点：

-   每个组件的配置分为两部分：
    -   部署配置参数，即示例中deploy config部分参数，是定义该组件是否部署及部署的版本信息等。Storage-Operator根据该部分信息安装、升级组件。
    -   环境配置参数，即示例中env config部分参数，是给对应的存储组件使用。例如：`SNAPSHOT_INTERVAL`配置为storage-snapshot-manager的环境变量。
-   若自定义了部署配置部分的值，将覆盖默认配置中的对应值。
-   部署配置参数是组件部署配置保留字段，不能用作环境变量。
-   可针对于相应组件的特定参数自定义配置。

## 步骤二：部署Storage-Operator

1.  执行以下命令部署Storage-Operator组件。

    ```
    kubectl apply -f StorageOperator.yaml
    ```

    部署Storage-Operator组件的示例模板StorageOperator.yaml如下所示：

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: storage-operator
      namespace: kube-system
      labels:
        app: storage-operator
    spec:
      selector:
        matchLabels:
          app: storage-operator
      template:
        metadata:
          labels:
            app: storage-operator
        spec:
          tolerations:
            - operator: "Exists"
          priorityClassName: system-node-critical
          serviceAccount: storage-operator-admin
          containers:
          - name: storage-operator
            image: registry.cn-hangzhou.aliyuncs.com/acs/storage-operator:v1.18.8.28-18cca7b-aliyun
            imagePullPolicy: Always
            volumeMounts:
            - mountPath: /acs/configmap/
              name: storage-operator
          restartPolicy: Always
          volumes:
          - configMap:
              name: storage-operator
            name: storage-operator
    ```

2.  执行以下命令查看storage-operator组件是否启动。

    ```
    kubectl get pods -nkubes-system | grep storage-operator
    ```

    预期输出：

    ```
    NAME                        READY   STATUS    RESTARTS   AGE         IP                NODE              NOMINATED NODE   READINESS GATES
    storage-operator-***        1/1     Running   0          6d20h       192.168.XX.XX     virtual-kubelet   <none>           <none>
    ```


