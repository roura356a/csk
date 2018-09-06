# 在 pod 中使用配置项 {#concept_e2n_vkn_vdb .concept}

您可以在 Pod 中使用配置项，有多种使用场景，主要包括：

-   使用配置项定义 pod 环境变量
-   通过配置项设置命令行参数
-   在数据卷中使用配置项

更多关于配置项的信息，可以参见 [Configure a Pod to Use a ConfigMap](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/)。

## 使用限制 {#section_gtv_cln_vdb .section}

您在 pod 里使用配置项时，需要两者处于同一集群和命名空间中。

## 创建配置项 {#section_htv_cln_vdb .section}

本示例创建配置项 special\_config，包含 `SPECIAL_LEVEL: very` 和 `SPECIAL_TYPE: charm` 两个键值对。

**使用编排模板创建配置项**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在 Kubernetes 菜单下，单击左侧导航栏中的**应用** \> **部署**，然后单击右上角的**使用模板创建**。
3.  选择所需的集群和命名空间，选择样例模板或自定义，然后单击**创建**。

    您可以使用如下 yaml 示例模板创建配置项。

    ```
    apiVersion: v1
    kind: ConfigMap
    metadata:
       name: special-config
       namespace: default
    data:
       SPECIAL_LEVEL: very
       SPECIAL_TYPE: charm
    ```


**通过 Web 界面创建配置项**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在 Kubernetes 菜单下，单击左侧导航栏中的**应用** \> **配置项**，进入配置项列表页面。
3.  选择所需的集群和命名空间，然后单击右上角的**使用模板创建**。
4.  输入配置项名称，然后单击**添加**，输入配置项，最后单击**确定**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16702/153622168710753_zh-CN.png)


## 使用配置项定义 pod 环境变量 {#section_x41_rnn_vdb .section}

**使用配置项的数据定义 pod 环境变量**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在 Kubernetes 菜单下，单击左侧导航栏中的**应用** \> **部署**，然后单击右上角的**使用模板创建**。
3.  选择所需的集群和命名空间，选择样例模板或自定义，然后单击**创建**。

    您可以在 pod 中定义环境变量，使用 `valueFrom` 引用 SPECIAL\_LEVEL 的 value 值，从而定义 pod 的环境变量。

    下面是一个编排示例。

    ```
    apiVersion: v1
    kind: Pod
    metadata:
       name: config-pod-1
    spec:
       containers:
         - name: test-container
           image: busybox
           command: [ "/bin/sh", "-c", "env" ]
           env:
             - name: SPECIAL_LEVEL_KEY
               valueFrom:                             ##使用valueFrom来指定env引用配置项的value值
                 configMapKeyRef:
                   name: special-config               ##引用的配置文件名称
                   key: SPECIAL_LEVEL                 ##引用的配置项key
       restartPolicy: Never
    ```


同理，如果您需要将多个配置项的 value 值定义为 pod 的环境变量值，您只需要在 pod 中添加多个 env 参数即可。

**将配置项的所有 key/values 配置为 pod 的环境变量**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在 Kubernetes 菜单下，单击左侧导航栏中的**应用** \> **部署**，然后单击右上角的**使用模板创建**。
3.  选择所需的集群和命名空间，选择样例模板或自定义，然后单击**创建**。

    如果您想在一个 pod 中将配置项的所有 key/values 键值对配置为 pod 的环境变量，可以使用 envFrom 参数，配置项中的 key 会成为 Pod 中的环境变量名称。

    下面是一个编排示例。

    ```
    apiVersion: v1
    kind: Pod
    metadata:
       name: config-pod-2
    spec:
       containers:
         - name: test-container
           image: busybox
           command: [ "/bin/sh", "-c", "env" ]
           envFrom:                ##引用 sepcial-config 配置文件的所有 key/values 键值对
           - configMapRef:
               name: special-config
       restartPolicy: Never
    ```


**通过配置项设置命令行参数**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在 Kubernetes 菜单下，单击左侧导航栏中的**应用** \> **部署**，然后单击右上角的**使用模板创建**。
3.  选择所需的集群和命名空间，选择样例模板或自定义，然后单击**创建**。

    您可以使用配置项设置容器中的命令或者参数值，使用环境变量替换语法 `$(VAR_NAME)`来进行。

    下面是一个编排示例。

    ```
    apiVersion: v1
    kind: Pod
    metadata:
       name: config-pod-3
    spec:
       containers:
         - name: test-container
           image: busybox
           command: [ "/bin/sh", "-c", "echo $(SPECIAL_LEVEL_KEY) $(SPECIAL_TYPE_KEY)" ]
           env:
             - name: SPECIAL_LEVEL_KEY
               valueFrom:
                 configMapKeyRef:
                   name: special-config
                   key: SPECIAL_LEVEL
             - name: SPECIAL_TYPE_KEY
               valueFrom:
                 configMapKeyRef:
                   name: special-config
                   key: SPECIAL_TYPE
       restartPolicy: Never
    ```

    运行这个 pod 后，会输出如下结果。

    ```
    very charm
    ```


## 在数据卷中使用配置项 {#section_qtn_wkn_vdb .section}

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在 Kubernetes 菜单下，单击左侧导航栏中的**应用****部署**，然后单击右上角的**使用模板创建**。
3.  选择所需的集群和命名空间，选择样例模板或自定义，然后单击**创建**。

    您也可以在数据卷里面使用配置项，在 volumes 下指定配置项名称，会将 key/values 的数据存储到 mountPath 路径下（本例中是 /etc/config）。最终生成以 key 为文件名，values 为文件内容的配置文件。

    下面是一个编排示例。

    ```
    apiVersion: v1
    kind: Pod
    metadata:
       name: config-pod-4
    spec:
       containers:
         - name: test-container
           image: busybox
           command: [ "/bin/sh", "-c", "ls /etc/config/" ]   ##列出该目录下的文件名
           volumeMounts:
           - name: config-volume
             mountPath: /etc/config
       volumes:
         - name: config-volume
           configMap:
             name: special-config
       restartPolicy: Never
    ```

    运行 pod 后，会输出配置项的 key。

    ```
    SPECIAL_TYPE
    SPECIAL_LEVEL
    ```


