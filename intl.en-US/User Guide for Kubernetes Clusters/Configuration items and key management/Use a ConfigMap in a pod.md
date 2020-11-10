# Use a ConfigMap in a pod

This topic describes how to use a ConfigMap in a pod.

You can use a ConfigMap in a pod in the following scenarios:

-   Use a ConfigMap to define environment variables for a pod.
-   Use a ConfigMap to set command line parameters
-   Use a ConfigMap in a volume.

For more information, see [Configure a pod to use a ConfigMap](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/).

## Limits

To use a ConfigMap in a pod, make sure that the ConfigMap and pod are in the same cluster and namespace.

## Create a ConfigMap

In this example, a ConfigMap named special\_config is created. This ConfigMap consists of two key-value pairs: `SPECIAL_LEVEL: very` and `SPECIAL_TYPE: charm`.

The following YAML template shows how to create the ConfigMap:

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

## Use a ConfigMap to define environment variables for a pod

-   Use the key-value pairs of a ConfigMap to define environment variables for a pod
    1.  Log on to the [ACK console](https://cs.console.aliyun.com).
    2.  In the left-side navigation pane, click **Clusters**.
    3.  On the Clusters page, find the cluster that you want to manage and click **Details** in the **Actions** column.
    4.  In the left-side navigation pane, click **Workload**.
    5.  In the upper-right corner of the **Deployments** tab, click **Create from Template**.
    6.  Select the sample template or enter a custom template, and click **Create**.

        To define an environment variable for a pod, you can use the `valueFrom` field to reference the value of SPECIAL\_LEVEL.

        The following template is an example:

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
                   valueFrom:                             ## Use valueFrom to reference the value of a ConfigMap. configMapKeyRef:
                       name: special-config               ## The value of special-config is referenced.                 ## The name of the ConfigMap that is referenced.
           restartPolicy: Never
        ```

-   Use all key-value pairs of a ConfigMap to define multiple environment variables for a pod
    1.  In the left-side navigation pane, click **Clusters**.
    2.  On the Clusters page, select the cluster that you want to manage and click **Details** in the **Actions** column.
    3.  In the left-side navigation pane, click **Workload**.
    4.  In the upper-right corner of the **Deployments** tab, click **Create from Template**.
    5.  Select the sample template or enter a custom template, and click **Create**.

        To use all key-value pairs of a ConfigMap to define multiple environment variables for a pod, you can use the envFrom parameter. The keys of the ConfigMap are used as the names of the environment variables.

        The following template is an example:

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
               envFrom:                ## All key-value pairs of the special-config ConfigMap are referenced.
               - configMapRef:
                   name: special-config
           restartPolicy: Never
        ```

-   Use a ConfigMap to set command line parameters
    1.  In the left-side navigation pane, click **Clusters**.
    2.  On the Clusters page, find the cluster that you want to manage and click **Details** in the **Actions** column.
    3.  In the left-side navigation pane, click **Workload**.
    4.  In the upper-right corner of the **Deployments** tab, click **Create from Template**.
    5.  Select the sample template or enter a custom template, and click **Create**.

        You can use ConfigMaps to define the commands or parameter values for a container by using the environment variable replacement syntax `$(VAR_NAME)`.

        The following template is an example:

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


## Use a ConfigMap in a volume

1.  In the left-side navigation pane, click **Clusters**.

2.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

3.  In the left-side navigation pane, click **Workload**.

4.  In the upper-right corner of the **Deployments** tab, click **Create from Template**.

5.  Select the sample template or enter a custom template, and click **Create**.

    To use a ConfigMap in a volume, specify the name of the ConfigMap in the volumes section. This saves the key-value pairs of the ConfigMap to the directory specified by mountPath, which is /etc/config in this example. Configuration files that are named after the keys of the key-value pairs of the ConfigMap are generated. The values of the key-value pairs are stored in the related files.

    The following template is an example:

    ```
    apiVersion: v1
    kind: Pod
    metadata:
       name: config-pod-4
    spec:
       containers:
         - name: test-container
           image: busybox
           command: [ "/bin/sh", "-c", "ls /etc/config/" ]   ## Lists the files under the directory.
           - name: config-volume
             mountPath: /etc/config
       volumes:
         - name: config-volume
           configMap:
             name: special-config
       restartPolicy: Never
    ```

    After you run the pod, the following output is returned:

    ```
    SPECIAL_TYPE
    SPECIAL_LEVEL
    ```


