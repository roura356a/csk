# 更新Kubernetes集群即将过期的证书 {#concept_jnx_v3b_rfb .concept}

本文介绍如何更新Kubernetes集群即将过期的证书。您可以通过控制台操作，也可以通过命令行自动化一键式更新所有节点证书，也可以手动更新master和worker节点证书。

## 前提条件 {#section_d1t_x3b_rfb .section}

-   您已经成功创建一个Kubernetes集群，参见[../../../../dita-oss-bucket/SP\_235/DNcsk1877477/ZH-CN\_TP\_16639.md\#](../../../../intl.zh-CN//创建Kubernetes 集群.md#)。
-   您可以通过kubectl连接集群，参见[通过 kubectl 连接 Kubernetes 集群](../../../../intl.zh-CN//通过 kubectl 连接 Kubernetes 集群.md#)。

## 控制台更新所有节点证书 {#section_ywp_1qg_mgb .section}

在容器服务管理控制台，单击目标集群的**更新证书**更新即将过期的证书，请参考[更新即将过期证书](../../../../intl.zh-CN/Kubernetes集群用户指南/安全管理/更新即将过期证书.md#)。

## 命令行自动更新所有节点证书 {#section_sz4_lyh_rfb .section}

登录集群任意master节点，执行以下命令完成集群所有节点的证书更新。

``` {#codeblock_w3a_kjk_nix}
$ curl http://aliacs-k8s-cn-hangzhou.oss-cn-hangzhou.aliyuncs.com/public/cert-update/renew.sh | bash
```

**执行结果** 

1.  执行以下命令，查看集群master和worker节点状态。

    ``` {#codeblock_8ow_wsd_k2r}
    $ kubectl get nodes
    ```

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41643/156465994021600_zh-CN.png)

2.  执行以下命令，当master节点对应的SUCCESSFUL均为1，worker节点对应的SUCCESSFUL为集群worker节点数时，所有证书完成更新。

    ``` {#codeblock_k3j_adw_jo8}
    $ kubectl get job –nkube-system
    ```

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41643/156465994021601_zh-CN.png)


## 手动更新master节点证书 {#section_pdw_cjb_rfb .section}

1.  任意路径下，复制以下内容，创建job-master.yml文件。

    ``` {#codeblock_tt4_df6_5d3}
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

        执行以下命令：

        ``` {#codeblock_38l_59o_q3w}
        $ kubectl get nodes
        ```

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41643/156465994121548_zh-CN.png)

    -   方法二：

        1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
        2.  在Kubernetes菜单下，单击左侧导航栏的**集群** \> **集群**，进入集群列表页面。

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41643/156465994121549_zh-CN.png)

        3.  选择目标集群，单击集群名称，查看集群详细信息，单击左侧导航栏的**节点列表**获取master个数和hostname。

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41643/156465994121550_zh-CN.png)

3.  执行以下命令替换job-master.yml文件中指定的变量$\{jobname\}和$\{hostname\}。

    ``` {#codeblock_ok7_a8f_yjn}
    $ sed 's/${jobname}/cert-job-2/g; s/${hostname}/hostname/g' job-master.yml > job-master2.yml
    ```

    其中：

    -   $\{jobname\}为Job和Pod的名称，此处设置为cert-job-2。
    -   $\{hostname\}为集群master节点的名称，此处请将hostname替换为步骤2中查看到的master名称。
4.  执行以下命令创建Job。

    ``` {#codeblock_ofc_wyl_mmv}
    $ kubectl create -f job-master2.yml
    ```

5.  执行以下命令查看Job状态，当SUCCESSFUL均为1时，证书完成更新。

    ``` {#codeblock_zra_7d6_urf}
    $ kubectl get job –nkube-system
    ```

6.  重复执行步骤3~5，完成所有master节点的证书更新。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41643/156465994121551_zh-CN.png)


## 手动更新worker节点证书 {#section_k4z_skb_rfb .section}

1.  任意路径下，复制以下内容，创建job-node.yml文件。

    ``` {#codeblock_0dw_0tk_bsh}
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

    **说明：** 如果worker节点带有taint，需要在job-node.yml文件中增加对该taint的`tolerations`，即在`securityContext: {}`与`volumes:`之间增加以下内容（若有n个带有taint的worker节点，请复制n次）：

    ``` {#codeblock_phq_1dt_tai}
          tolerations:
          - effect: NoSchedule
            key: ${key}
            operator: Equal
            value: ${value}
    ```

    获取$\{name\}和$\{value\}的方法如下：

    1.  任意路径下，复制以下内容，创建taint.tml文件。

        ``` {#codeblock_byi_6yl_i3m}
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

        ``` {#codeblock_odi_9vw_zao}
        $ kubectl get nodes -o go-template-file="taint.tml"
        ```

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41643/156465994121570_zh-CN.png)

2.  执行以下命令，获取集群的CAKey。

    ``` {#codeblock_n0o_6si_6v6}
    $ sed '1d' /etc/kubernetes/pki/ca.key | base64 -w 0
    ```

3.  执行以下命令替换job-node.yml文件中指定的变量$\{jobname\}、$\{nodesize\}和$\{key\}。

    ``` {#codeblock_g4t_o99_aat}
    $ sed 's/${jobname}/cert-node-2/g; s/${nodesize}/nodesize/g; s/${key}/key/g' job-node.yml > job-node2.yml
    ```

    其中：

    -   $\{jobname\}为Job和Pod的名称，此处设置为cert-node-2。
    -   $\{nodesize\}为worker节点个数，获取方法可参考[手动更新master节点证书](#section_pdw_cjb_rfb)的步骤2。此处请将nodesize替换为集群的worker个数。
    -   $\{key\}为集群的CAKey，此处请将key替换为[手动更新worker节点证书](intl.zh-CN/最佳实践/集群/更新Kubernetes集群即将过期的证书.md#section_k4z_skb_rfb)步骤3获取到的CAKey。
4.  执行以下命令创建 Job。

    ``` {#codeblock_1qh_k1z_lb3}
    $ kubectl create –f job-node2.yml
    ```

5.  执行以下命令查看Job状态，当SUCCESSFUL为集群worker节点数时，证书完成更新。

    ``` {#codeblock_arh_s0t_72u}
    $ kubectl get job –nkube-system
    ```

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41643/156465994121565_zh-CN.png)


