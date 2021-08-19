---
keyword: [runC容器, 安全沙箱（runV）容器, 对比]
---

# runC容器和安全沙箱（runV）容器的区别

本文通过对比runC容器和安全沙箱（runV）容器的性能和创建方式，让您了解安全沙箱容器的优势以便更好地使用安全沙箱容器。

## runC容器和安全沙箱（runV）容器方案对比

|配置|runC容器|安全沙箱（runV）容器|
|--|------|------------|
|容器引擎|Docker、Containerd|Containerd|
|容器所在节点型号|ECS、EBM|EBM|
|容器内核|与宿主机共享内核|独占内核|
|容器隔离方式|Cgroups和Namespace|轻量虚拟机|
|Rootfs Graph Driver|OverlayFS|OverlayFS|
|Rootfs IO限速|Cgroups|否|
|NAS直挂|否|是|
|云盘直挂|否|是|
|容器日志文件采集|logtail可直接从节点侧采集容器内的日志。|logtail sidecar，详情请参见[通过Sidecar-CRD方式采集容器日志](/intl.zh-CN/数据采集/Logtail采集/采集容器日志/通过Sidecar-CRD方式采集容器日志.md)。|
|Pod Overhead|无|Memory：M=64 Mi+Pod内存申请 \*2%。当M\>512，M最终取值为512 Mi。当M<64，M最终取值为64。|

## runC Pod和安全沙箱（runV）Pod的创建方式对比

您可以通过kubectl命令行工具连接容器服务Kubernetes版（ACK）集群，请参见[t16645.md\#](/intl.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl工具连接集群.md)。

-   创建runC Pod
    1.  通过`runtimeClassName: runc`指定使用runC容器运行时。

        **说明：** 您也可以不配置，Pod会用默认的容器运行时runC。

    2.  执行以下命令创建runC Pod。

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

-   创建安全沙箱容器（runV）
    1.  通过`runtimeClassName: runv`指定使用安全沙箱容器运行时。
    2.  执行以下命令查看集群中已经存在的`runv`这个RuntimeClass对象。

        ```
        kubectl get runtimeclass runv -o yaml
        ```

        **说明：** 安全沙箱容器集群中默认创建了RuntimeClass对象`runv`。

    3.  执行以下命令创建安全沙箱Pod。

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

        **说明：** 如果您的Kubernetes版本小于1.16（不包括1.16），需添加以下nodeSelector配置；如果您的Kubernetes版本在1.16（包括1.16）以上则不需要添加。

        ```
        nodeSelector:
            alibabacloud.com/container-runtime: Sandboxed-Container.runv
        ```

    4.  执行以下命令查看创建的Pod。如果输出结果为`runv`，则表示Pod已经运行在安全沙箱内了。

        ```
        kubectl get pod busybox-runv -o jsonpath={.spec.runtimeClassName}
        ```

    5.  执行以下命令登录容器查看CPU和内存规格。

        ```
        kubectl exec -ti pod busybox-runv /bin/sh
        / # cat /proc/meminfo | head -n1
        MemTotal:        1130692 kB
        / # cat /proc/cpuinfo | grep processor
        processor    : 0
        ```

        通过以上命令返回的结果，可以看出CPU核数并非宿主机CPU核数，内存总大小为Pod内存与Overhead内存之和（系统会消耗一些内存，所以看到的内存会少一些）。


**相关文档**  


[安全沙箱兼容性说明](/intl.zh-CN/Kubernetes集群用户指南/安全沙箱/安全沙箱兼容性说明.md)

[RuntimeClass](https://kubernetes.io/docs/concepts/containers/runtime-class/)

