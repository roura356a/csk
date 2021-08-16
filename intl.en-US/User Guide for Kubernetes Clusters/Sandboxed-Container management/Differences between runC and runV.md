---
keyword: [runC containers, runV sandboxed containers, comparison]
---

# Differences between runC and runV

This topic describes the differences between runC and Sandboxed-Container \(runV\) in terms of their performance and pod creation methods. This helps you better understand and utilize sandboxed containers.

## Comparison between runC and runV

|Item|runC|runV|
|----|----|----|
|Container engine|Docker and containerd|Containerd|
|Node type|Elastic Compute Service \(ECS\) instances and ECS bare metal instances|EBM|
|Container kernel|Shares the host kernel|Uses a dedicated kernel|
|Container isolation|Control groups \(cgroups\) and namespaces|Lightweight VMs|
|Rootfs Graph Driver|OverlayFS|OverlayFS|
|RootFS I/O throttling|Cgroups|Not supported|
|NAS mounting|Not supported|Supported|
|Disk mounting|Not supported|Supported|
|Container log collection|Logtail directly collects the container log from the node.|Logtail sidecar. For more information, see [Use CRDs to collect Kubernetes container logs in Sidecar mode](/intl.en-US/Data Collection/Logtail collection/Container log collection/Use CRDs to collect Kubernetes container logs in Sidecar mode.md).|
|Pod Overhead|N/A|-   Sandboxed-Container V1:

Memory: 512 Mi. Pod overhead refers to the amount of resources occupied by a pod sandbox. For example, if you specify `limits.memory = 512Mi` for a pod, the pod will request 1,024 MiB of memory in total.

-   Sandboxed-Container V2:

The memory of a pod overhead is calculated based on the following formula: Memory of a pod overhead = 64 MiB + Pod memory request × 2%. If the result is greater than 512 MiB, the value is set to 512 MiB. If the result is less than 64 MiB, the value is set to 64 MiB. |

## Differences in pod creation between runC and runV

You can connect to Container Service for Kubernetes \(ACK\) clusters by using the kubectl command-line tool. For more information, see [t16645.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to ACK clusters by using kubectl.md).

-   Create a pod that uses runC
    1.  Use `runtimeClassName: runc` to set the container runtime to runC.

        **Note:** The preceding configuration is optional. runC is the default container runtime.

    2.  Run the following command to create a pod that uses runC:

        ```
        cat <<EOF | kubectl create -f -
        apiVersion: v1
        kind: Pod
        metadata:
          name: busybox-runc
          labels:
            app: busybox-runc
        spec:
          containers:
          - name: busybox
            image: registry.cn-hangzhou.aliyuncs.com/acs/busybox:v1.29.2
            command:
            - tail
            - -f
            - /dev/null 
            resources:
              limits:
                cpu: 1000m
                memory: 512Mi
              requests:
                cpu: 1000m
                memory: 512Mi
        EOF
        ```

-   Create a pod that uses runV
    1.  Use `runtimeClassName: runv` to set the container runtime to runV.
    2.  Run the following command to verify that a RuntimeClass object named `runv` exists in the cluster:

        ```
        kubectl get runtimeclass runv -o yaml
        ```

        **Note:** A RuntimeClass object named `runv` is automatically created in a Kubernetes cluster that uses Sandboxed-Container.

    3.  Run the following command to create a pod that uses runV:

        ```
        cat <<EOF | kubectl create -f -
        apiVersion: v1
        kind: Pod
        metadata:
          name: busybox-runv
          labels:
            app: busybox-runv
        spec:
          runtimeClassName: runv
          nodeSelector:
            alibabacloud.com/container-runtime: Sandboxed-Container.runv
          containers:
          - name: busybox
            image: registry.cn-hangzhou.aliyuncs.com/acs/busybox:v1.29.2
            command:
            - tail
            - -f
            - /dev/null
            resources:
              limits:
                cpu: 1000m
                memory: 512Mi
              requests:
                cpu: 1000m
                memory: 512Mi
        EOF
        ```

        **Note:** If the Kubernetes version is earlier than 1.16, you must add the following nodeSelector configuration. If the Kubernetes version is 1.16 or later, the following nodeSelector configuration is not required.

        ```
        nodeSelector:
            alibabacloud.com/container-runtime: Sandboxed-Container.runv
        ```

    4.  Run the following command to query the created pod. If `runv` is included in the output, it indicates that the pod runs in a sandbox.

        ```
        kubectl get pod busybox-runv -o jsonpath={.spec.runtimeClassName}
        ```

    5.  Run the following command to log on to the pod and query its CPU and memory configurations:

        ```
        kubectl exec -ti pod busybox-runv /bin/sh
        / # cat /proc/meminfo | head -n1
        MemTotal:        1130692 kB
        / # cat /proc/cpuinfo | grep processor
        processor    : 0
        ```

        The preceding output shows that the number of CPUs is not the same as that of the host. The total memory is the sum of pod memory and pod overhead. Note that the total memory is slightly smaller because the system consumes some memory as well.


**Related topics**  


[Compatibility notes](/intl.en-US/User Guide for Kubernetes Clusters/Sandboxed-Container management/Compatibility notes.md)

[RuntimeClass](https://kubernetes.io/docs/concepts/containers/runtime-class/)

