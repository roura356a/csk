---
keyword: [Inclavare Containers, 远程证明, 机密容器]
---

# 在加密计算托管集群中使用Inclavare Containers机密容器实现远程证明

Inclavare Containers实现了一个针对云场景的通用且跨平台的远程证明架构EAA（Enclave Attestation Architecture）。EAA能够向您证明其敏感的工作负载是运行在真实可信的基于机密技术硬件的TEE中的。本文介绍如何在部署了Inclavare Containers的加密计算托管集群中实现远程证明。

-   [创建加密计算托管集群](/cn.zh-CN/Kubernetes集群用户指南/ACK-TEE机密计算/创建加密计算托管集群.md)。

    **说明：** 目前只有**规格族**为**安全增强计算型c7t**的实例规格支持加密计算托管集群，请确保所选可用区有这些实例规格。

-   创建一台装有CentOS 8.2或Ubuntu 18.04操作系统的ECS。具体操作，请参见[创建ECS实例](/cn.zh-CN/实例/创建实例/使用向导创建实例.md)。

    **说明：** 用于运行远程证明客户端程序Shelter。

-   [通过kubectl连接Kubernetes集群](/cn.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl管理Kubernetes集群.md)。
-   在加密计算托管集群上的所有节点上，部署Inclavare Containers机密容器环境。具体操作，请参见[在加密计算托管集群中使用Inclavare Containers机密容器](/cn.zh-CN/Kubernetes集群用户指南/ACK-TEE机密计算/在加密计算托管集群中使用Inclavare Containers机密容器.md)。

EAA远程证明以关联了带有硬件可执行环境的Quote的TLS证书为信任根，确保通信双方的通信信道的安全性是完全基于硬件可信执行环境的。具体工作流程和架构图如下：

![AKK](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7403183261/p280617.png)

当您想验证工作负载是否运行在可信平台上时，可以启动Shelter验证工具发送验证请求给Inclavared对平台进行验证。具体工作流程如下：

1.  当Inclavared接收到Shelter的验证请求之后，将请求发送给机密容器，Inclavared和机密容器分别产生带有硬件可执行环境的Quote的TLS证书。
2.  Inclavared和Shelter之间基于Enclave-TLS建立经过证明的安全信道。
3.  Inclavared与机密容器之间基于Enclave-TLS建立经过双向证明过的安全信道。
4.  Inclavared转发机密容器提供的硬件可执行环境信息和敏感信息给Shelter。
5.  Shelter对Enclave运行时的度量值进行验证，并返回验证结果。

该架构包含以下组件：

|组件名称|角色|功能|
|----|--|--|
|机密容器|工作负载|在Occlum中运行服务端程序enclave-tls-server，并基于Enclave-TLS，响应来自Inclavared的请求，并返回机密容器的attestation evidence（`mrenclave`值和`mrsigner`值）。更多信息，请参见[occlum](https://github.com/occlum/occlum)和[enclave-tls](https://github.com/alibaba/inclavare-containers/tree/master/enclave-tls)。|
|Inclavared|证明者|负责转发下游的机密容器和上游的客户端验证者程序Shelter之间的流量，且受到经过证明的Enclave-TLS信道的保护。|
|Shelter|线下的远程证明验证者|1.  记录Enclave运行时的启动度量值。
2.  建立可信TLS信道与Inclavared进行通信。
3.  Shelter对Enclave运行时的度量值进行验证，以便您能够明确知道自己的工作负载是否在真正的TEE环境中加载。 |
|阿里云证书缓存服务PCCS（Provisioning Certificate Caching Service）|远程证明服务|阿里云SGX远程证明服务完全兼容Intel® SGX ECDSA远程证明服务和Intel® SGX SDK，因此阿里云vSGX实例（即g7t、c7t或r7t实例的简称）能够通过远程证明来获得远程提供商或生产者的信任。更多信息，请参见[Intel® SGX ECDSA远程证明服务和Intel® SGX SDK](https://software.intel.com/content/www/us/en/develop/topics/software-guard-extensions/attestation-services.html)。|

## 使用限制

-   仅支持对DaemonSet应用进行远程证明，即同一应用必须在所有节点上有且仅有一个工作负载。
-   为了能让远程证明客户端程序Shelter直接访问到工作负载，集群中每个节点都要绑定EIP。

## 步骤一：部署Inclavared

1.  执行以下命令，创建Inclavared Daemonset应用。

    ```
    cat <<-EOF | kubectl apply -f -
    apiVersion: apps/v1
    kind: DaemonSet
    metadata:
      name: inclavared
    spec:
      selector:
        matchLabels:
          k8s-app: inclavared
      template:
        metadata:
          labels:
            k8s-app: inclavared
        spec:
          affinity:
            nodeAffinity:
              requiredDuringSchedulingIgnoredDuringExecution:
                nodeSelectorTerms:
                - matchExpressions:
                  - key: alibabacloud.com/tee-hardware-category
                    operator: In
                    values:
                    - intel-sgx1
                    - intel-sgx2
          containers:
          - image: docker.io/inclavarecontainers/inclavared:latest
            imagePullPolicy: IfNotPresent
            securityContext:
              privileged: true
            name: inclavared
            command:
              - /usr/local/bin/inclavared
            args:
              - --listen
              - 0.0.0.0:1236
              - --xfer
              - 127.0.0.1:1234
              - --attester
              - sgx_ecdsa
              - --verifier 
              - sgx_ecdsa_qve
              - --mutual
            volumeMounts:
            - mountPath: /dev/sgx/enclave
              name: dev-enclave
            - mountPath: /dev/sgx/provision
              name: dev-provision
            - mountPath: /var/run/aesmd
              name: run-dir
            resources:
              requests:
                cpu: 100m
                memory: 100Mi
              limits:
                cpu: 1
                memory: 1000Mi
          tolerations:
          - effect: NoSchedule
            key: alibabacloud.com/sgx_epc_MiB
            operator: Exists
          volumes:
          - hostPath:
              path: /var/run/aesmd
              type: DirectoryOrCreate
            name: run-dir
          - hostPath:
              path: /dev/sgx_enclave
            name: dev-enclave
          - hostPath:
              path: /dev/sgx_provision
            name: dev-provision
          hostNetwork: true
    EOF
    ```

2.  执行以下命令，查看Inclavared Daemonset应用是否部署成功。

    ```
    kubectl get daemonset inclavared
    ```

    预期输出：

    ```
    NAME         DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
    inclavared   2         2         2       2            2           <none>          7d22h
    ```

    **说明：** Inclavared启动后：

    1.  在端口监听地址`0.0.0.0:1236`监听来自远程认证客户端Shelter的请求。
    2.  收到请求后，Inclavared会把请求转发给当前节点的工作负载。

## 步骤二：部署工作负载occlum-attestation-app

1.  执行以下命令，创建工作负载`occlum-attestation-app`。

    ```
    cat <<-EOF | kubectl apply -f -
    apiVersion: apps/v1
    kind: DaemonSet
    metadata:
      name: occlum-attestation-app
    spec:
      selector:
        matchLabels:
          k8s-app: occlum-attestation-app
      template:
        metadata:
          labels:
            k8s-app: occlum-attestation-app
        spec:
          affinity:
            nodeAffinity:
              requiredDuringSchedulingIgnoredDuringExecution:
                nodeSelectorTerms:
                - matchExpressions:
                  - key: alibabacloud.com/tee-hardware-category
                    operator: In
                    values:
                    - intel-sgx1
                    - intel-sgx2
          containers:
          - image: docker.io/inclavarecontainers/occlum-ecdsa-server:0.21.0
            imagePullPolicy: IfNotPresent
            securityContext:
              privileged: true
            name: occlum-attestation-app
            command:
              - /bin/enclave-tls-server
            args:
              - --ip
              - "127.0.0.1"    #本地机的IP地址。
              - --port
              - "1234"
              - --mutual
            env:
              - name: ENCLAVE_TYPE
                value: intelSgx
              - name: RUNE_CARRIER
                value: occlum
              - name: ENCLAVE_RUNTIME_LOGLEVEL
                value: info
              - name: ENCLAVE_RUNTIME_PATH
                value: /opt/occlum/build/lib/libocclum-pal.so.0.21.0
              - name: ENCLAVE_RUNTIME_ARGS
                value: occlum_workspace_server
              - name: OCCLUM_RELEASE_ENCLAVE
                value: "1"
            workingDir: /run/rune
            resources:
              requests:
                cpu: 100m
                memory: 100Mi
              limits:
                cpu: 1
                memory: 1000Mi
          tolerations:
          - effect: NoSchedule
            key: alibabacloud.com/sgx_epc_MiB
            operator: Exists
          hostNetwork: true
    EOF
    ```

2.  执行以下命令，查看`occlum-attestation-app`是否部署成功。

    ```
    kubectl get daemonset occlum-attestation-app
    ```

    预期输出：

    ```
    NAME                     DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
    occlum-attestation-app   2         2         2       2            2           <none>          7d22h
    ```

    从上述输出信息可知`occlum-attestation-app`已部署成功，工作负载中的`enclave-tls-server`程序，在端口监听地址`127.0.0.1:1234`监听来自客户端Inclavared的连接请求。


## 步骤三：修改集群安全组配置

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群信息管理页面，单击**集群资源**页签，然后单击安全组右侧的链接。

5.  修改默认安全组规则，允许入方向`1236`端口可访问。具体操作，请参见[修改安全组规则](/cn.zh-CN/安全/安全组/管理安全组规则/修改安全组规则.md)。


## 步骤四：绑定节点EIP

**说明：** 如果ECS实例已有公网IP，请跳过该步骤。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**节点管理** \> **节点**。

5.  在**节点**页面，单击目标节点的**实例ID**名称。

6.  在云服务器ECS控制台，选择**实例与镜像** \> **实例**，单击**实例详情**页签。

7.  单击**绑定弹性IP**为实例分配公网IP。

    ![akk1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/2760473261/p280664.png)

    -   如果已有弹性IP，在**绑定弹性IP**对话框中，从**弹性IP**下拉列表中选择目标弹性IP。
    -   如果没有弹性IP，在**绑定弹性IP**对话框中，单击**创建弹性公网IP**。具体操作，请参见[申请EIP](/cn.zh-CN/用户指南/申请EIP/申请新EIP.md)。

        ![jm11](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4630193261/p283199.png)


## 步骤五：安装远程证明客户端程序Shelter

1.  安装SGX PSW环境。

    **说明：** Shelter在验证嵌有SGX认证信息的TLS证书过程中，需要依赖SGX PSW提供的动态库。更多信息，请参见[SGX PSW](https://download.01.org/intel-sgx/sgx-linux/2.13/docs/Intel_SGX_Installation_Guide_Linux_2.13_Open_Source.pdf)。

    -   在CentOS 8.2系统上，执行以下命令，安装SGX PSW。

        ```
        yum install -y yum-utils && \
            wget -c https://download.01.org/intel-sgx/sgx-linux/2.13/distro/centos8.2-server/sgx_rpm_local_repo.tgz && \
            tar xzf sgx_rpm_local_repo.tgz && \
            yum-config-manager --add-repo sgx_rpm_local_repo && \
            yum makecache && rm -f sgx_rpm_local_repo.tgz && \
            yum install --nogpgcheck -y libsgx-dcap-quote-verify \
            libsgx-dcap-default-qpl libsgx-dcap-ql \
            libsgx-uae-service
        ```

    -   在Ubuntu 18.04系统上，执行以下命令，安装SGX PSW。

        ```
        apt-get update -y && apt-get install -y wget gnupg && \
            echo "deb [arch=amd64] https://download.01.org/intel-sgx/sgx_repo/ubuntu bionic main" | tee /etc/apt/sources.list.d/intel-sgx.list && \
            wget -qO - https://download.01.org/intel-sgx/sgx_repo/ubuntu/intel-sgx-deb.key | apt-key add - && \
            apt-get update -y && \
            apt-get install -y libsgx-dcap-quote-verify=1.10.100.4-bionic1 \
            libsgx-dcap-default-qpl=1.10.100.4-bionic1 \
            libsgx-dcap-ql=1.10.103.1-bionic1 \
            libsgx-uae-service=2.13.100.4-bionic1
        ```

2.  配置阿里云PCCS公网URL。

    阿里云SGX远程证明服务采用区域化部署，您可以通过访问实例所在地域的阿里云SGX远程证明服务来获得最佳的稳定性。您需要手动修改/etc/sgx\_default\_qcnl.conf文件以适配实例所在地域的阿里云SGX远程证明服务。

    **说明：** 仅中国内地地域支持阿里云SGX远程证明服务。更多信息，请参见[地域和可用区]()。

    -   如果vSGX实例已分配公网IP，/etc/sgx\_default\_qcnl.conf的内容需要做以下修改。

        ```
        # PCCS server address
        PCCS_URL=https://sgx-dcap-server.<Region-ID>.aliyuncs.com/sgx/certification/v3/
        # To accept insecure HTTPS cert, set this option to FALSE
        USE_SECURE_CERT=TRUE
        ```

    -   如果vSGX实例只有VPC内网IP，/etc/sgx\_default\_qcnl.conf的内容需要做以下修改。

        ```
        # PCCS server address
        PCCS_URL=https://sgx-dcap-server-vpc.<Region-ID>.aliyuncs.com/sgx/certification/v3/
        # To accept insecure HTTPS cert, set this option to FALSE
        USE_SECURE_CERT=TRUE
        ```

        **说明：** 您需要将<Region-ID\>替换为vSGX实例所在地域的ID。

3.  安装远程证明客户端程序Shelter。

    -   在CentOS 8.2系统上，执行以下命令，安装远程证明客户端程序Shelter。

        ```
        yum-config-manager --add-repo https://mirrors.openanolis.org/inclavare-containers/rpm-repo/ && \
            rpm --import https://mirrors.openanolis.org/inclavare-containers/rpm-repo/RPM-GPG-KEY-rpm-sign && \
            yum install -y shelter
        ```

    -   在Ubuntu 18.04系统上，执行以下命令，安装远程证明客户端程序Shelter。

        ```
        echo 'deb [arch=amd64] https://mirrors.openanolis.org/inclavare-containers/deb-repo bionic main' | tee /etc/apt/sources.list.d/inclavare-containers.list && \
            wget -qO - https://mirrors.openanolis.org/inclavare-containers/deb-repo/DEB-GPG-KEY.key  |  apt-key add - && \
            apt-get update -y && apt-get install -y shelter
        ```

    结果验证

    执行以下命令，查看Shelter是否安装成功。

    ```
    which shelter
    ```

    预期输出：

    ```
    /usr/local/bin/shelter
    ```


## 验证在加密计算托管集群中使用Inclavare Containers机密容器实现远程证明是否成功

向集群中Woker节点依次执行以下命令，运行远程证明客户端程序Shelter。

**说明：** 执行命令前请将<$IP\>替换为Woker节点的EIP。

```
shelter remoteattestation --verifier sgx_ecdsa --tls openssl --crypto openssl --addr=tcp://<$IP>:1236
```

-   如果在SGX环境中运行Shelter，预期输出如下。

    ![jm67](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9311465261/p282846.png)

    如果输出结果中包含`Remote attestation is successful`，则表明您已成功实现远程证明。

-   如果在非SGX环境中运行Shelter，除了会输出上述包含`Remote attestation is successful`的信息，还会输出以下错误信息。

    ```
    [load_qve ../sgx_dcap_quoteverify.cpp:209] Error, call sgx_create_enclave for QvE fail [load_qve], SGXError:2006.
    [sgx_qv_get_quote_supplemental_data_size ../sgx_dcap_quoteverify.cpp:527] Error, failed to load QvE.
    ```

    上述输出的错误信息您可以忽略，因为其对最终的证明结果没有任何影响。

    出现该错误的原因是：Shelter在调用[sgx\_qv\_get\_quote\_supplemental\_data\_size\(\)](https://download.01.org/intel-sgx/sgx-dcap/1.3/linux/docs/Intel_SGX_ECDSA_QuoteLibReference_DCAP_API.pdf)时，总会先尝试加载QvE（Quote Verification Enclave）。如果加载失败（所有非SGX环境都无法成功加载QvE），Shelter会输出上述错误信息，然后会自动使用QVL（Quote Verification Library）进行后续证明逻辑。在非SGX环境中，验证过程都是由QVL处理。更多信息，请参见[Intel® SGX ECDSA远程证明](https://api.portal.trustedservices.intel.com/provisioning-certification)。


