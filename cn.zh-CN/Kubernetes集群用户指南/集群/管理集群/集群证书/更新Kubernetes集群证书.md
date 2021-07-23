# 更新Kubernetes集群证书

本文介绍如何更新Kubernetes集群证书。

## 前提条件

-   您已经成功创建一个Kubernetes集群，参见[t16639.md\#](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes专有版集群.md)。
-   您可以通过kubectl连接集群，参见[t16645.md\#](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl管理Kubernetes集群.md)。

## 更新master节点证书

1.  任意路径下，复制以下内容，创建job-master.yml文件。

    ```
    apiVersion: batch/v1
    kind: Job
    metadata:
      name: ${jobname}
      namespace: kube-system
    spec:
      backoffLimit: 0
      completions: 1
      parallelism: 1
      template:
        spec:
          activeDeadlineSeconds: 3600
          affinity:
            nodeAffinity:
              requiredDuringSchedulingIgnoredDuringExecution:
                nodeSelectorTerms:
                - matchExpressions:
                  - key: kubernetes.io/hostname
                    operator: In
                    values:
                    - ${hostname}
          containers:
          - command:
            - /renew/upgrade-k8s.sh
            - --role
            - master
            image: registry.cn-hangzhou.aliyuncs.com/acs/cert-rotate:v1.0.0
            imagePullPolicy: Always
            name: ${jobname}
            securityContext:
              privileged: true
            volumeMounts:
            - mountPath: /alicoud-k8s-host
              name: ${jobname}       
          hostNetwork: true
          hostPID: true
          restartPolicy: Never
          schedulerName: default-scheduler
          securityContext: {}
          tolerations:
          - effect: NoSchedule
            key: node-role.kubernetes.io/master
          volumes:
          - hostPath:
              path: /
              type: Directory
            name: ${jobname}
    ```

2.  获取集群master节点个数和hostname：
    -   方法一：

        执行以下命令获取：

        ```
        $ kubectl get nodes
        ```

        ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4575659951/p21548.png)

    -   方法二：

        1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
        2.  在Kubernetes菜单下，单击左侧导航栏的**集群**，进入集群列表页面。

            ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3740333951/p21549.png)

        3.  选择目标集群，点击集群名称，查看集群详细信息，点击左侧导航栏的**节点列表**获取master个数和hostname。

            ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3740333951/p21550.png)

3.  执行以下命令替换job-master.yml文件中指定的变量$\{jobname\}和$\{hostname\}。

    ```
    $ sed 's/${jobname}/cert-job-2/g; s/${hostname}/hostname/g' job-master.yml > job-master2.yml
    ```

    其中：

    -   $\{jobname\}为Job的名称，此处设置为cert-job-2。
    -   $\{hostname\}为集群master节点的名称，此处请将hostname替换为步骤2中查看到的master名称。
4.  执行以下命令创建Job。

    ```
    $ kubectl create -f job-master2.yml
    ```

5.  执行以下命令查看Job状态，当SUCCESSFUL均为1时，证书完成更新。

    ```
    $ kubectl get job –nkube-system
    ```

6.  重复执行步骤3~5，完成所有master节点的证书更新。

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4575659951/p21551.png)


## 更新worker节点证书

1.  任意路径下，复制以下内容，创建job-node.yml文件。

    ```
    apiVersion: batch/v1
    kind: Job
    metadata:
      name: ${jobname}
      namespace: kube-system
    spec:
      backoffLimit: 0
      completions: ${nodesize}
      parallelism: ${nodesize}
      template:
        spec:
          activeDeadlineSeconds: 3600
          affinity:
            podAntiAffinity:
              requiredDuringSchedulingIgnoredDuringExecution:
              - labelSelector:
                  matchExpressions:
                  - key: job-name
                    operator: In
                    values:
                    - ${jobname}
                topologyKey: kubernetes.io/hostname
          containers:
          - command:
            - /renew/upgrade-k8s.sh
            - --role
            - node
            - --rootkey
            - ${key}
            image: registry.cn-hangzhou.aliyuncs.com/acs/cert-rotate:v1.0.0
            imagePullPolicy: Always
            name: ${jobname}
            securityContext:
              privileged: true
            volumeMounts:
            - mountPath: /alicoud-k8s-host
              name: ${jobname}
          hostNetwork: true
          hostPID: true
          restartPolicy: Never
          schedulerName: default-scheduler
          securityContext: {}
          volumes:
          - hostPath:
              path: /
              type: Directory
            name: ${jobname}
    ```

    **说明：** 如果worker节点带有taint，需要在job-node.yml文件中增加对该taint的`tolerations`，即在`securityContext: {}`与`volumes:`之间增加以下内容（若有n个带有taint的worker节点，请复制n次以下内容）：

    ```
          tolerations:
          - effect: NoSchedule
            key: ${key}
            operator: Equal
            value: ${value}
    ```

    请参考以下内容，获取$\{name\}和$\{value\}

    1.  任意路径下，复制以下内容，创建taint.tml文件。

        ```
        {{printf "%-50s %-12s\n" "Node" "Taint"}}
        {{- range .items}}
            {{- if $taint := (index .spec "taints") }}
                {{- .metadata.name }}{{ "\t" }}
                {{- range $taint }}
                    {{- .key }}={{ .value }}:{{ .effect }}{{ "\t" }}
                {{- end }}
                {{- "\n" }}
            {{- end}}
        {{- end}}
        ```

    2.  执行以下命令，查询带有taint的worker节点的$\{name\}和$\{value\}。

        ```
        $ kubectl get nodes -o go-template-file="taint.tml"
        ```

        ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4575659951/p21570.png)

2.  执行以下命令，获取集群的CAKey。

    ```
    $ sed '1d' /etc/kubernetes/pki/ca.key | base64 -w 0
    ```

3.  执行以下命令替换job-node.yml文件中指定的变量$\{jobname\}、$\{nodesize\}和$\{key\}。

    ```
    $ sed 's/${jobname}/cert-node-2/g; s/${nodesize}/nodesize/g; s/${key}/key/g' job-node.yml > job-node2.yml
    ```

    其中：

    -   $\{jobname\}为Job的名称，此处设置为cert-node-2。
    -   $\{nodesize\}为worker节点个数，获取方法可参考[更新master节点证书](#section_pdw_cjb_rfb)的步骤2。此处请将nodesize替换为集群的worker个数。
    -   $\{key\}为集群的CAKey，此处请将key替换为步骤3获取到的CAKey。
4.  执行以下命令创建 Job。

    ```
    $ kubectl create –f job-node2.yml
    ```

5.  执行以下命令查看Job状态，当SUCCESSFUL均为1时，证书完成更新。

    ```
    $ kubectl get job –nkube-system
    ```

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4575659951/p21565.png)


