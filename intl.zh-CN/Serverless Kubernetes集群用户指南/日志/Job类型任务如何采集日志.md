---
keyword: [采集日志, Job类型任务]
---

# Job类型任务如何采集日志

本文将为您介绍如何针对Job类型任务场景采集日志到日志系统。

-   已创建了一个ASK集群。更多信息，请参见[ASK使用快速入门](/intl.zh-CN/Serverless Kubernetes集群用户指南/集群/创建Serverless Kubernetes集群.md)。
-   已在该集群上部署了虚拟节点。更多信息，请参见[通过部署ACK虚拟节点组件创建ECI Pod](/intl.zh-CN/Kubernetes集群用户指南/弹性容器实例ECI/通过部署ACK虚拟节点组件创建ECI Pod.md)。
-   已创建NAS文件系统并添加挂载点。更多信息，请参见[创建文件系统]()和[管理挂载点]()。

**说明：** 如果您使用了阿里云日志服务，Job任务挂载volume收集日志通过配置环境变量，可以直接同步阿里云日志服务。详情请参见[ECI中日志采集的自定义配置]()。

## 操作步骤

**说明：**

在ECS模式下Job任务可以通过DeamonSet方式采集标准输出，但是在ECI模式下不支持DeamonSet。当Job任务结束后，Pod会立即退出，此时日志可能还未被收集完成，针对这种情况我们可以采用如下方式解决：

Job类任务挂载NAS盘，把输出的日志存储在NAS盘，再通过另一个同样挂载NAS盘的Pod来采集Job任务标准输出到日志系统中。

1.  通过kubectl客户端创建job.yaml

    一个计算π值Job任务：

    ```
    apiVersion: batch/v1
    kind: Job
    metadata:
      name: pi
    spec:
      template:
        spec:
          containers:
          - name: pi
            image: resouer/ubuntu-bc 
            command: ["sh", "-c", "echo 'scale=1000; 4*a(1)' | bc -l > /eci/a.log 2>&1"] #运行输出结果重定向到指定文件
            volumeMounts:
            - name: log-volume
              mountPath: /eci
              readOnly: false
          restartPolicy: Never
          volumes:
          - name: log-volume
            nfs:
                path: /eci
                server: 04edd48c7c-****.cn-hangzhou.nas.aliyuncs.com
                readOnly: false
      backoffLimit: 4
    ```

2.  部署一个Job任务到虚拟节点。

    **说明：** 关于如何部署虚拟节点请参见[通过部署ACK虚拟节点组件创建ECI Pod](/intl.zh-CN/Kubernetes集群用户指南/弹性容器实例ECI/通过部署ACK虚拟节点组件创建ECI Pod.md)。

    ```
    kubectl apply -f job.yaml -n fvt-eci
    ```

3.  查看Pod状态。

    ```
    kubectl get pod -n fvt-eci
    ```

4.  通过kubectl客户端创建log-collection.yaml文件，并拷贝以下内容到该文件。然后执行指令创建一个Pod，挂载NAS盘用来采集Job任务输出日志。

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: log-collection
    spec:
      containers:
      - image: nginx:latest
        name: log-collection
        command: ['/bin/sh', '-c', 'echo &(cat /eci/a.log)'] #查看Job日志文件
        volumeMounts:
        - mountPath: /eci
          name: log-volume
      restartPolicy: Never
      volumes:
      - name: log-volume
        nfs:
          server: 04edd48c7c-****.cn-hangzhou.nas.aliyuncs.com
          path: /eci
          readOnly: false
    ```


