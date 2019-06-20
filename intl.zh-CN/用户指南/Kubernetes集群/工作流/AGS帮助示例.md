# AGS帮助示例 {#concept_525794 .concept}

## 前提条件 {#section_urd_o09_7p3 .section}

-   您已经成功创建一个 Kubernetes 集群，参见[创建Kubernetes集群](intl.zh-CN/用户指南/Kubernetes集群/集群管理/创建Kubernetes集群.md#)。
-   您已连接到Kubernetes集群的Master节点，参见[通过 kubectl 连接 Kubernetes 集群](intl.zh-CN/用户指南/Kubernetes集群/集群管理/通过 kubectl 连接 Kubernetes 集群.md#)。

## Log {#section_wvg_uvb_jfm .section}

1.  执行`ags config sls`命令，在AGS上配置并安装日志服务。

    原生Argo查看 Pod 日志只能从本地拉取，当Pod或者所在节点被删除后，日志也会随之丢失，这对查看错误分析以及原因等带来很大问题。 我们将日志上传到阿里云日志服务，即使节点消失也会从 日志服务重新拉取，将日志持久化。

2.  执行`ags logs`命令，查看工作流的日志。

    本例中执行`ags logs POD/WORKFLOW`，查看Pod/Workflow 的日志。

    ``` {#codeblock_tu3_ddw_rtr}
    [root@iZwz92q9h36kv8posr0i6uZ ~]# ags logs
    view logs of a workflow
    
    Usage:
     ags logs POD/WORKFLOW [flags]
    
    Flags:
     -c, --container string    Print the logs of this container (default "main")
     -f, --follow              Specify if the logs should be streamed.
     -h, --help                help for logs
     -l, --recent-line int     how many lines to show in one call (default 100)
         --since string        Only return logs newer than a relative duration like 5s, 2m, or 3h. Defaults to all logs. Only one of since-time / since may be used.
         --since-time string   Only return logs after a specific date (RFC3339). Defaults to all logs. Only one of since-time / since may be used.
         --tail int            Lines of recent log file to display. Defaults to -1 with no selector, showing all log lines otherwise 10, if a selector is provided. (default -1)
         --timestamps          Include timestamps on each line in the log output
     -w, --workflow            Specify that whole workflow logs should be printed
    ```

    **说明：** 

    -   如果Pod存在本机，AGS 会从本地将Pod日志查询出来，所有flag兼容原argo命令。
    -   如果Pod已经被删除，AGS会从阿里云日志服务来查询日志，默认返回最近100条日志，可以通过-l flag来指定到底返回多少条日志。

## 集成kubectl 命令 {#section_y54_tss_1h8 .section}

您可以执行如下命令，查看 Pod状态以及其他需要使用kubectl命令的情况。

``` {#codeblock_amp_uky_00o}
[root@iZwz92q9h36kv8posr0i6uZ ~]# ags get test-v2
Name:                test-v2
Namespace:           default
ServiceAccount:      default
Status:              Running
Created:             Thu Nov 22 11:06:52 +0800 (2 minutes ago)
Started:             Thu Nov 22 11:06:52 +0800 (2 minutes ago)
Duration:            2 minutes 46 seconds

STEP           PODNAME             DURATION  MESSAGE
● test-v2
└---● bcl2fq  test-v2-2716811808  2m

[root@iZwz92q9h36kv8posr0i6uZ ~]# ags kubectl describe pod test-v2-2716811808
Name:               test-v2-2716811808
Namespace:          default
Priority:           0
PriorityClassName:  <none>
Node:               cn-shenzhen.i-wz9gwobtqrbjgfnqxl1k/192.168.0.94
Start Time:         Thu, 22 Nov 2018 11:06:52 +0800
Labels:             workflows.argoproj.io/completed=false
                   workflows.argoproj.io/workflow=test-v2
Annotations:        workflows.argoproj.io/node-name=test-v2[0].bcl2fq
                   workflows.argoproj.io/template={"name":"bcl2fq","inputs":{},"outputs":{},"metadata":{},"container":{"name":"main","image":"registry.cn-hangzhou.aliyuncs.com/dahu/curl-jp:1.2","command":["sh","-c"],"ar...
Status:             Running
IP:                 172.16.1.152
Controlled By:      Workflow/test-v2
```

通过使用ags kubectl 命令，可以查看到describe pod的状态信息，所有kubectl 原生命令ags均支持。

## 查看workflow 资源使用量 {#section_ebj_d6d_wph .section}

1.  创建并拷贝内容到arguments-workflow-resource.yaml文件中，并执行`ags submit arguments-workflow-resource.yaml`命令，指定resource requests。

    ``` {#codeblock_ojc_rhf_7fz}
    apiVersion: argoproj.io/v1alpha1
    kind: Workflow
    metadata:
      name: test-resource
    spec:
      arguments: {}
      entrypoint: test-resource-
      templates:
      - inputs: {}
        metadata: {}
        name: test-resource-
        outputs: {}
        parallelism: 1
        steps:
        - - arguments: {}
            name: bcl2fq
            template: bcl2fq
      - container:
          args:
          - id > /tmp/yyy;echo `date` > /tmp/aaa;ps -e -o comm,euid,fuid,ruid,suid,egid,fgid,gid,rgid,sgid,supgid
            > /tmp/ppp;ls -l /tmp/aaa;sleep 100;pwd
          command:
          - sh
          - -c
          image: registry.cn-hangzhou.aliyuncs.com/dahu/curl-jp:1.2
          name: main
          resources:                #don't use too much resources
            requests:
              memory: 320Mi
              cpu: 1000m
        inputs: {}
        metadata: {}
        name: bcl2fq
        outputs: {}
    ```

2.  执行`ags get test456 --show`命令，查看workflow 资源使用。

    本例中，结果显示的是 Pod和test456 使用的核/时。

    ``` {#codeblock_z0h_paa_be7}
    [root@iZwz92q9h36kv8posr0i6uZ ~]# ags get test456 --show
    Name:                test456
    Namespace:           default
    ServiceAccount:      default
    Status:              Succeeded
    Created:             Thu Nov 22 14:41:49 +0800 (2 minutes ago)
    Started:             Thu Nov 22 14:41:49 +0800 (2 minutes ago)
    Finished:            Thu Nov 22 14:43:30 +0800 (27 seconds ago)
    Duration:            1 minute 41 seconds
    Total CPU:           0.02806    (core*hour)
    Total Memory:        0.00877    (GB*hour)
    
    STEP           PODNAME             DURATION  MESSAGE  CPU(core*hour)  MEMORY(GB*hour)
    ✔ test456                                            0               0
    └---✔ bcl2fq  test456-4221301428  1m                 0.02806         0.00877
    ```


## securityContext 安全支持 {#section_j2n_s17_ovt .section}

创建并拷贝内容到arguments-security-context.yaml文件中，并执行`ags submit arguments-security-context.yaml`命令，绑定对应的 psp来进行权限控制。

``` {#codeblock_dv4_n6y_zkq}
apiVersion: argoproj.io/v1alpha1
kind: Workflow
metadata:
  name: test
spec:
  arguments: {}
  entrypoint: test-security-
  templates:
  - inputs: {}
    metadata: {}
    name: test-security-
    outputs: {}
    parallelism: 1
    steps:
    - - arguments: {}
        name: bcl2fq
        template: bcl2fq
  - container:
      args:
      - id > /tmp/yyy;echo `date` > /tmp/aaa;ps -e -o comm,euid,fuid,ruid,suid,egid,fgid,gid,rgid,sgid,supgid
        > /tmp/ppp;ls -l /tmp/aaa;sleep 100;pwd
      command:
      - sh
      - -c
      image: registry.cn-hangzhou.aliyuncs.com/dahu/curl-jp:1.2
      name: main
      resources:                #don't use too much resources
        requests:
          memory: 320Mi
          cpu: 1000m
    inputs: {}
    metadata: {}
    name: bcl2fq
    outputs: {}
    securityContext:
      runAsUser: 800
```

## Yaml定义自动retry功能 {#section_szr_k3b_2h0 .section}

有时一些bash命令会由于不明原因失败，重试就可以解决，AGS 提供一种基于yaml配置的自动重启机制，当Pod内命令运行失败后，会自动拉起重试，并且可以设置重试次数。

创建并拷贝内容到arguments-auto-retry.yaml文件中，并执行`ags submit arguments-auto-retry.yaml`命令，配置Workflow的自动重启机制。

``` {#codeblock_agw_ndb_ncs}
# This example demonstrates the use of retries for a single container.
apiVersion: argoproj.io/v1alpha1
kind: Workflow
metadata:
  generateName: retry-container-
spec:
  entrypoint: retry-container
  templates:
  - name: retry-container
    retryStrategy:
      limit: 10
    container:
      image: python:alpine3.6
      command: ["python", -c]
      # fail with a 66% probability
      args: ["import random; import sys; exit_code = random.choice([0, 1, 1]); sys.exit(exit_code)"]
```

## 基于最近失败断点retry整个workflow {#section_5qc_vro_mgu .section}

在整个workfow运行中，有时候会出现某个step失败，这时候希望从某个失败的节点重新retry workflow，类似断点续传的断点重试功能。

1.  执行`ags get test456 --show`命令，查看Workflow test456 从哪个step断点。

    ``` {#codeblock_s03_mek_wmu}
    [root@iZwz92q9h36kv8posr0i6uZ ~]# ags get test456 --show
    Name:                test456
    Namespace:           default
    ServiceAccount:      default
    Status:              Succeeded
    Created:             Thu Nov 22 14:41:49 +0800 (2 minutes ago)
    Started:             Thu Nov 22 14:41:49 +0800 (2 minutes ago)
    Finished:            Thu Nov 22 14:43:30 +0800 (27 seconds ago)
    Duration:            1 minute 41 seconds
    Total CPU:           0.0572   (core*hour)
    Total Memory:        0.01754    (GB*hour)
    
    STEP           PODNAME             DURATION  MESSAGE  CPU(core*hour)  MEMORY(GB*hour)
     ✔ test456                                            0               0
     └---✔ bcl2fq  test456-4221301428  1m                 0.02806         0.00877
     └---X bcl2fq  test456-4221301238  1m                 0.02806         0.00877
    ```

2.  执行`ags retry test456`命令，从最近失败断点处继续retry workflow test456。

## 使用 ECI 运行Workflow {#section_qo3_q6i_2be .section}

ECI操作请参见[弹性容器实例ECI](https://www.alibabacloud.com/help/zh/doc-detail/89129.html)。

配置使用 ECI 前，请先安装AGS，请参见[AGS 下载和安装](intl.zh-CN/用户指南/Kubernetes集群/工作流/AGS命令行帮助.md#section_bs7_2zj_w60)。

1.  执行`kubectl get cm -n argo`命令，获取Workflow对应的yaml文件名称。

    ``` {#codeblock_te0_k2b_eor}
    [root@iZwz9f4ofes6kbo01ipuf1Z ~]# kubectl get cm -n argo
    NAME                            DATA      AGE
    workflow-controller-configmap   1         4d
    ```

2.  执行`kubectl get cm -n argo workflow-controller-configmap -o yaml`命令，打开workflow-controller-configmap.yaml文件，并使用如下内容覆盖当前yaml文件的内容。

    ``` {#codeblock_qp1_vle_1ne}
    apiVersion: v1
    data:
      config: |
        containerRuntimeExecutor: k8sapi
    kind: ConfigMap
    ```

3.  执行`kubectl delete pod <*podName*>`命令，重启 argo controller。

    **说明：** 这里的*podName*为Workflow所在的Pod的名称。

4.  创建并拷贝内容到arguments-workflow-eci.yaml文件中，并执行`ags submit arguments-workflow-eci.yaml`命令，在 ECI 上运行的容器添加nodeSelector 和Tolerations这两个标识。

    ``` {#codeblock_6pe_0mb_dob}
    apiVersion: argoproj.io/v1alpha1
    kind: Workflow
    metadata:
      generateName: hello-world-
    spec:
      entrypoint: whalesay
      templates:
      - name: whalesay
        container:
          image: docker/whalesay
          command: [env]
          #args: ["hello world"]
          resources:
            limits:
              memory: 32Mi
              cpu: 100m
        nodeSelector:               # 添加nodeSelector
          type: virtual-kubelet
        tolerations:                # 添加tolerations
        - key: virtual-kubelet.io/provider
          operator: Exists
        - key: alibabacloud.com
          effect: NoSchedule
    ```


## 查看Workflow 实际资源使用量以及峰值 {#section_lmk_ax6_gqq .section}

ags workflow controller 会通过metrics-server 自动获取Pod 每分钟的实际资源使用量，并且统计出来总量和各个Pod的峰值使用量。

执行`ags get steps-jr6tw --metrics`命令，查看Workflow 实际资源使用量以及峰值。

``` {#codeblock_4e7_7og_inb}
➜  ags get steps-jr6tw --metrics
Name:                steps-jr6tw
Namespace:           default
ServiceAccount:      default
Status:              Succeeded
Created:             Tue Apr 16 16:52:36 +0800 (21 hours ago)
Started:             Tue Apr 16 16:52:36 +0800 (21 hours ago)
Finished:            Tue Apr 16 19:39:18 +0800 (18 hours ago)
Duration:            2 hours 46 minutes
Total CPU:           0.00275    (core*hour)
Total Memory:        0.04528    (GB*hour)

STEP            PODNAME                 DURATION  MESSAGE  CPU(core*hour)  MEMORY(GB*hour)  MaxCpu(core)  MaxMemory(GB)
 ✔ steps-jr6tw                                             0               0                0             0
 └---✔ hello1   steps-jr6tw-2987978173  2h                 0.00275         0.04528          0.000005      0.00028
```

## 设置Workflow 优先级 {#section_5sy_ryg_qqn .section}

当前面有一些任务正在运行时，有一个紧急任务急需运行，此时，您可以给Workflow 设置高、中、低的优先级，高优先级抢占低优先级任务的资源。

-   您可以给某个Pod设置高优先级，示例如下：

    创建并拷贝内容到arguments-high-priority-taskA.yaml文件中，并执行`ags submit arguments-high-priority-taskA.yaml`命令，给任务A设置高优先级。

    ``` {#codeblock_r94_s19_4gd}
    apiVersion: scheduling.k8s.io/v1beta1
    kind: PriorityClass
    metadata:
      name: high-priority
    value: 1000000
    globalDefault: false
    description: "This priority class should be used for XYZ service pods only."
    ```

-   您可以给某个Pod设置中优先级，示例如下：

    创建并拷贝内容到arguments-high-priority-taskB.yaml文件中，并执行`ags submit arguments-high-priority-taskB.yaml`命令，给任务B设置中优先级。

    ``` {#codeblock_fdi_os8_2a1}
    apiVersion: scheduling.k8s.io/v1beta1
    kind: PriorityClass
    metadata:
      name: medium-priority
    value: 100
    globalDefault: false
    description: "This priority class should be used for XYZ service pods only."
    ```

-   您也可以一个Workflow设置高优先级，示例如下：

    创建并拷贝内容到arguments-high-priority-Workflow.yaml文件中，并执行`ags submit arguments-high-priority-Workflow.yaml`命令，给Workflow中所有的Pod设置高优先级。

    ``` {#codeblock_hfh_pb2_9a1}
    apiVersion: argoproj.io/v1alpha1
    kind: Workflow                  # new type of k8s spec
    metadata:
      generateName: high-proty-   # name of the workflow spec
    spec:
      entrypoint: whalesay          # invoke the whalesay template
      podPriorityClassName: high-priority # workflow level priority
      templates:
      - name: whalesay              # name of the template
        container:
          image: ubuntu
          command: ["/bin/bash", "-c", "sleep 1000"]
          resources:
            requests:
              cpu: 3
    						
    ```


下面以一个Workflow里面含有两个Pod，分别给一个 Pod 设置中优先级，另一个 Pod 设置高优先级，此时，高优先级的 Pod 就能抢占低优先级 Pod的资源。

1.  创建并拷贝内容到arguments-high-priority-steps.yaml文件中，并执行`ags submit arguments-high-priority-steps.yaml`命令，给Pod 设置优先级。

    ``` {#codeblock_10g_67n_czm}
    apiVersion: argoproj.io/v1alpha1
    kind: Workflow
    metadata:
      generateName: steps-
    spec:
      entrypoint: hello-hello-hello
    
      templates:
      - name: hello-hello-hello
        steps:
        - - name: low           
            template: low
        - - name: low-2          
            template: low
          - name: high           
            template: high
    
      - name: low
        container:
          image: ubuntu
          command: ["/bin/bash", "-c", "sleep 30"]
          resources:
            requests:
              cpu: 3
    
      - name: high
        priorityClassName: high-priority # step level priority
        container:
          image: ubuntu
          command: ["/bin/bash", "-c", "sleep 30"]
          resources:
            requests:
              cpu: 3
    ```

2.  运行结果为高优先级 Pod 会抢占并删除老的Pod。执行结果如下：

    ``` {#codeblock_7lf_jii_ytn}
    Name:                steps-sxvrv
    Namespace:           default
    ServiceAccount:      default
    Status:              Failed
    Message:             child 'steps-sxvrv-1724235106' failed
    Created:             Wed Apr 17 15:06:16 +0800 (1 minute ago)
    Started:             Wed Apr 17 15:06:16 +0800 (1 minute ago)
    Finished:            Wed Apr 17 15:07:34 +0800 (now)
    Duration:            1 minute 18 seconds
    
    STEP            PODNAME                 DURATION  MESSAGE
     ✖ steps-sxvrv                                    child 'steps-sxvrv-1724235106' failed
     ├---✔ low      steps-sxvrv-3117418100  33s
     └-·-✔ high     steps-sxvrv-603461277   45s
       └-⚠ low-2    steps-sxvrv-1724235106  45s       pod deleted
    ```

    **说明：** 这里高优先级任务会自动抢占低优先Pod所占用资源，会停止低优先级任务，中断正在运行的进程，所以使用时要非常谨慎。


## Workflow Filter {#section_61n_dqz_205 .section}

在ags get workflow 中，针对较大的Workflow 可以使用 filter 列出指定状态的Pod。

1.  执行`ags get <pod名称\> --status Running`命令，列出指定状态的Pod。

    ``` {#codeblock_44a_5ae_9gr}
    [root@iZ8vb19036cvgu1tpxkzl1Z workflow-prioty]# ags get pod-limits-n262v --status Running
    Name:                pod-limits-n262v
    Namespace:           default
    ServiceAccount:      default
    Status:              Running
    Created:             Wed Apr 17 15:59:08 +0800 (1 minute ago)
    Started:             Wed Apr 17 15:59:08 +0800 (1 minute ago)
    Duration:            1 minute 17 seconds
    Parameters:
      limit:             300
    
    STEP                   PODNAME                      DURATION  MESSAGE
     ● pod-limits-n262v
       ├-● run-pod(13:13)  pod-limits-n262v-3643890604  1m
       ├-● run-pod(14:14)  pod-limits-n262v-4115394302  1m
       ├-● run-pod(16:16)  pod-limits-n262v-3924248206  1m
       ├-● run-pod(17:17)  pod-limits-n262v-3426515460  1m
       ├-● run-pod(18:18)  pod-limits-n262v-824163662   1m
       ├-● run-pod(20:20)  pod-limits-n262v-4224161940  1m
       ├-● run-pod(22:22)  pod-limits-n262v-1343920348  1m
       ├-● run-pod(2:2)    pod-limits-n262v-3426502220  1m
       ├-● run-pod(32:32)  pod-limits-n262v-2723363986  1m
       ├-● run-pod(34:34)  pod-limits-n262v-2453142434  1m
       ├-● run-pod(37:37)  pod-limits-n262v-3225742176  1m
       ├-● run-pod(3:3)    pod-limits-n262v-2455811176  1m
       ├-● run-pod(40:40)  pod-limits-n262v-2302085188  1m
       ├-● run-pod(6:6)    pod-limits-n262v-1370561340  1m
    ```

2.  执行`ags get <pod名称\> --sum-info`命令，统计当前Pod 状态信息。

    ``` {#codeblock_zmc_k41_2rp}
    [root@iZ8vb19036cvgu1tpxkzl1Z workflow-prioty]# ags get pod-limits-n262v --sum-info --status Error
    Name:                pod-limits-n262v
    Namespace:           default
    ServiceAccount:      default
    Status:              Running
    Created:             Wed Apr 17 15:59:08 +0800 (2 minutes ago)
    Started:             Wed Apr 17 15:59:08 +0800 (2 minutes ago)
    Duration:            2 minutes 6 seconds
    Pending:             198
    Running:             47
    Succeeded:           55
    Parameters:
      limit:             300
    
    STEP                 PODNAME  DURATION  MESSAGE
     ● pod-limits-n262v
    ```


## 敏捷版Autoscaler 使用流程 {#section_k63_sa9_b8f .section}

在敏捷版使用autoscaler 需要用户提前创建或者已经具备以下资源：

-   您已经有一个VPC。
-   您已经有一个vSwitch。
-   您已经设置好一个安全组。
-   您已经获取到敏捷版的APIServer 内网地址。
-   您明确扩容节点的规格。
-   您已创建好一个ECS实例且拥有公网访问能力。

您可以在AGS命令行，按照界面提示进行如下操作：

``` {#codeblock_scq_9e8_bqj}
$ ags config autoscaler   根据提示输入对应的值
Please input vswitchs with comma separated
vsw-hp3cq3fnv47bpz7x58wfe
Please input security group id
sg-hp30vp05x6tlx13my0qu
Please input the instanceTypes with comma separated
ecs.c5.xlarge
Please input the new ecs ssh password
xxxxxxxx
Please input k8s cluster APIServer address like(192.168.1.100)
172.24.61.156
Please input the autoscaling mode (current: release. Type enter to skip.)
Please input the min size of group (current: 0. Type enter to skip.)
Please input the max size of group (current: 1000. Type enter to skip.)
Create scaling group successfully.
Create scaling group config successfully.
Enable scaling group successfully.
Succeed
```

配置完成后，登录[弹性伸缩控制台](https://essnew.console.aliyun.com)， 可以看到创建好的自动伸缩组。

## 配置使用 ags configmap {#section_lgj_ygv_zjb .section}

本例中，默认使用 hostNetwork。

1.  执行`kubectl get cm -n argo`命令，获取 Workflow 对应的yaml文件名称。

    ``` {#codeblock_s72_sri_e4z}
    [root@iZ8vb19036cvgu1tpxkzl1Z ~]# kubectl get cm -n argo
    NAME                            DATA   AGE
    workflow-controller-configmap   1      6d23h
    ```

2.  执行`kubectl edit cm workflow-controller-configmap -n argo`命令，打开workflow-controller-configmap.yaml文件，将如下内容填入当前yaml文件中。

    ``` {#codeblock_cjf_0dg_wab}
    data:
      config: |
        extraConfig:
          enableHostNetwork: true
          defaultDnsPolicy: Default
    ```

    填入完成后，workflow-controller-configmap.yaml全文如下：

    ``` {#codeblock_8y4_o2y_z7p}
    apiVersion: v1
    data:
      config: |
        extraConfig:
          enableHostNetwork: true
          defaultDnsPolicy: Default
    kind: ConfigMap
    metadata:
      name: workflow-controller-configmap
      namespace: argo
    ```

3.  配置完成后，新部署的Workflow 均会默认使用hostNetwork，且dnsPolicy为Default。
4.  （可选）如果配置了psp，需要在psp中对应的yaml文件增加如下内容。

    ``` {#codeblock_lpy_80o_qh8}
    hostNetwork: true
    ```

    **说明：** 如果该yaml文件中已有hostNetwork参数，需要将值改为ture。

    完整的yaml文件示例点[这里](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#host-namespaces)。


