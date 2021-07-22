---
keyword: PSP安全策略
---

# 使用PSP安全策略

PodSecurityPolicy（简称PSP）是Kubernetes中Pod部署时重要的安全校验手段，能够有效地约束应用运行时行为安全。本文主要介绍如何创建、管理和绑定PSP策略。

-   创建一个Kubernetes托管版或专有版集群，且集群版本至少为1.14.8-aliyun.1，请参见[创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。
-   以主账号（即阿里云账号）登录控制台，或赋予子账号（即RAM用户）管理员权限，请参见[配置RAM用户RBAC权限](/intl.zh-CN/Kubernetes集群用户指南/授权/配置RAM用户RBAC权限.md)，然后以子账号登录控制台。
-   如果您使用子账号进行PSP策略管理，子账号需要拥有以下RAM授权条件：

    -   `cs:DescribeClusterPSPState`：获取集群PSP开启状态。
    -   `cs:DescribeServiceAccountBindingPSP`：获取指定`serviceaccount`绑定的PSP策略。
    -   `cs:UpdateClusterPodSecurityPolicy`：更新集群中的PSP策略模型实例。
    -   `cs:CreatePSPBinding`：创建对指定`serviceaccount`的PSP绑定。
    -   `cs:UnbindingServiceAccountPSP`：解绑指定`serviceaccount`的PSP策略模型实例。
    更多自定义RAM授权策略，请参见[自定义RAM授权策略](/intl.zh-CN/Kubernetes集群用户指南/授权/自定义RAM授权策略.md)。


在Kubernetes Pod定义中，可以通过设定安全参数来约束应用容器运行时的行为。作为集群的管理员，则会希望在集群的安全纵深防护中具有像AppArmor，SELinux这样的策略引擎，能够在应用部署时对应用Pod进行强制的安全校验，而PSP正是这样的解决方案。

-   强制校验：PSP是Kubernetes原生的集群维度资源模型，通过API server的admission准入机制，在应用部署时刻对Pod的安全参数进行强制校验。如果参数配置不满足指定PSP策略的定义，API server会禁止该Pod的部署请求。
-   PSP策略的RBAC绑定：创建PSP策略，需要开启PSP对应的[admission plugin](https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/#how-do-i-turn-on-an-admission-controller)，同时建立指定Pod部署凭证对PSP策略的使用权限的RBAC绑定。
-   开启PSP特性：当前ACK新创建的托管和专有集群默认开启PSP特性，减少开启PSP带来的运维工作。
-   权限最小化原则：为了保证集群使用上的兼容性，默认给所有合法认证用户绑定了一个ack.privileged的特权策略，而在PSP的实际应用场景中，我们希望策略遵循权限最小化的安全原则，比如禁止部分用户部署特权容器，只能使用只读的根文件系统，或者只能挂载指定范围的host目录。

## 创建PSP策略

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群详情页面选择**安全管理** \> **策略管理**。

5.  在策略管理页面单击**创建**，在选择策略模板编辑框中选择目标策略模板，单击目标策略模板上的**使用**。

    除了集群默认设置的特权策略（默认放开所有安全校验）ack.privileged之外，默认提供了两个PSP策略实例模板：

    -   privileged-restricted：限制`privileged`，共用主机网络、端口、Namespace等特权容器的部署。
    -   privileged-root-volumes-restricted：限制`privileged`，共用主机网络、端口、Namespace等特权容器的部署，同时限制挂载卷的类型和root权限的使用。
6.  在创建YAML页面根据不同的安全等级需求修改对应的配置项，从而创建不同应用场景下的PSP策略。PSP模板中的配置项详细说明请参见[Pod Security Policies](https://kubernetes.io/docs/concepts/policy/pod-security-policy/)。

    |配置项|描述|
    |---|--|
    |privileged|启动特权容器。|
    |hostPID，hostIPC|使用主机Namespaces。|
    |hostNetwork，hostPorts|使用主机网络和端口。|
    |volumes|允许使用的挂载卷类型。|
    |allowedHostPaths|允许hostPath类型挂载卷在主机上挂载的路径，通过pathPrefix字段声明允许挂载的主机路径前缀组。|
    |allowedFlexVolumes|允许使用的指定FlexVolume驱动。|
    |fsGroup|配置Pod中挂载卷使用的辅组ID。|
    |readOnlyRootFilesystem|约束启动Pod使用只读的root文件系统。|
    |runAsUser，runAsGroup，supplementalGroups|指定Pod中容器启动的用户ID以及主组和辅组ID。|
    |allowPrivilegeEscalation，defaultAllowPrivilegeEscalation|约束Pod中是否允许配置allowPrivilegeEscalation=true，该配置会控制setuid的使用，同时控制程序是否可以使用额外的特权系统调用。|
    |defaultAddCapabilities，requiredDropCapabilities，allowedCapabilities|控制Pod中使用的Linux Capabilities。|
    |seLinux|控制Pod使用seLinux配置。|
    |allowedProcMountTypes|控制Pod允许使用的ProcMountTypes。|
    |annotations|配置Pod中容器使用的AppArmor或seccomp。|
    |forbiddenSysctls，allowedUnsafeSysctls|控制Pod中容器使用的sysctl配置。|

7.  单击**确定**。

    策略实例创建成功后，您可以在策略管理页面中查看创建的PSP策略实例。

    **说明：**

    -   在策略管理页签，单击目标策略**操作**列的**YAML**，可以在弹出的窗口中修改PSP策略。
    -   在策略管理页签，单击目标策略**操作**列的**删除**，删除PSP策略。

## 绑定PSP策略

您需要给应用部署者赋予相应的PSP策略使用权限，否则如果开启了PSP特性且应用部署者没有绑定任何的PSP策略（已经删除了默认特权绑定），部署的Pod会遇到以下错误。

```
Error from server (Forbidden): error when creating xxx: pods "xxx" is forbidden: unable to validate against any pod security policy: []
```

您可以对指定用户的服务账号或某个命名空间下的服务账号绑定PSP策略。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群详情页面选择**安全管理** \> **策略管理**。

5.  选择**策略绑定**页签，单击**新增绑定规则**。

6.  在新增绑定规则编辑框中设置**命名空间**、**服务账号**、**绑定策略**。

7.  单击**确定**。


## 解除默认绑定

为了使用已经创建的PSP策略和绑定的约束能力，您需要删除默认的特权绑定。

-   为了确保系统组件和已部署应用的正常运行，在解除默认特权绑定的同时，后台会自动给kube-system命名空间下的所有serviceaccount对象和系统默认的system:nodes用户组绑定特权策略的使用权限，以确保系统组件的正常工作。
-   对于已经存在的命名空间，如果在命名空间下您没有通过策略管理控制台创建任何的PSP策略绑定，后台会给该命名空间下的所有serviceaccount对象绑定特权策略的使用权限，以确保该命名空间中已有应用的正常启动。

**说明：**

-   对于已经在控制台创建了策略绑定的命名空间。

    为了使策略绑定的约束能力生效，在解除绑定时后台不会自动创建该命名空间下的特权策略绑定，因此在解除绑定前请您务必确认PSP策略和绑定的正确性。

-   对于新创建的命名空间。

    容器服务Kubernetes版后台不会自动为新命名空间下的serviceaccount对象创建对应的特权策略绑定，请您在新命名空间创建后及时创建对应的PSP策略和绑定对象。


1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群详情页面选择**安全管理** \> **策略管理**。

5.  在**策略管理使用引导**区域中单击**解除默认绑定**。

6.  在解绑对话框中单击**确定**。


## 确保PSP的admission controller为开启状态

当前ACK新建的集群默认开启了PSP的admission controller。然而对于运行中的已有集群，为了使上述PSP操作生效，我们需要确保集群API server的PSP admission controller为开启状态。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群详情页面选择**安全管理** \> **策略管理**。

5.  开启admission controller。

    -   如果在策略管理页面右上角显示![关闭](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7195659951/p131566.png)，则单击**改变PSP状态**，在PSP状态变更对话框中单击**确定**，开启admission controller。
    -   如果在策略管理页面右上角显示![开启](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8195659951/p131571.png)，则表示已开启admission controller。
    **说明：** 开启PSP会重启集群API server，如果集群中存在依赖API server的业务服务，请选择非业务非高峰期进行状态变更。


## 执行结果

下面将通过示例展示PSP策略管理的应用以及具体的安全校验应用效果。

1.  [t16645.md\#](/intl.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl管理Kubernetes集群.md)。

2.  创建privileged-restricted和privileged-root-volumes-restricted策略模板，详细介绍请参见[创建PSP策略](#section_70j_m0v_87s)。

3.  执行以下命令，创建2个测试命名空间，同时在命名空间下创建测试用的serviceaccount实例并建立相应的RBAC权限绑定。

    ```
    kubectl create ns ack-all-restrictive
    kubectl create ns ack-restrictive
    
    kubectl create sa ack-dev -n ack-restrictive
    kubectl create sa ack-tester -n ack-all-restrictive
    
    kubectl -n ack-restrictive create rolebinding ack-dev-editor \
               --clusterrole=edit \
               --serviceaccount=ack-restrictive:ack-dev
    kubectl -n ack-all-restrictive create rolebinding ack-tester-editor \
               --clusterrole=edit \
               --serviceaccount=ack-all-restrictive:ack-tester          
    ```

4.  在策略绑定页签中绑定PSP策略。

    1.  单击**新增绑定规则**，在新增绑定规则编辑框中设置**命名空间**为**ack-restrictive**，设置**服务账号**为**ack-dev**，设置**绑定策略**为**privileged-restricted**，单击**确定**。

    2.  单击**新增绑定规则**，在新增绑定规则编辑框中设置**命名空间**为**ack-restrictive**，设置**服务账号**为**ack-dev**，设置**绑定策略**为**privileged-root-volumes-restricted**，单击**确定**。

5.  执行以下命令，扮演使用ack-restrictive命名空间下的服务账号ack-dev和ack-all-restrictive命名空间下的服务账号ack-tester。

    ```
    alias kubectl-dev='kubectl --as=system:serviceaccount:ack-restrictive:ack-dev -n ack-restrictive'
    alias kubectl-tester='kubectl --as=system:serviceaccount:ack-all-restrictive:ack-tester -n ack-all-restrictive'
    
    kubectl-dev auth can-i use  podsecuritypolicy/privileged-restricted
    Warning: resource 'podsecuritypolicies' is not namespace scoped in group 'policy'
    yes
                            
    ```

6.  执行以下命令，创建共享主机网络的特权容器。

    ```
    kubectl-dev apply -f- <<EOF
    apiVersion: v1
    kind: Pod
    metadata:
      name: privileged
    spec:
      hostNetwork: true
      containers:
        - name: busybox
          image: busybox
          command: [ "sh", "-c", "sleep 1h" ]
    EOF
    Error from server (Forbidden): error when retrieving current configuration of:
    Resource: "/v1, Resource=pods", GroupVersionKind: "/v1, Kind=Pod"
    Name: "privileged", Namespace: "ack-restrictive"
    Object: &{map["apiVersion":"v1" "kind":"Pod" "metadata":map["annotations":map["kubectl.kubernetes.io/last-applied-configuration":""] "name":"privileged" "namespace":"ack-restrictive"] "spec":map["containers":[map["command":["sh" "-c" "sleep 1h"] "image":"busybox" "name":"busybox"]] "hostNetwork":%!q(bool=true)]]}
    from server for: "STDIN": pods "privileged" is forbidden: User "system:serviceaccount:ack-restrictive:ack-dev" cannot get resource "pods" in API group "" in the namespace "ack-restrictive"
    ```

    系统显示报错，创建特权容器失败。

7.  执行以下命令，创建非特权容器。

    ```
    kubectl-dev apply -f- <<EOF
    apiVersion: v1
    kind: Pod
    metadata:
      name: non-privileged
    spec:
      containers:
        - name: busybox
          image: busybox
          command: [ "sh", "-c", "sleep 1h" ]
    EOF
    pod/non-privileged created
    ```

    系统显示正常，创建非特权容器成功。

8.  执行以下命令，创建一个root权限Pod和一个非root权限Pod。

    ```
    kubectl-tester apply -f- <<EOF
    apiVersion: v1
    kind: Pod
    metadata:
      name: root-pod
    spec:
      containers:
        - name: busybox
          image: busybox
          command: [ "sh", "-c", "sleep 1h" ]
    EOF
    
    kubectl-tester apply -f- <<EOF
    apiVersion: v1
    kind: Pod
    metadata:
      name: non-root-pod
    spec:
      containers:
        - name: busybox
          image: busybox
          command: [ "sh", "-c", "sleep 1h" ]
          securityContext:
            runAsUser: 1000
            runAsGroup: 3000
    EOF
    ```

9.  执行以下命令，查看Pod状态。

    使用root权限的容器创建失败，使用的ack-tester sa绑定的PSP策略会约束pod使用非root权限启动容器。

    命令如下：

    ```
    kubectl-tester get po
    ```

    结果如下：

    ```
    NAME           READY   STATUS                       RESTARTS   AGE
    non-root-pod   1/1     Running                      0          115s
    root-pod       0/1     CreateContainerConfigError   0          24s
    ```

10. 执行以下命令，查看错误详情。

    命令如下：

    ```
    kubectl describe pod root-pod
    ```

    结果如下：

    ```
    Warning  Failed     28s (x5 over 2m1s)   kubelet, cn-shenzhen.192.168.1.52  Error: container has runAsNonRoot and image will run as root
    ```


