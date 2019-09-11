# 如何使用BYOK创建加密云盘 {#task_2117903 .task}

阿里云容器服务Kubernetes集群如何使用BYOK创建加密云盘。

使用BYOK创建加密云盘时，系统需要使用同一地域的密钥管理服务（KMS）提供的BYOK（Bring Your Own Key）。因此，首次通过控制台或者API使用云盘加密功能之前，您必须先开通[密钥管理服务](https://kms.console.aliyun.com/)请参见[开通方法](../../../../cn.zh-CN/产品定价/开通方法.md#)

当您的业务因为安全需求或法规合规要求等原因，需要对存储在云盘上的数据进行加密保护时，您可以使用阿里云ECS云盘加密功能，无需构建、维护和保护自己的密钥管理基础设施，即可保护数据的隐私性和自主性。

本文中的云盘指代云盘和共享块存储中的数据盘。下文中统一简称为云盘，除非特别指出。

## 创建BYOK {#section_yxl_ugq_maa .section}

创建BYOK并记录密钥ID。

1.  登录[KMS 控制台](https://kms.console.aliyun.com)。
2.  在控制台左上角处，单击**创建密钥**。
3.  在弹出的创建密钥对话框中，填写**别名**和**描述**。
4.  在**高级选项**下，选择**密钥材料来源**。 
    -   选择**阿里云KMS**：密钥材料将由KMS生成。
    -   选择**外部**：KMS将不会生成密钥材料，您需要将自己的密钥材料导入。详情请参见[导入密钥材料](../../../../cn.zh-CN/用户指南/导入密钥材料.md#)。
5.  单击**确定**。

## 创建StorageClass使用BYOK {#section_9v6_gcq_kz2 .section}

在容器服务Kubernetes集群中新建StorageClass，并配置`encrypted: "true" kmsKeyId: <your BYOK>`。

1.  [通过 kubectl 连接 Kubernetes 集群](../../../../cn.zh-CN/Kubernetes集群用户指南/集群管理/管理与访问集群/通过 kubectl 连接 Kubernetes 集群.md#)。
2.  执行如下命令，打开您的storageclass-ssd-byok.yaml文件。 

    ``` {#codeblock_wqm_x4v_f9q}
    $ cat storageclass-ssd-byok.yaml
    apiVersion: storage.k8s.io/v1
    kind: StorageClass
    metadata:
      name: alicloud-disk-ssd-byok
    parameters:
      type: cloud_ssd
      encrypted: "true"
      kmsKeyId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    provisioner: alicloud/disk
    reclaimPolicy: Delete
    ```

    **说明：** `kmsKeyId`需要替换成[创建BYOK](#section_yxl_ugq_maa)中填写的密钥ID。

3.  执行如下命令，创建StorageClass。 

    ``` {#codeblock_m43_u7k_jw7}
    kubectl apply -f storageclass-ssd-byok.yaml
    ```

4.  执行如下命令，查看StorageClass。 

    ``` {#codeblock_a6h_uhd_4fx}
    $ kubectl get sc
    
    NAME                       PROVISIONER     AGE
    alicloud-disk-available    alicloud/disk   19m
    alicloud-disk-efficiency   alicloud/disk   19m
    alicloud-disk-essd         alicloud/disk   19m
    alicloud-disk-ssd          alicloud/disk   19m
    alicloud-disk-ssd-byok     alicloud/disk   28s
    ```


## 创建示例应用并挂载云盘 {#section_poo_syf_uox .section}

1.  执行如下命令，打开您的deploy.yaml文件。 

    ``` {#codeblock_ksw_ibd_oiu}
    $ cat deploy.yaml
    kind: PersistentVolumeClaim
    apiVersion: v1
    metadata:
      name: disk-ssd-byok
    spec:
      accessModes:
        - ReadWriteOnce
      storageClassName: alicloud-disk-ssd-byok
      resources:
        requests:
          storage: 20Gi
    ---
    kind: Pod
    apiVersion: v1
    metadata:
      name: disk-pod-ssd-byok
    spec:
      containers:
      - name: disk-pod-byok
        image: nginx
        volumeMounts:
          - name: disk-pvc-byok
            mountPath: "/mnt"
      restartPolicy: "Never"
      volumes:
        - name: disk-pvc-byok
          persistentVolumeClaim:
            claimName: disk-ssd-byok
    ```

2.  执行如下命令，创建应用。 

    ``` {#codeblock_ssh_opx_xoa}
    kubectl apply -f deploy.yaml
    ```

3.  执行如下命令，查看PV。 

    ``` {#codeblock_z2a_e1w_jfe}
    $ kubectl get pv
    NAME                     CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                   STORAGECLASS             REASON   AGE
    d-j6c5jezzajt9ri47lvjs   20Gi       RWO            Delete           Bound    default/disk-ssd-byok   alicloud-disk-ssd-byok            8s
    ```

4.  登录[ECS管理控制台](https://ecs.console.aliyun.com)，单击左侧导航栏的**存储与快照** \> **云盘**，在磁盘列表中， 查看磁盘ID为 `d-j6c5jezzajt9ri47lvjs`的云盘是否已加密。 

    ![磁盘加密](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1680593/156818248759816_zh-CN.png)


更多关于云盘加密以及BYOK的介绍请参见[云盘加密](../../../../cn.zh-CN/块存储/云盘/ECS云盘加密.md#)。

