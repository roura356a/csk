# Use a ConfigMap in a pod {#concept_e2n_vkn_vdb .concept}

You can use a ConfigMap in a pod as follows:

-   Use a ConfigMap to define environment variables for a pod.
-   Use a ConfigMap to set command-line parameters.
-   Use a ConfigMap in volumes.

For more information, see [Configure a Pod to Use a ConfigMap](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/).

## Limits {#section_gtv_cln_vdb .section}

To use a ConfigMap in a pod, make sure that the ConfigMap and the pod are in the same cluster and namespace.

## Create a ConfigMap {#section_htv_cln_vdb .section}

In this example, you create the special\_config ConfigMap with two key-value pairs `SPECIAL_LEVEL: very` and `SPECIAL_TYPE: charm`.

**Create a ConfigMap from an orchestration template**

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane under Container Service - Kubernetes, choose **Applications** \> **Deployments**. On the Deployments page that appears, click **Create from Template** in the upper-right corner.
3.  Select the cluster and namespace, select a sample template or Custom from the Sample Template drop-down list, and click **Create**.

    You can use the following YAML template to create a ConfigMap:

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


**Create a ConfigMap on the web UI**

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane under Container Service - Kubernetes, choose **Configuration** \> **ConfigMaps**.
3.  On the ConfigMaps page that appears, select the cluster and namespace, and click **Create** in the upper-right corner.
4.  Enter the ConfigMap name. Click ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16702/156903994740662_en-US.png) to add and configure key-value pairs. Then, click **OK**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16702/156903994710753_en-US.png)


## Use a ConfigMap to define environment variables for a pod {#section_x41_rnn_vdb .section}

**Use the values of a ConfigMap to define environment variables for a pod**

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane under Container Service - Kubernetes, choose **Applications** \> **Deployments**. On the Deployments page that appears, click **Create from Template** in the upper-right corner.
3.  Select the cluster and namespace, select a sample template or Custom from the Sample Template drop-down list, and click **Create**.

    You can define an environment variable for a pod by using `valueFrom` to reference the value of a key-value pair in a ConfigMap.

    The following sample template shows how to define an environment variable by referring the value of a key-value pair in a ConfigMap:

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
               valueFrom:                             ## Specifies the key-value pair in the ConfigMap referenced by env.
                 configMapKeyRef:
                   name: special-config               ## The name of the referenced ConfigMap.
                   key: SPECIAL_LEVEL                 ## The key of the referenced key-value pair.
       restartPolicy: Never
    ```


To define environment variables for a pod by using the values of multiple ConfigMaps, you only need to add multiple env parameters in the pod.

**Configure all key-value pairs of a ConfigMap as the environment variables in a pod**

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane under Container Service - Kubernetes, choose **Applications** \> **Deployments**. On the Deployments page that appears, click **Create from Template** in the upper-right corner.
3.  Select the cluster and namespace, select a sample template or Custom from the Sample Template drop-down list, and click **Create**.

    If you need to configure all key-value pairs of a ConfigMap as the environment variables in a pod, use the envFrom parameter. The keys in the ConfigMap are used as the names of the environment variables.

    The following sample template shows how to configure all key-value pairs of a ConfigMap as the environment variables in a pod:

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
           envFrom:                ## References all key-value pairs of special-config.
           - configMapRef:
               name: special-config
       restartPolicy: Never
    ```


**Use a ConfigMap to set command-line parameters**

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane under Container Service - Kubernetes, choose **Applications** \> **Deployments**. On the Deployments page that appears, click **Create from Template** in the upper-right corner.
3.  Select the cluster and namespace, select a sample template or Custom from the Sample Template drop-down list, and click **Create**.

    You can use a ConfigMap to set command-line parameters for a container by using the environment variable replacement syntax `$(VAR_NAME)`.

    The following sample template shows how to use a ConfigMap to set command-line parameters for a container.

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

    After the pod is run, the output is as follows:

    ```
    very charm
    ```


## Use a ConfigMap in volumes {#section_qtn_wkn_vdb .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane under Container Service - Kubernetes, choose **Applications** \> **Deployments**. On the Deployments page that appears, click **Create from Template** in the upper-right corner.
3.  Select the cluster and namespace, select a sample template or Custom from the Sample Template drop-down list, and click **Create**.

    A ConfigMap can be used in volumes. You can specify the name of a ConfigMap under the volumes parameter. This stores the key-value pairs of the ConfigMap to the directory specified by mountPath, which is /etc/config in this example. This generates configuration files that are named after the keys of the ConfigMap. The corresponding values of the ConfigMap are stored in these files.

    The following sample template shows how to use a ConfigMap in volumes.

    ```
    apiVersion: v1
    kind: Pod
    metadata:
       name: config-pod-4
    spec:
       containers:
         - name: test-container
           image: busybox
           command: [ "/bin/sh", "-c", "ls /etc/config/" ]   ## List the files in the directory.
           volumeMounts:
           - name: config-volume
             mountPath: /etc/config
       volumes:
         - name: config-volume
           configMap:
             name: special-config
       restartPolicy: Never
    ```

    After the pod is run, the output is as follows:

    ```
    SPECIAL_TYPE
    SPECIAL_LEVEL
    ```


