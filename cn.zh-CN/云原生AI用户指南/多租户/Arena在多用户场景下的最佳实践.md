---
keyword: [Arena, K8s集群, 多用户, 存储]
---

# Arena在多用户场景下的最佳实践

本文通过实现五个目标任务举例说明如何在多用户场景下使用Arena。

## 操作视频



## 前提条件

请确保您已完成以下操作：

-   创建一个ACK集群。详情请参见[创建Kubernetes托管版集群](/cn.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。
-   在ACK集群同VPC下，创建一个操作系统为Linux的ECS实例。具体操作步骤请参见[使用向导创建实例](/cn.zh-CN/实例/创建实例/使用向导创建实例.md)。

    本示例中，ECS实例被称为Client机器。Client机器作为Arena的工作站，提交作业至ACK集群。

-   安装最新版本Arena。具体操作步骤请参见[安装Arena]()。

## 背景信息

当多个开发人员在一个公司或大团体下使用Arena进行工作时，为了有效管理，您可能需要对这些人员进行小组划分，且每个小组需要彼此隔离。这样在同一个集群内，小组就是您分配、隔离资源和权限的基本单元。

通常您需要将整个集群的资源（GPU、CPU、MEM）根据具体需求划分给每个组，并且给组内成员分配不同权限，以及提供各自独立的Arena使用环境。其中权限包括：用户对于作业的可见、可操作权限，用户的作业对特定数据的读写权限。

![arena](../images/p173496.png "配置多用户使用Arena的工作环境图")

本文示例中的ACK集群和Client机器的节点信息如下表所示。

|主机名|角色|IP地址|GPU卡数|CPU核数|MEM|
|---|--|----|-----|-----|---|
|client01|Client|10.0.0.97（私有）39.98.xxx.xxx（公）|0|2|8 GiB|
|master01|Master|10.0.0.91（私有）|0|4|8 GiB|
|master02|Master|10.0.0.92（私有）|0|4|8 GiB|
|master03|Master|10.0.0.93（私有）|0|4|8 GiB|
|worker01|Worker|10.0.0.94（私有）|1|4|30 GiB|
|worker02|Worker|10.0.0.95（私有）|1|4|30 GiB|
|worker03|Worker|10.0.0.96（私有）|1|4|30 GiB|

**说明：** 如无特殊说明的话，本文的操作均为集群管理员（admin）操作，操作节点为Client机器。

## 目标任务

本文最佳实践的示例中将达成以下五个目标任务：

-   目标一：您需要为当前ACK集群创建两个分组dev1和dev2，并且为这两个分组各添加一个用户bob和tom。
-   目标二：每个用户只能通过自己的账号和密码登录到Client机器，使用自己环境下的Arena。
-   目标三：bob和tom只对自己提交的作业可见，以及进行管理。
-   目标四：按组划分Worker节点GPU、CPU、MEM资源（注：仅Worker节点的计算资源才能被Arena作业使用）。
-   目标五：创建组内共享的数据卷，和组与组之间全局共享的数据卷。

|组名|用户|GPU|CPU|MEM|共享数据卷|
|--|--|---|---|---|-----|
|dev1|bob|1|不限制|不限制|dev1-public和department1-public-dev1|
|dev2|tom|2|8|60 GiB|dev2-public和department1-public-dev2|

**说明：** department1-public-dev1和department1-public-dev2数据卷指向的是同一份数据；组dev1和dev2为所有用户共享；dev1-public和dev2-public分别对应分组dev1、dev2用户独享的数据。

## 步骤一：创建和管理ACK集群的用户和组

为了安全起见，不建议您直接登录ACK集群的Master节点安装使用Arena以及对集群进行操作，因此建议您在与ACK集群同一个VPC下创建ECS实例（Client机器）。通过配置KubeConfig文件，使用ECS实例节点对ACK集群进行访问。

1.  创建Client机器上的用户和组。

    1.  通过kubectl连接ACK集群。

        使用kubectl命令连接ACK集群时，您需要安装kubectl客户端工具和配置供集群管理员admin操作ACK集群的KubeConfig文件。有关具体的操作步骤，请参见[t16645.md\#](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl工具连接集群.md)。

        **说明：** 要求kubectl的版本大于或等于1.10。

    2.  执行以下代码在Client机器上创建对应的Linux UID和GID。

        集群管理员需要为bob、tom以及分组dev1、dev2，在Client机器上创建对应的Linux UID和GID。通过Linux自身的账号系统机制，实现[目标二](#li_b62_eoa_lit)，即每个用户只能通过自己的账号和密码登录到Client机器，使用自己环境下的Arena。

        ```
        # Create Linux groups, users.
        groupadd -g 10001 dev1
        groupadd -g 10002 dev2
        adduser -u 20001 -s /bin/bash -G dev1 -m bob
        adduser -u 20002 -s /bin/bash -G dev2 -m tom
        
        #设置bob登录操作AI平台的Linux节点的密码。
        passwd bob 
        #设置tom登录操作AI平台的Linux节点的密码。
        passwd tom
        ```

2.  创建ACK集群中的用户（服务账号）和组（命名空间）。

    提交给AI平台的作业都将在ACK集群中运行。在ACK中，作业的拥有者以服务账号（ServiceAccount）区分，作业运行的环境以名字空间（Namespace）区分。运维管理员admin将创建ServiceAccount和Namespace，并保证与在Client机器上创建的用户和组对应。可以将Namespace对应组，ServiceAccount对应用户。

    admin以root身份登录Client机器，并确保拥有操作集群的权限（[通过kubectl连接ACK集群](#substep_212_rl0_m60)步骤中完成）。执行以下操作：

    ```
    # 创建分组dev1对应的Namespace。
    kubectl create namespace dev1
    
    # 创建分组dev2对应的Namespace。
    kubectl create namespace dev2
    
    # 创建用户bob对应的serviceaccount。
    kubectl create serviceaccount bob -n dev1
    
    # 创建用户tom对应的serviceaccount。
    kubectl create serviceaccount tom -n dev2
    
    # 可以查看创建的组（Namespace）和用户（ServiceAccount）。
    kubectl get serviceaccount -n dev1
    kubectl get serviceaccount -n dev2
    ```

    预期输出：

    ![namespace serviceacount](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/9783572061/p173703.png)


## 步骤二：为用户配置Arena的使用环境

1.  安装Arena。

    集群管理员在Client机器上安装Arena。首先集群管理员以root身份登录到Client机器，下载社区最新Arena发行的release安装包，然后解压、执行安装包中install.sh脚本。有关具体的操作步骤，请参见[安装Arena]()。

    **说明：** 在同一台Linux操作机上，您只需安装一份Arena工具。管理员通过为每位用户配置各自的配置文件，从而实现隔离，便于用户以不同的权限使用Arena工具。

2.  为用户创建Arena配置文件。

    为了使不同用户以各自的身份和权限正确操作AI平台集群（ACK集群），首先您需要创建不同用户用于Arena连接ACK集群的环境配置文件（即创建在各自ServiceAccount下使用的KubeConfig文件）。

    集群管理员以root身份登录到Client机器上，使用[附录](#section_m57_i99_0zo)提供的脚本createKubeConfig.sh为每位用户生成各自的KubeConfig配置文件。执行以下代码：

    ```
    # 赋予生成KubeConfig脚本工具执行权限。
    chmod +x createKubeConfig.sh
    
    # 为组dev1下的bob用户创建专门的KubeConfig文件。
    ./createKubeConfig.sh bob -n dev1
    
    # 为组dev2下的tom用户创建专门的KubeConfig文件。
    ./createKubeConfig.sh tom -n dev2
    
    # 把生成各自用户的Kubernetes环境配置文件放到各自用户的home目录下。
    mkdir -p /home/bob/.kube/ && cp bob.config /home/bob/.kube/config
    mkdir -p /home/tom/.kube/ && cp tom.config /home/tom/.kube/config
    ```

    预期输出：

    ![config](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/8631672061/p173803.png)

    完成上述操作后，用户使用自己的账号登录Client机器后，就可以使用Arena自动连接正确的集群环境。至此，本文示例已完成[目标一](#li_s36_5go_gcm)和[目标二](#li_b62_eoa_lit)。


## 步骤三：为用户配置Arena的权限

1.  在ACK集群中为每个组（Namespace），按需创建组内的角色（Role）。

    集群管理员可以为每个组（Namespace）在ACK集群的具体操作权限配置不同的角色（Role）。一个角色（Role）代表组（Namespace）内一系列具体权限规则的集合。有关ACK集群中角色（Role）的定义方法，请参见[Using RBAC Authorization](https://kubernetes.io/docs/reference/access-authn-authz/rbac/)。

    1.  创建权限定义文件。

        因为需要保证组dev1下用户bob和组dev2下用户tom的作业彼此不可见，并且独立管理各自的作业，所以需要给组dev1和dev2创建最小权限，并绑定到对应的用户上（即用户bob和tom对应的ServiceAccount）。

        有关组dev1的权限定义，请参见[附录](#section_m57_i99_0zo)文件dev1\_roles.yaml；有关组dev2的权限定义，请参见[附录](#section_m57_i99_0zo)文件dev2\_roles.yaml。

    2.  创建好权限定义文件dev1\_roles.yaml和dev2\_roles.yaml后，集群管理员通过执行以下命令，使之在ACK集群中生效。

        ```
        kubectl apply -f dev1_roles.yaml
        
        kubectl apply -f dev2_roles.yaml
        ```

        预期输出：

        ![ROLE](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/0123672061/p173962.png)

        集群管理员可以通过以下命令查看集群中不同Namespace下的角色。

        ```
        kubectl get role -n dev1
        kubectl get role -n dev2
        ```

        如出现下图所示内容，即完成了角色创建操作。

        ![role](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/1123672061/p173966.png)

2.  为用户配置在集群内的权限（赋权）。

    组的角色创建完成后，我们需要给用户绑定这个角色，使之作用在组里的成员上，即赋权给用户。集群管理员通过将组（Namespace）内的某些角色（Role）与用户（ServiceAccount）进行绑定，来为每位用户在组内赋权。

    通过角色绑定，集群管理员可以为用户赋予多个组内的角色权限。这既包括用户所属的组内权限，也包括其他组内的权限。这样集群管理员就可以动态地管理任何用户在任何组内的权限，只需要更新用户与角色的对应关系即可。

    ACK中通过定义RoleBinding和ClusterRoleBinding对象来声明用户（ServiceAccount）在不同组（Namespaces）或者整个集群所有组中可以扮演的角色而获取权限。ACK中角色绑定（RoleBinding和ClusterRoleBinding）的定义方法可以参见官方的文档[Using RBAC Authorization](https://kubernetes.io/docs/reference/access-authn-authz/rbac/)。

    本文以[目标三](#li_zu0_17k_nom)为例，仅仅需要将用户bob和tom分别绑定各自组的权限即可（[步骤三](#step_9f2_rfl_ruj)中创建的角色）。操作步骤如下：

    1.  集群管理员以root身份登录Client机器后，通过kubectl执行[附录](#section_m57_i99_0zo)中的文件bob\_rolebindings.yaml和tom\_rolebindings.yaml即可完成用户和组角色的绑定，即用户赋权。

        ```
        kubectl apply -f bob_rolebindings.yaml
        kubectl apply -f tom_rolebindings.yaml
        ```

        预期输出：

        ![rolebinding](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/3625182061/p173986.png)

    2.  集群管理员通过以下命令查看集群中不同Namespace下对不同用户的角色赋权情况。

        ```
        kubectl get rolebinding -n dev1
        kubectl get rolebinding -n dev2
        ```

        如出现下图所示内容，即完成了权限绑定操作。

        ![role created](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/3625182061/p173991.png)

        至此，本文示例已完成了上述的前三个目标任务。


## 步骤四：配置用户组资源配额

AI平台通过ACK统一管理所有集群资源。为了保证不同组和用户安全、有效地使用集群资源，需要为小组分配资源配额。AI平台会在用户提交作业时，自动检查用户有权限运行作业的组配额使用情况。如果作业需求量超过配额上限，则提交会被拒绝。

在ACK中资源配额（ResourceQuota）的作用域是命名空间（Namespace），即对应本文中的用户组。ResourceQuota支持的资源类型很多，既包括CPU、Memory，也包括Nvidia GPU这类扩展资源。ResourceQuota也能够限制一个Namespace中容器及其它Kubernetes对象的使用量。有关更详细的说明，请参见[Resource Quotas](https://kubernetes.io/docs/concepts/policy/resource-quotas/)。

根据[目标四](#li_db9_ab1_imr)中的描述，本文示例按照组划分当前集群的GPU、CPU、MEM等资源。示例假设划分的配置如[数据配置表](#table_xy2_zmb_z48)所示。本文示例将分配组dev1一张GPU卡，不划分CPU和MEM，即可以使用整个集群的CPU和MEM。分配组dev2两张GPU卡，八核CPU和60 GiB MEM。具体的操作步骤如下：

1.  集群管理员登录Client机器后，使用[附录](#section_m57_i99_0zo)中dev1\_quota.yaml和dev2\_quota.yaml文件定义组资源配额。执行以下命令使之在集群中生效。

    ```
    kubectl apply -f dev1_quota.yaml
    kubectl apply -f dev2_quota.yaml
    ```

    创建后，集群管理员可以通过以下命令查看ResourceQuata是否生效，并可以看到定义的配额以及组内已经分配掉的资源。

    ```
    # 查看组dev1下的资源配额。
    kubectl get resourcequotas -n dev1
    
    # 查看组dev2下的资源配额。
    kubectl get resourcequotas -n dev2
    
    # 查看组dev1资源配额的使用情况。
    kubectl describe resourcequotas dev1-compute-resources -n dev1
    
    # 查看组dev2资源配额的使用情况。
    kubectl describe resourcequotas dev2-compute-resources -n dev2
    ```

    预期输出：

    ![quota](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/3625182061/p174046.png)

    至此，本文完成了上述的[目标四](#li_db9_ab1_imr)的按照组划分ACK集群计算资源。


## 步骤五：配置多级别共享存储NAS

根据用户组织内对数据共享的控制规则，可以为AI平台内的组和用户分别配置带有相应权限控制的数据卷挂载，方便用户间安全地共享数据。

本文示例[目标五](#li_9tk_kye_uqc)需要创建两类共享数据卷。一类用于存放全局的共享数据，所有组内成员都可以访问和使用，另一类仅仅用于各个小组内共享。本文假设共享数据卷的需求如[数据配置表](#table_xy2_zmb_z48)所示。四个数据卷dev1-public、dev2-public、department1-public-dev1、department1-public-dev2中的department1-public-dev1和department1-public-dev2指向NAS盘的同一个目录，组dev1和dev2共享这一份数据。dev1-public、dev2-public分别指向组dev1和组dev2所属NAS盘的不同目录，组dev1 、dev2独享这份数据。具体操作如下：

1.  创建NAS实例。

    在[阿里云NAS控制台](https://nasnext.console.aliyun.com/overview)开通、购买NAS示例，并创建挂载点。有关详细步骤，请参见[配置NAS共享存储]()。

2.  配置ACK集群的存储卷（PV）和存储声明（PVC）。

    1.  创建PV。

        分别创建4个PV。有关创建PV的具体操作步骤，请参见[创建PV](/cn.zh-CN/Kubernetes集群用户指南/存储-CSI/NAS存储卷/使用NAS静态存储卷.md)。department1-public-dev1和department1-public-dev2是用于dev1和dev2两组分别共享部门department1的数据。dev1-public，dev2-public是用于dev1和dev2各自组内共享数据。创建PV的参数配置如下图所示。

        ![PV](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/6337182061/p174088.png)

        **说明：** 设置挂载点时，选择上步骤创建NAS实例时创建的挂载点。

    2.  创建PVC。

        使用上步创建的PV，分别创建PVC。有关创建PVC的具体操作步骤，请参见[创建PVC](/cn.zh-CN/Kubernetes集群用户指南/存储-CSI/NAS存储卷/使用NAS静态存储卷.md)。

        完成创建PVC后，可以看到在命名空间dev1下给dev1组分配部门和小组两级共享的NAS数据卷：department1-public-dev1和dev1-public。在命名空间dev2下给dev2组分配部门和小组两级共享的NAS数据卷：department1-public-dev2和dev2-public。

3.  检查数据卷的配置。

    集群管理员以root身份登录Client机器，执行以下命令查看为不同组分配的NAS数据情况。

    ```
    # 查看组dev1可以使用的数据卷。
    arena data list -n dev1
    
    # 查看组dev2可以使用的数据卷。
    arena data list -n dev2
    ```

    预期输出：

    ![check](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/3599282061/p174112.png)

    至此，本文已经完成了所有目标。接下来本文将会以bob和tom账户进行登录使用Arena。


## 步骤六：模拟多用户操作

**用户bob**

1.  登录Client机器，执行以下命令查看当前可以用共享数据卷。

    ```
    # 使用bob账号登录Client机器
    ssh bob@39.98.xxx.xx
    
    # 通过arena data list查看当前用户可用共享数据卷。
    arena data list
    ```

    预期输出：

    ![result](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/4599282061/p174151.png)

2.  执行以下命令提交一个需要1张GPU卡的训练作业。

    ```
    arena submit tf \
            --name=tf-git-bob-01 \
            --gpus=1 \
            --image=tensorflow/tensorflow:1.5.0-devel-gpu \
            --sync-mode=git \
            --sync-source=https://code.aliyun.com/xiaozhou/tensorflow-sample-code.git \
            "python code/tensorflow-sample-code/tfjob/docker/mnist/main.py --max_steps 10000 --data_dir=code/tensorflow-sample-code/data"
    ```

3.  执行以下命令列出当前用户提交的作业。

    ```
    arena list
    ```

    预期输出：

    ![bob](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/4599282061/p174177.png)

4.  执行以下命令再提交一个一张GPU卡的训练作业。

    ```
    arena submit tf \
            --name=tf-git-bob-02 \
            --gpus=1 \
            --image=tensorflow/tensorflow:1.5.0-devel-gpu \
            --sync-mode=git \
            --sync-source=https://code.aliyun.com/xiaozhou/tensorflow-sample-code.git \
            "python code/tensorflow-sample-code/tfjob/docker/mnist/main.py --max_steps 10000 --data_dir=code/tensorflow-sample-code/data"
    ```

    由于本文示例只给dev1组分配了一张GPU卡，所以预期这个作业不会被调度起来。

    ![result](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/4599282061/p174148.png)

    ![reason](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/4599282061/p174150.png)

    可以看到，虽然整个集群还有剩余资源，但是由于bob所在的组只被分配了一张GPU卡资源，并且当前已经有了一个作业占用了资源，所以bob后续的提交的作业就会被暂停。


**用户tom**

1.  执行以下命令登录Client机器，查看当前可以用共享数据卷。

    ```
    # 使用tom账号登录Client机器。
    ssh tom@39.98.xx.xx
    
    # 通过arena data list查看当前用户可用共享数据卷。
    arena data list
    ```

    预期输出：

    ![tom job](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/4599282061/p174157.png)

2.  执行以下命令列出当前用户提交的作业。

    ```
    arena list
    ```

    此时看不到bob提交的作业。

    ![list](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/4599282061/p174176.png)

3.  执行以下命令提交一个需要1张GPU卡的训练作业。

    ```
     arena submit tf \
             --name=tf-git-tom-01 \
             --gpus=1 \
             --chief-cpu=2 \
             --chief-memory=10Gi \
             --image=tensorflow/tensorflow:1.5.0-devel-gpu \
             --sync-mode=git \
             --sync-source=https://code.aliyun.com/xiaozhou/tensorflow-sample-code.git \
             "python code/tensorflow-sample-code/tfjob/docker/mnist/main.py --max_steps 10000 --data_dir=code/tensorflow-sample-code/data"
    ```

    **说明：** 由于示例分配了组dev2的GPU、CPU、MEM资源，因此在dev2组里的用户提交作业的时候需要明确指定GPU、CPU、MEM的使用资源。

4.  执行以下命令再次提交一个需要1张GPU卡的训练作业。

    ```
     arena submit tf \
             --name=tf-git-tom-02 \
             --gpus=1 \
             --chief-cpu=2 \
             --chief-memory=10Gi \
             --image=tensorflow/tensorflow:1.5.0-devel-gpu \
             --sync-mode=git \
             --sync-source=https://code.aliyun.com/xiaozhou/tensorflow-sample-code.git \
             "python code/tensorflow-sample-code/tfjob/docker/mnist/main.py --max_steps 10000 --data_dir=code/tensorflow-sample-code/data"
    ```

5.  执行以下命令列出当前用户提交的作业。

    ```
    arena list
    ```

    预期输出：

    ![result](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/4599282061/p174181.png)


## 执行结果

从上述操作可以看到，目前分别位于两个组的用户bob、tom可以通过自己的账号登录Client机器独立地使用Arena ，并通过Arena查看和使用用户当前所在组可访问的存储资源和计算资源，以及管理各自的作业。

## 附录

[下载tools.tar.gz](https://lumo-package.oss-cn-beijing.aliyuncs.com/tools.tar.gz)的命令如下：

```
wget https://lumo-package.oss-cn-beijing.aliyuncs.com/tools.tar.gz
```

