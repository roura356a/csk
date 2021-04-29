---
keyword: [Storage Operator, install storage components, upgrade storage components]
---

# Use Storage Operator to deploy and upgrade storage components

You can use Storage Operator to deploy and upgrade the storage components provided by Container Service for Kubernetes \(ACK\). You can use ConfigMaps to modify the configurations of storage components. This topic describes how to configure and deploy Storage Operator.

## Introduction

Storage Operator is used to manage the lifecycle of storage components. Storage Operator runs as a Deployment, which deploys and upgrades storage components based on the default configurations inherited from the image and the custom configurations provided by ConfigMaps. This helps reduce the complexity of container development and maintenance.

-   Default configurations: Storage Operator provides the default configurations of storage components. The default configurations vary based on the version of Storage Operator.
-   Custom configurations: ConfigMaps can be used to define custom configurations of storage components, such as version information, and whether to install the component.

Storage Operator preferably uses custom configurations. The default configurations are used only when the custom configurations are not specified.

![Storage-Operator](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4352669161/p253634.png)

**Note:**

-   Each image of Storage Operator contains the default configuration file.
-   When Storage Operator runs as a Deployment, the Deployment reads configurations from a ConfigMap file that is mounted on the Deployment and contains configurations of storage components.
-   Storage Operator determines whether to deploy and upgrade a storage component by combining the default and custom configurations.

## Step 1: Configure the components

**Method 1: Use the default configurations provided by the image**

ACK supports the following storage components: storage-snapshot-manager, storage-analyzer, storage-auto-expander, and storage-monitor.

Storage Operator provides the default configurations of storage components. The default configurations vary based on the version of Storage Operator. In this example, V1.18.8.0 is used.

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

The following table describes the parameters in the default configurations.

|Parameter|Description|
|---------|-----------|
|install|Specifies whether to deploy the component. Default value: `false`.|
|imageTag|Specifies the default image version if the component is to be deployed.|
|imageRep|Specifies the default image name if the component is to be deployed.|
|crdTmpl|Specifies the template path of the Custom Resource Definition \(CRD\) that you want to deploy. If this parameter is empty, it indicates that no CRD is to be deployed.|
|svcTmpl|Specifies the template path of the Service that you want to deploy.|
|template|Specifies the template path of the component that you want to deploy.|

**Note:** You can use a ConfigMap to modify the parameters.

**Method 2: Use a ConfigMap to customize the configurations**

In this example, the following ConfigMap template is provided:

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

Take note of the following items when you use custom configurations:

-   The configurations of each component consist of two parts:
    -   Deployment configurations, which are defined by the parameters in the deploy config section in the preceding example. The parameters specify whether to install the component and the component version that is deployed. Storage Operator deploys and upgrades components based on these configurations.
    -   Environment configurations, which are defined by the parameters in the env config section in the preceding example. The parameters are used by the storage component. For example, `SNAPSHOT_INTERVAL` is configured as an environment variable for the storage-snapshot-manager component.
-   The parameter values that are specified in the custom configurations overwrite the parameter values in the default configurations.
-   The parameters used in deployment configurations are reserved parameters and cannot be used as environment variables.
-   You can customize specific parameters based on the features of the component.

## Step 2: Deploy Storage Operator

1.  Run the following command to deploy Storage Operator:

    ```
    kubectl apply -f StorageOperator.yaml
    ```

    In this example, the following StorageOperator.yaml file is used to deploy Storage Operator:

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

2.  Run the following command to check whether Storage Operator is running:

    ```
    kubectl get pods -nkubes-system | grep storage-operator
    ```

    Expected output:

    ```
    NAME                        READY   STATUS    RESTARTS   AGE         IP                NODE              NOMINATED NODE   READINESS GATES
    storage-operator-***        1/1     Running   0          6d20h       192.168.XX.XX     virtual-kubelet   <none>           <none>
    ```


