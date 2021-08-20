---
keyword: [Windows节点, Logtail, 应用日志信息采集]
---

# 使用Logtail收集Windows容器日志

您可以通过使用Logtail采集Windows容器上的应用信息。本文介绍如何使用Logtail收集Windows容器日志。

-   已创建Kubernetes集群，并且在创建集群时选中**使用日志服务**。更多信息，请参见[创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。
-   已创建Windows节点池。更多信息，请参见[创建Windows节点池](/intl.zh-CN/Kubernetes集群用户指南/Windows容器/创建Windows节点池.md)。
-   已通过kubectl连接ACK集群。具体操作，请参见[步骤二：选择集群凭证类型](/intl.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl工具连接集群.md)。

## 使用限制

目前Logtail仅限于支持采集Stdout的输出至日志服务，后续会支持对日志文件的采集。

## 为Windows节点安装Logtail v1.0.x

执行以下YAML模板将Logtail以Daemonset方式部署至Windows节点上。

```
apiVersion: apps/v1
kind: DaemonSet
metadata:
  labels:
    k8s-app: logtail-ds-windows
  name: logtail-ds-windows
  namespace: kube-system
spec:
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      app: logtail-ds-windows
  template:
    metadata:
      annotations:
        scheduler.alpha.kubernetes.io/critical-pod: ""
      labels:
        app: logtail-ds-windows
    spec:
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: type
                operator: NotIn
                values:
                - virtual-kubelet
              - key: beta.kubernetes.io/os
                operator: In
                values:
                - windows
              - key: windows.alibabacloud.com/deployment-topology
                operator: DoesNotExist
            - matchExpressions:
              - key: type
                operator: NotIn
                values:
                - virtual-kubelet
              - key: kubernetes.io/os
                operator: In
                values:
                - windows
              - key: windows.alibabacloud.com/deployment-topology
                operator: DoesNotExist
      containers:
      - name: logtail
        command:
        - powershell.exe
        - -NoLogo
        - -NonInteractive
        - -File
        - entrypoint.ps1
        env:
        # --- configure the logtail file configuration generation --- #
        ## "ALICLOUD_LOGTAIL_CONFIG_PATH" specifies the configuration path of logtail,
        ## if the path is blank, the configuration will generate
        ## via "ALICLOUD_LOGTAIL_CONFIG_ITEM__" prefix environment variables.
        ## "ALICLOUD_LOGTAIL_CONFIG_ITEM__" needs to be combined with a type indicator to
        ## display the type of the configuration item, the following indicators are optional.
        ## -       INT__ : 64-bit signed integer.
        ## -      UINT__ : 64-bit unsigned integer.
        ## -      DOUB__ : 64-bit floating-point number.
        ## -      BOOL__ : boolean.
        ## -       STR__ : string, default type.
        ## - {<TYPE>}S__ : array in <TYPE> with vertical bar separated format.
        ## P.S: don't treate "ALICLOUD_LOGTAIL_CONFIG_ITEM__" as a silver bullet,
        ## when the configuration is too complicated,
        ## please mount a detailed configuration file on "ALICLOUD_LOGTAIL_CONFIG_PATH".
        - name: ALICLOUD_LOGTAIL_CONFIG_PATH
          valueFrom:
            configMapKeyRef:
              key: log-config-path
              name: alibaba-log-configuration
        - name: ALICLOUD_LOGTAIL_CONFIG_ITEM__DOUB__CPU_USAGE_LIMIT
          valueFrom:
            configMapKeyRef:
              key: cpu-core-limit
              name: alibaba-log-configuration
        - name: ALICLOUD_LOGTAIL_CONFIG_ITEM__UINT__MEM_USAGE_LIMIT
          valueFrom:
            configMapKeyRef:
              key: mem-limit
              name: alibaba-log-configuration
        - name: ALICLOUD_LOGTAIL_CONFIG_ITEM__UINT__MAX_BYTES_PER_SEC
          valueFrom:
            configMapKeyRef:
              key: max-bytes-per-sec
              name: alibaba-log-configuration
        - name: ALICLOUD_LOGTAIL_CONFIG_ITEM__UINT__SEND_REQUESTS_CONCURRENCY
          valueFrom:
            configMapKeyRef:
              key: send-requests-concurrency
              name: alibaba-log-configuration
        # --- configure the logtail configuration --- #
        ## "ALICLOUD_LOG_REGION" specifies the region of the Alibaba Cloud,
        ## it will discover the region automatically if blank.
        ## "ALICLOUD_LOG_USER_ID" is the same as "ALIYUN_LOGTAIL_USER_ID",
        ## which is to specify the uid of Alibaba Cloud SLS service.
        ## "ALICLOUD_LOG_PROJECT" is the same as "ALICLOUD_LOG_DEFAULT_PROJECT",
        ## which is to specify the project of Alibaba Cloud SLS service.
        ## "ALICLOUD_LOG_MACHINE_GROUP" is the same as "ALIYUN_LOGTAIL_USER_DEFINED_ID"
        ## and "ALICLOUD_LOG_DEFAULT_MACHINE_GROUP",
        ## which is to specify the machine group of Alibaba Cloud SLS service.
        ## "ALICLOUD_LOG_ENDPOINT" specifies the endpoint of the Alibaba Cloud SLS service.
        ## "ALICLOUD_LOG_ECS_FLAG" specifies whether to log the ECS flags.
        ## "ALICLOUD_LOG_DOCKER_ENV_CONFIG"
        ## "ALICLOUD_LOG_ENV_TAGS" is the same as "ALIYUN_LOG_ENV_TAGS",
        ## which is to specify the environment variables to be recorded,
        ## it is in form of vertical bar separated list.
        - name: ALICLOUD_LOG_REGION
          value: ""
        - name: ALICLOUD_LOG_USER_ID
          valueFrom:
            configMapKeyRef:
              key: log-ali-uid
              name: alibaba-log-configuration
        - name: ALICLOUD_LOG_PROJECT
          valueFrom:
            configMapKeyRef:
              key: log-project
              name: alibaba-log-configuration
        - name: ALICLOUD_LOG_MACHINE_GROUP
          valueFrom:
            configMapKeyRef:
              key: log-machine-group
              name: alibaba-log-configuration
        - name: ALICLOUD_LOG_ENDPOINT
          valueFrom:
            configMapKeyRef:
              key: log-endpoint
              name: alibaba-log-configuration
        - name: ALICLOUD_LOG_ECS_FLAG
          value: "true"
        - name: ALICLOUD_LOG_DOCKER_ENV_CONFIG
          value: "true"
        - name: ALICLOUD_LOG_ENV_TAGS
          value: _node_name_|_node_ip_|working_ip
        - name: _node_name_
          valueFrom:
            fieldRef:
              apiVersion: v1
              fieldPath: spec.nodeName
        - name: working_ip
          valueFrom:
            fieldRef:
              apiVersion: v1
              fieldPath: status.hostIP
        - name: _node_ip_
          valueFrom:
            fieldRef:
              apiVersion: v1
              fieldPath: status.hostIP
        # 根据不同集群的地域，您需修改以下镜像地址中的地域<cn-hangzhou>信息。
        image: registry-vpc.cn-hangzhou.aliyuncs.com/acs/logtail-windows:v1.0.20
        imagePullPolicy: IfNotPresent
        resources:
          limits:
            cpu: 1
            memory: 1Gi
          requests:
            cpu: 100m
            memory: 256Mi
        securityContext:
          privileged: false
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
        volumeMounts:
        - name: docker-pipe
          mountPath: \\.\pipe\docker_engine
        - name: docker-data
          mountPath: c:/ProgramData/docker
          readOnly: true
        - mountPath: c:/logtail_host
          name: root
          readOnly: true
      terminationGracePeriodSeconds: 30
      priorityClassName: system-node-critical
      restartPolicy: Always
      terminationGracePeriodSeconds: 30
      tolerations:
      - operator: Exists
      volumes:
      - name: docker-pipe
        hostPath:
          path: \\.\pipe\docker_engine
      - name: docker-data
        hostPath:
          path: c:/ProgramData/docker
          type: Directory
      - name: root
        hostPath:
          path: c:/
          type: Directory
  updateStrategy:
    rollingUpdate:
      maxUnavailable: 10%
    type: RollingUpdate
```

## 为Windows节点安装Logtail v1.1.x

**说明：** Logtail v1.1.x依赖Windows工作节点上的特权托管进程，仅适用于集群内节点池默认含有`windows.alibabacloud.com/deployment-topology=2.0`标签的Windows工作节点。

使用以下YAML模板将Logtail以Daemonset方式部署至Windows节点上。

```
apiVersion: apps/v1
kind: DaemonSet
metadata:
  labels:
    k8s-app: logtail-ds-windows
  name: logtail-ds-windows
  namespace: kube-system
spec:
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      app: logtail-ds-windows
  template:
    metadata:
      annotations:
        scheduler.alpha.kubernetes.io/critical-pod: ""
      labels:
        app: logtail-ds-windows
    spec:
      #since 1.18, we can specify `hostNetwork: true` for Windows workloads, so we can deploy an application without NetworkReady.
      hostNetwork: true
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: type
                operator: NotIn
                values:
                - virtual-kubelet
              - key: beta.kubernetes.io/os
                operator: In
                values:
                - windows
              - key: windows.alibabacloud.com/deployment-topology
                operator: In
                values:
                - "2.0"
            - matchExpressions:
              - key: type
                operator: NotIn
                values:
                - virtual-kubelet
              - key: kubernetes.io/os
                operator: In
                values:
                - windows
              - key: windows.alibabacloud.com/deployment-topology
                operator: In
                values:
                - "2.0"
      containers:
      - name: logtail
        # use the below volume configuration since v1.1+.
        command:
        - pwsh.exe
        - -NoLogo
        - -NonInteractive
        - -File
        - entrypoint.ps1
        env:
        # --- configure the logtail file configuration generation --- #
        ## "ALICLOUD_LOGTAIL_CONFIG_PATH" specifies the configuration path of logtail,
        ## if the path is blank, the configuration will generate
        ## via "ALICLOUD_LOGTAIL_CONFIG_ITEM__" prefix environment variables.
        ## "ALICLOUD_LOGTAIL_CONFIG_ITEM__" needs to be combined with a type indicator to
        ## display the type of the configuration item, the following indicators are optional.
        ## -       INT__ : 64-bit signed integer.
        ## -      UINT__ : 64-bit unsigned integer.
        ## -      DOUB__ : 64-bit floating-point number.
        ## -      BOOL__ : boolean.
        ## -       STR__ : string, default type.
        ## - {<TYPE>}S__ : array in <TYPE> with vertical bar separated format.
        ## P.S: don't treate "ALICLOUD_LOGTAIL_CONFIG_ITEM__" as a silver bullet,
        ## when the configuration is too complicated,
        ## please mount a detailed configuration file on "ALICLOUD_LOGTAIL_CONFIG_PATH".
        - name: ALICLOUD_LOGTAIL_CONFIG_PATH
          valueFrom:
            configMapKeyRef:
              key: log-config-path
              name: alibaba-log-configuration
        - name: ALICLOUD_LOGTAIL_CONFIG_ITEM__DOUB__CPU_USAGE_LIMIT
          valueFrom:
            configMapKeyRef:
              key: cpu-core-limit
              name: alibaba-log-configuration
        - name: ALICLOUD_LOGTAIL_CONFIG_ITEM__UINT__MEM_USAGE_LIMIT
          valueFrom:
            configMapKeyRef:
              key: mem-limit
              name: alibaba-log-configuration
        - name: ALICLOUD_LOGTAIL_CONFIG_ITEM__UINT__MAX_BYTES_PER_SEC
          valueFrom:
            configMapKeyRef:
              key: max-bytes-per-sec
              name: alibaba-log-configuration
        - name: ALICLOUD_LOGTAIL_CONFIG_ITEM__UINT__SEND_REQUESTS_CONCURRENCY
          valueFrom:
            configMapKeyRef:
              key: send-requests-concurrency
              name: alibaba-log-configuration
        # --- configure the logtail configuration --- #
        ## "ALICLOUD_LOG_REGION" specifies the region of the Alibaba Cloud,
        ## it will discover the region automatically if blank.
        ## "ALICLOUD_LOG_USER_ID" is the same as "ALIYUN_LOGTAIL_USER_ID",
        ## which is to specify the uid of Alibaba Cloud SLS service.
        ## "ALICLOUD_LOG_PROJECT" is the same as "ALICLOUD_LOG_DEFAULT_PROJECT",
        ## which is to specify the project of Alibaba Cloud SLS service.
        ## "ALICLOUD_LOG_MACHINE_GROUP" is the same as "ALIYUN_LOGTAIL_USER_DEFINED_ID"
        ## and "ALICLOUD_LOG_DEFAULT_MACHINE_GROUP",
        ## which is to specify the machine group of Alibaba Cloud SLS service.
        ## "ALICLOUD_LOG_ENDPOINT" specifies the endpoint of the Alibaba Cloud SLS service.
        ## "ALICLOUD_LOG_ECS_FLAG" specifies whether to log the ECS flags.
        ## "ALICLOUD_LOG_DOCKER_ENV_CONFIG"
        ## "ALICLOUD_LOG_ENV_TAGS" is the same as "ALIYUN_LOG_ENV_TAGS",
        ## which is to specify the environment variables to be recorded,
        ## it is in form of vertical bar separated list.
        - name: ALICLOUD_LOG_REGION
          value: ""
        - name: ALICLOUD_LOG_USER_ID
          valueFrom:
            configMapKeyRef:
              key: log-ali-uid
              name: alibaba-log-configuration
        - name: ALICLOUD_LOG_PROJECT
          valueFrom:
            configMapKeyRef:
              key: log-project
              name: alibaba-log-configuration
        - name: ALICLOUD_LOG_MACHINE_GROUP
          valueFrom:
            configMapKeyRef:
              key: log-machine-group
              name: alibaba-log-configuration
        - name: ALICLOUD_LOG_ENDPOINT
          valueFrom:
            configMapKeyRef:
              key: log-endpoint
              name: alibaba-log-configuration
        - name: ALICLOUD_LOG_ECS_FLAG
          value: "true"
        - name: ALICLOUD_LOG_DOCKER_ENV_CONFIG
          value: "true"
        - name: ALICLOUD_LOG_ENV_TAGS
          value: _node_name_|_node_ip_
        - name: _node_name_
          valueFrom:
            fieldRef:
              apiVersion: v1
              fieldPath: spec.nodeName
        - name: _node_ip_
          valueFrom:
            fieldRef:
              apiVersion: v1
              fieldPath: status.hostIP
        - name: working_ip
          valueFrom:
            fieldRef:
              apiVersion: v1
              fieldPath: status.hostIP
        # 根据不同集群的地域，您需修改以下镜像地址中的地域<cn-hangzhou>信息。
        image: registry-vpc.cn-hangzhou.aliyuncs.com/acs/logtail-windows:v1.1.18-aliyun.1
        imagePullPolicy: IfNotPresent
        resources:
          limits:
            cpu: 1
            memory: 1Gi
          requests:
            cpu: 100m
            memory: 256Mi
        securityContext:
          privileged: false
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
        volumeMounts:
        - name: wins-pipe
          mountPath: \\.\pipe\rancher_engine
        - name: host-binary
          mountPath: c:/host/opt/ilogtail/bin
      terminationGracePeriodSeconds: 30
      priorityClassName: system-node-critical
      restartPolicy: Always
      tolerations:
      - operator: Exists
      volumes:
      - name: wins-pipe
        hostPath:
          path: \\.\pipe\rancher_wins
      - name: host-binary
        hostPath:
          path: c:/opt/ilogtail/bin
          type: DirectoryOrCreate
  updateStrategy:
    type: RollingUpdate
```

## 部署添加Logtail的应用

在Windows节点上添加Logtail后，使用以下应用模板检验添加的Logtail。

```
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: logtail-test
  name: logtail-test
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: logtail-test
      name: logtail-test
    spec:
      containers:
      - name: nanoserver
        image: mcr.microsoft.com/windows/servercore:1809
        command: ["powershell.exe"]
        args: ["ping -t 127.0.0.1 -w 10000"]
        env:
      ######### 配置环境变量 ###########
        - name: aliyun_logs_logtail-stdout
          value: stdout
        - name: aliyun_logs_logttail-tags
          value: tag1=v1
      #################################
      nodeSelector:
        beta.kubernetes.io/os: windows
      tolerations:
      - effect: NoSchedule
        key: os
        operator: Equal
        value: windows
```

成功部署示例应用后，您可查看日志信息。具体操作，请参见[查询和分析日志](/intl.zh-CN/查询与分析/查询和分析日志.md)。

