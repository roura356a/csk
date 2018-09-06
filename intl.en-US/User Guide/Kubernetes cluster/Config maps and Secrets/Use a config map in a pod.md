# Use a config map in a pod {#concept_e2n_vkn_vdb .concept}

You can use a config map in a pod in the following scenarios:

-   Use a config map to define the pod environment variables.
-   Use a config map to configure command line parameters.
-   Use a config map in data volumes.

For more information, see [Configure a pod to use a ConfigMap](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/).

## Limits {#section_gtv_cln_vdb .section}

To use a config map in a pod, make sure the config map and the pod are in the same cluster and namespace.

## Create a config map {#section_htv_cln_vdb .section}

In this example, create a config map special-config, which includes two key-value pairs: `SPECIAL_LEVEL: very` and `SPECIAL_TYPE: charm`.

**Create a config map by using an orchestration template**

1.  Log on to the [Container Service console](https://cs.console.aliyun.com).
2.  Under Kubernetes, click **Application** \> **Deployment**Click **Create by template** in the upper-right corner.
3.  Select the cluster and namespace from the Clusters and Namespace drop-down lists. Select a sample template or Custom from the Resource Type drop-down list. Click **DEPLOY**.

    You can use the following YAML sample template to create a config map.

    ```
    apiVersion: v1
    kind: ConfigMap
    metadata:
       name: special-config
       namespace: default
    data:
       SPECIAL_LEVEL: very
       SPECIAL_TYPE: charm
    ```


**Create a config map on Config Maps page**

1.  Log on to the [Container Service console](https://cs.console.aliyun.com).
2.  Under Kubernetes, click**Application** \> **Config Maps **in the left-side navigation pane.
3.  Select the cluster and namespace from the Clusters and Namespace drop-down lists. Click **Create** in the upper-right corner.
4.  Enter the Config Map Name. Enter the Variable Name and the Variable Value. Then, click **Add** on the right. Click **OK** after completing the configurations.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16702/153622168510753_en-US.png)


## Use a config map to define pod environment variables {#section_x41_rnn_vdb .section}

**Use config map data to define pod environment variables**

1.  Log on to the [Container Service console](https://cs.console.aliyun.com).
2.  Under Kubernetes, click**Application** \> **Deployment **Click **Create by template** in the upper-right corner.
3.  Select the cluster and namespace from the Clusters and Namespace drop-down lists. Select a sample template or Custom from the Resource Type drop-down list. Click **DEPLOY**. 

    You can define the environment variables in a pod. Use `valueFrom` to reference the value of SPECIAL\_LEVEL to define the pod environment variables.

    See the following orchestration example:

    ```
    apiVersion: v1
    kind: Pod
    metadata:
       name: config-pod-1
    spec:
       containers:
         - name: test-container
           image: busybox
           command: [ "/bin/sh", "-c", "env" ]
           env:
             - name: SPECIAL_LEVEL_KEY
               valueFrom:                             ##Use valueFrom to specify env to reference the value of the config map.
                 configMapKeyRef:
                   name: special-config               ##The referenced config map name.
                   key: SPECIAL_LEVEL                 ##The referenced config map key.
       restartPolicy: Never
    ```


Similarly, to define the values of multiple config maps to the environment variable values of the pod, add multiple env parameters in the pod.

**Configure all key-value pairs of a config map to pod environment variables**

1.  Log on to the [Container Service console](https://cs.console.aliyun.com).
2.  Under Kubernetes, click **Application** \> **Deployment**Click **Create by template** in the upper-right corner.
3.  Select the cluster and namespace from the Clusters and Namespace drop-down lists. Select a sample template or Custom from the Resource Type drop-down list. Click **DEPLOY**. 

    To configure all the key-value pairs of a config map to the environment variables of a pod, use the envFrom parameter. The key in a config map becomes the environment variable name in the pod.

    See the following orchestration example:

    ```
    apiVersion: v1
    kind: Pod
    metadata:
       name: config-pod-2
    spec:
       containers:
         - name: test-container
           image: busybox
           command: [ "/bin/sh", "-c", "env" ]
           envFrom:                ##Reference all the key-value pairs in the config map special-config.
           - configMapRef:
               name: special-config
       restartPolicy: Never
    ```


**Use a config map to configure command line parameters**

1.  Log on to the [Container Service console](https://cs.console.aliyun.com).
2.  Under Kubernetes, click **Application** \> **Deployment **Click **Create by template** in the upper-right corner.
3.  Select the cluster and namespace from the Clusters and Namespace drop-down lists. Select a sample template or Custom from the Resource Type drop-down list. Click **DEPLOY**. 

    You can use the config map to configure the commands or parameter values in the container by using the environment variable replacement syntax `$(VAR_NAME)`.

    See the following orchestration example:

    ```
    apiVersion: v1
    kind: Pod
    metadata:
       name: config-pod-3
    spec:
       containers:
         - name: test-container
           image: busybox
           command: [ "/bin/sh", "-c", "echo $(SPECIAL_LEVEL_KEY) $(SPECIAL_TYPE_KEY)" ]
           env:
             - name: SPECIAL_LEVEL_KEY
               valueFrom:
                 configMapKeyRef:
                   name: special-config
                   key: SPECIAL_LEVEL
             - name: SPECIAL_TYPE_KEY
               valueFrom:
                 configMapKeyRef:
                   name: special-config
                   key: SPECIAL_TYPE
       restartPolicy: Never
    ```

    The output after running the pod is as follows:

    ```
    very charm
    ```


## Use a config map in data volumes {#section_qtn_wkn_vdb .section}

1.  Log on to the [Container Service console](https://cs.console.aliyun.com).
2.  Under the Kubernetes menu, click **Application****Deployment** in the left-side navigation pane. Click **Create by template** in the upper-right corner.
3.  Select the cluster and namespace from the Clusters and Namespace drop-down lists. Select a sample template or Custom from the Resource Type drop-down list. Click **DEPLOY**.

    You can also use a config map in data volumes. Specifying the config map name under volumes stores the key-value pair data to the mountPath directory \(/etc/config in this example\). It finally generates a configuration file with key as the file name and values as the contents of the file.

    Then, the configuration file with key as the name and value as the contents is generated.

    ```
    apiVersion: v1
    kind: Pod
    metadata:
       name: config-pod-4
    spec:
       containers:
         - name: test-container
           image: busybox
           command: [ "/bin/sh", "-c", "ls /etc/config/" ]   ##List the file names under this directory.
           volumeMounts:
           - name: config-volume
             mountPath: /etc/config
       volumes:
         - name: config-volume
           configMap:
             name: special-config
       restartPolicy: Never
    ```

    Keys of the config map are output after running the pod.

    ```
    SPECIAL_TYPE
    SPECIAL_LEVEL
    ```


