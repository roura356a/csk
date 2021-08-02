---
keyword: [更新即将过期的证书, Kubernetes专有版集群]
---

# 更新Kubernetes集群即将过期的证书

本文介绍如何更新Kubernetes集群即将过期的证书。您可以通过控制台操作，也可以通过命令行自动化一键式更新所有节点证书，也可以手动更新Master和Worker节点证书。

-   已创建Kubernetes集群。具体操作，请参见[创建Kubernetes专有版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes专有版集群.md)或[创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。

    **说明：** 您无需担心托管版集群中Master节点证书过期情况，容器服务ACK会自动更新托管版集群中Master节点的证书。

-   已通过kubectl连接集群。具体操作，请参见[通过kubectl连接Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl管理Kubernetes集群.md)。

## 控制台更新所有节点证书

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  单击证书即将过期集群右侧的**更新证书**，进入更新证书页面。

    **说明：** 如果集群证书即将在两个月左右过期，会出现更新证书。

    ![更新证书](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/7944806261/p37315.png)

4.  在**更新证书**页面单击**更新证书**。

5.  在**提示**页面单击**确定**。

    成功更新集群证书后，您可以看到以下内容：

    -   在更新证书页面，显示**更新成功**。
    -   在集群列表页面，目标集群无**更新证书**提示。

## 命令行自动更新所有节点证书

在集群任意Master节点，执行以下命令完成集群所有节点的证书更新。

```
curl http://aliacs-k8s-cn-hangzhou.oss-cn-hangzhou.aliyuncs.com/public/cert-update/renew.sh | bash
```

结果验证：

1.  执行以下命令，查看集群Master和Worker节点状态。

    ```
    kubectl get nodes
    ```

    ![nodes](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/3575659951/p21600.png)

2.  执行以下命令，当Master节点对应的COMPLETIONS均为1，Worker节点对应的COMPLETIONS为集群Worker节点数时，所有证书完成更新。

    ```
    kubectl get job –nkube-system
    ```

    ![nodes](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/5754887261/p21601.png)


## 手动更新Master节点证书

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

2.  获取集群Master节点个数和hostname：

    -   方法一：通过命令行

        执行以下命令：

        ```
        kubectl get nodes
        ```

        ![nodes](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/4575659951/p21548.png)

    -   方法二：通过控制台

        1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
        2.  在控制台左侧导航栏中，单击**集群**。
        3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。
        4.  在集群管理页的左侧导航栏中，选择**节点管理** \> **节点**获取Master个数和和对应的名称、IP地址、实例ID。
3.  执行以下命令替换job-master.yml文件中指定的变量$\{jobname\}和$\{hostname\}。

    ```
    sed 's/${jobname}/cert-job-2/g; s/${hostname}/hostname/g' job-master.yml > job-master2.yml
    ```

    其中：

    -   $\{jobname\}为Job的名称，此处设置为cert-job-2。
    -   $\{hostname\}为集群Master节点的名称，此处请将hostname替换为步骤2中查看到的Master名称。
4.  执行以下命令创建Job。

    ```
    kubectl create -f job-master2.yml
    ```

5.  执行以下命令查看Job状态，当COMPLETIONS均为1时，证书完成更新。

    ```
    kubectl get job –nkube-system
    ```

6.  重复执行步骤3~5，完成所有Master节点的证书更新。

    ![](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/6590987261/p21551.png)


## 手动更新Worker节点证书

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

    **说明：** 如果Worker节点带有Taint，需要在job-node.yml文件中增加对该Taint的`tolerations`，即在`securityContext: {}`与`volumes:`之间增加以下内容（若有n个带有Taint的Worker节点，请复制n次）：

    ```
          tolerations:
          - effect: NoSchedule
            key: ${key}
            operator: Equal
            value: ${value}
    ```

    获取$\{name\}和$\{value\}的方法如下：

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

    2.  执行以下命令，查询带有Taint的Worker节点的$\{name\}和$\{value\}。

        ```
        kubectl get nodes -o go-template-file="taint.tml"
        ```

        ![](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/4575659951/p21570.png)

2.  执行以下命令，获取集群的CAKey。

    ```
    sed '1d' /etc/kubernetes/pki/ca.key | base64 -w 0
    ```

3.  执行以下命令替换job-node.yml文件中指定的变量$\{jobname\}、$\{nodesize\}和$\{key\}。

    ```
    sed 's/${jobname}/cert-node-2/g; s/${nodesize}/nodesize/g; s/${key}/key/g' job-node.yml > job-node2.yml
    ```

    其中：

    -   $\{jobname\}为Job的名称，此处设置为cert-node-2。
    -   $\{nodesize\}为Worker节点个数，获取方法可参见[手动更新Worker节点证书](#section_f3i_kk1_7dz)的步骤1。此处请将nodesize替换为集群的Worker个数。
    -   $\{key\}为集群的CAKey，此处请将key替换为[手动更新Worker节点证书](#section_f3i_kk1_7dz)步骤2获取到的CAKey。
4.  执行以下命令创建Job。

    ```
    kubectl create –f job-node2.yml
    ```

5.  执行以下命令查看Job状态，当COMPLETIONS为集群Worker节点数时，证书完成更新。

    ```
    kubectl get job –nkube-system
    ```

    ![nodes](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/6590987261/p21565.png)


