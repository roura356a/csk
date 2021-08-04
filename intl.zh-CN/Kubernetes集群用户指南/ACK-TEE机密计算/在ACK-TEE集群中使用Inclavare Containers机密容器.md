---
keyword: [Inclavare Containers, 机密容器, ACK-TEE]
---

# 在ACK-TEE集群中使用Inclavare Containers机密容器

Inclavare Containers是工业界首个面向机密计算场景的开源容器运行时，它在基于硬件的可信执行环境中启动受保护的容器，以防止不受信任的实体（例如：云服务商）访问您的敏感数据。在ACK-TEE集群中，您可以基于Inclavare Containers灵活、便捷地部署机密容器。本文主要为您介绍如何在ACK-TEE集群中基于Inclavare Containers部署和使用机密容器（简称Inclavare Containers机密容器）。

-   [创建加密计算托管集群](/intl.zh-CN/Kubernetes集群用户指南/ACK-TEE机密计算/创建加密计算托管集群.md)。

    **说明：** 目前只有**规格族**为**安全增强计算型c7t**的实例支持ACK-TEE集群，请确保所选可用区有这些实例。

-   [通过kubectl连接Kubernetes集群](/intl.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl管理Kubernetes集群.md)。

在ACK-TEE集群中虽然也可以使用常规技术部署和使用机密容器，但您需要掌握机密计算领域的专业知识，并按照SGX SDK应用开发规范开发和构建镜像。而Inclavare Containers能帮您省去这些复杂过程，降低机密计算的使用门槛，灵活对接不同类型的Enclave运行时形态，同时保持和普通容器一样的使用体验。更多信息，请参见[inclavare-containers](https://github.com/alibaba/inclavare-containers)。

## 步骤一：搭建Inclavare Containers机密容器运行环境

**说明：** 由于Pod的调度具有随机性，如果在部署Inclavare Containers机密容器时，您不通过打标签的方式定义被调度Pod所属的节点，则需要在每一个节点搭建Inclavare Containers机密容器的运行环境。

1.  执行以下命令，安装`rune`和`shim-rune`。更多信息，请参见[rune](https://github.com/alibaba/inclavare-containers/tree/master/rune)和[shim-rune](https://github.com/alibaba/inclavare-containers/tree/master/shim)。

    **说明：**

    -   `rune`是符合OCI Runtime规范的命令行工具，用于在容器里创建和运行Enclave的命令行工具。更多信息，请参见[runtime-container](https://github.com/opencontainers/runtime-spec/blob/master/implementations.md#runtime-container)。
    -   `shim-rune`是为容器运行时`rune`提供的`shim`，在普通`shim`功能的基础上额外提供了Enclave签名和管理功能。
    ```
    yum-config-manager --add-repo https://mirrors.openanolis.org/inclavare-containers/alinux2-repo && \
      rpm --import https://mirrors.openanolis.org/inclavare-containers/alinux2-repo/RPM-GPG-KEY-rpm-sign && \
      yum install -y rune shim-rune
    ```

2.  配置容器引擎Containerd。

    **说明：** ACK-TEE机密计算容器引擎Containerd默认的OCI Runtime是runC，而运行机密容器需要用新的容器运行时runE。

    1.  执行以下命令，配置OCI Runtime runE运行时。

        ```
        cd /etc/containerd/ && \
          sed -i 's/\(default_runtime_name = \)"runc"/\1"rune"/' config.toml && \
          sed -i '/\[plugins."io.containerd.grpc.v1.cri".containerd.runtimes\]/a\\t[plugins."io.containerd.grpc.v1.cri".containerd.runtimes.rune]' config.toml && \
          sed -i '/\[plugins."io.containerd.grpc.v1.cri".containerd.runtimes.rune\]/a\\t  runtime_type = "io.containerd.rune.v2"' config.toml
        ```

    2.  执行以下命令，重启Containerd。

        ```
        systemctl restart containerd.service
        ```

    3.  执行以下命令，查看Containerd是否启动成功。

        ```
        systemctl status containerd.service
        ```

        预期输出：

        ```
        containerd.service - containerd container runtime
           Loaded: loaded (/usr/lib/systemd/system/containerd.service; enabled; vendor preset: disabled)
           Active: active (running) since 二 2021-06-15 19:16:19 CST; 5s ago
             Docs: https://containerd.io
          Process: 212462 ExecStartPre=/sbin/modprobe overlay (code=exited, status=0/SUCCESS)
         Main PID: 212464 (containerd)
            Tasks: 288
           Memory: 6.3G
           ...
        ```

3.  执行以下命令，安装Occlum软件栈。

    **说明：** Occlum是Inclavare Containers当前支持的Enclave运行时，而Inclavare Containers需要Enclave运行时的配合才能运行机密容器。

    ```
    yum install -y occlum-pal occlum-rdfsbase-dkms
    ```

4.  执行以下命令，查看`occlum-pal`是否安装成功。

    ```
    ls /opt/occlum/build/lib/
    ```

    预期输出：

    ```
    libocclum-pal.so.0.21.0
    ```

5.  执行以下命令，查看`rdfsbase`驱动是否安装成功。

    ```
    lsmod | grep enable_rdfsbase
    ```

    预期输出：

    ```
    enable_rdfsbase        16384  0
    ```


## 步骤二：构建Inclavare Containers机密容器镜像

**说明：**

-   步骤1~5：基于Occlum的开发镜像构建并打包Hello World可信应用。
-   步骤6：构建包含Hello World可信应用的机密容器镜像。

1.  执行以下命令，运行Occlum开发环境`occlum-app-builder`。

    **说明：** `docker.io/occlum/occlum`镜像版本需要与安装的Occlum版本一致。

    ```
    cat << EOF | kubectl apply -f -
    apiVersion: v1
    kind: Pod
    metadata:
      labels:
        run: occlum-app-builder
      name: occlum-app-builder
      namespace: default
    spec:
      hostNetwork: true
      containers:
      - command:
        - sleep
        - infinity
        image: docker.io/occlum/occlum:0.21.0-centos8.2
        imagePullPolicy: IfNotPresent
        securityContext:
          privileged: true
        name: occlum-app-builder
    EOF
    ```

2.  执行以下命令，登录到Occlum开发环境`occlum-app-builder`容器。

    ```
    kubectl exec -it occlum-app-builder -c occlum-app-builder -- /bin/bash
    ```

3.  在Occlum开发环境`occlum-app-builder`内安装Docker。

    1.  安装Docker。具体安装操作，请参见[Install Docker](https://docs.docker.com/engine/install/centos/)。

    2.  Docker安装完成后，执行以下命令启动Docker服务。

        ```
        nohup dockerd -b docker0 --storage-driver=vfs &
        ```

        **说明：** 在默认情况下Systemd未安装在容器中，因此您无法通过Systemd管理Docker服务，需要用`nohup dockerd -b docker0 --storage-driver=vfs &`来启动Docker服务。

    3.  执行以下命令，确认Docker服务成功启动。

        ```
        docker ps
        ```

        预期输出：

        ```
        CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
        ```

4.  使用以下C代码样例，编写Hello World代码。

    ```
    cat << EOF > hello_world.c
    #include <stdio.h>
    #include <unistd.h>
    
    void main(void)
    {
        while (1) {
            printf("Hello World!\n");
            fflush(stdout);
            sleep(5);
        }
    }
    EOF
    ```

5.  构建并打包Hello World可信应用。

    1.  执行下面命令，使用Occlum工具链编译程序。

        ```
        occlum-gcc -o hello_world hello_world.c
        ```

    2.  执行下面命令，初始化`occlum_instance`。

        ```
        occlum new occlum_instance
        ```

    3.  执行下面命令，生成Occlum文件系统镜像和Occlum SGX Enclave。

        ```
        cd occlum_instance && \
            cp ../hello_world image/bin/ && \
            openssl genrsa -aes128 -out occlum_key.pem -3 3072 && \
            occlum build --sign-key occlum_key.pem
        ```

    4.  执行下面命令，打包Hello World可信应用。

        ```
        occlum package occlum_instance.tar.gz
        ```

6.  构建机密容器镜像。

    1.  使用以下Dockerfile模板样例，创建名为Dockerfile的文件。

        ```
        cat << EOF >Dockerfile
        FROM scratch
        ADD occlum_instance.tar.gz /
        ENTRYPOINT ["/bin/hello_world"]
        EOF
        ```

    2.  执行以下命令，构建并上传镜像。

        ```
        docker build -f Dockerfile -t "<$TARGET_IMAGE>" .
        docker login -p <$password> -u <$username>
        docker push "<$TARGET_IMAGE>"
        ```

        **说明：**

        -   变量<$password\>、<$username\>分别表示登录`docker hub`的用户名和密码。
        -   变量<$TARGET\_IMAGE\>表示镜像地址。

## 步骤三：使用Inclavare Containers机密容器

1.  从Occlum开发环境`occlum-app-builder`中退出，在集群中执行以下命令，创建RuntimeClass对象runC和runE，从而您就可以在ACK-TEE集群中部署机密容器。

    ```
    cat << EOF | kubectl apply -f -
    apiVersion: node.k8s.io/v1beta1
    handler: runc
    kind: RuntimeClass
    metadata:
      name: runc
    ---
    apiVersion: node.k8s.io/v1beta1
    handler: rune
    kind: RuntimeClass
    metadata:
      name: rune
    EOF
    ```

2.  执行以下命令，部署机密容器。

    ```
    cat << EOF | kubectl apply -f -
    apiVersion: v1
    kind: Pod
    metadata:
      labels:
        run: occlum-helloworld
      name:  occlum-helloworld
      namespace: default
    spec:
      restartPolicy: Always
      runtimeClassName: rune
      containers:
        - command:
          - /bin/hello_world
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
              value: occlum_instance
          image: <$TARGET_IMAGE>     
          imagePullPolicy: IfNotPresent
          name: hello-world-client
      dnsPolicy: ClusterFirst
    EOF
    ```

    上述YAML中涉及的参数含义如下：

    |参数|描述|
    |--|--|
    |**ENCLAVE\_TYPE**|指定要使用的机密计算硬件的类型。|
    |**RUNE\_CARRIER**|`shim-rune`创建和运行Occlum应用。|
    |**ENCLAVE\_RUNTIME\_LOGLEVEL**|指定运行时的日志级别；允许设置的值有：trace、debug、info、warning、error、fatal、panic和off。|
    |**ENCLAVE\_RUNTIME\_PATH**|指定启动Enclave运行时的路径。|
    |**ENCLAVE\_RUNTIME\_ARGS**|指定启动Enclave运行时的参数。|
    |**TARGET\_IMAGE**|在步骤二第6步中自定义的容器镜像名称。|


## 验证在ACK-TEE集群中使用Inclavare Containers机密容器是否成功运行

执行以下命令，查看容器运行日志。

```
kubectl logs -f occlum-helloworld
```

预期输出：

```
Hello World!
Hello World!
Hello World!
```

如果容器日志每隔5s打印一行`Hello World!`，则表示Inclavare Containers环境安装成功且机密容器也被正确运行了。

**说明：** 如果您有关于任何Inclavare Containers机密容器的问题，请在[issues](https://github.com/alibaba/inclavare-containers/issues)中留言 。

