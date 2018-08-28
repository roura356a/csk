# Jenkins CI/CD on Kubernetes with dynamic slaves {#concept_x33_btb_wdb .concept}

本文档介绍如何通过在 Kubernetes 集群上创建并配置 Jenkins Server 实现应用开发管理的 CI/CD 流程，并且利用 Kubernetes-Jenkins-Plugin 实现动态按需扩展 jenkins-slave。

## 步骤 1 安装 Kubernetes 集群 {#section_pjk_ctb_wdb .section}

如果您没有 Kubernetes 集群，您需要先创建一个。具体操作参见[创建Kubernetes集群](../../../../cn.zh-CN/用户指南/Kubernetes 集群/集群管理/创建Kubernetes集群.md#) 。

## 步骤 2 连接 Kubernetes 集群 {#section_qjk_ctb_wdb .section}

有关如何连接到 Kubernetes 集群，参见 [通过 kubectl 连接 Kubernetes 集群](../../../../cn.zh-CN/用户指南/Kubernetes 集群/集群管理/通过 kubectl 连接 Kubernetes 集群.md#)。

## 步骤 3 创建 NAS 持久化卷 {#section_rjk_ctb_wdb .section}

**说明：** 为了让您的 Jenkins Server 可以具有 Fail Over 的能力，建议您将 Jenkins 的数据存储到阿里云 NAS 存储上。

有关如何创建 Kubernetes 持久化 NAS 存储卷，参见[使用阿里云 NAS](../../../../cn.zh-CN/用户指南/Kubernetes 集群/存储管理/使用阿里云 NAS.md#)。

假设您已经在阿里云控制台上面创建了您自己的 NAS 存储，其挂载点为 `yournas-xxxx.cn-hangzhou.nas.aliyuncs.com`。执行下面的命令下载一个 persistent volume 模板，替换里面的 server 的值为您的挂载点，然后执行 `kubectl apply -f pv.yml` 创建持久化 NAS 卷。

```
root@iZbp:~/jenkins# curl -L http://aliacs-k8s.oss.aliyuncs.com/conf%2Fjenkins%2Fpersistent-volume.yml > pv.yml
root@iZbp:~/jenkins# vi pv.yml
root@iZbp:~/jenkins# kubectl apply -f pv.yml
root@iZbp:~/jenkins# kubectl get pv -n ci
```

## 步骤 4 创建 Jenkins Server {#section_tjk_ctb_wdb .section}

通过以下命令启动一个 Jenkins Server。该命令会创建一个阿里云公网负载均衡，方便您从公网访问 Jenkins 服务。如果不需要公网访问请将 Service 的 type 更改为 NodePort。

```
root@iZbp:~/jenkins# curl -L http://aliacs-k8s.oss.aliyuncs.com/conf%2Fjenkins%2Fjenkins.yml > jenkins.yml
root@iZbp:~/jenkins# kubectl apply -f jenkins.yml
root@iZbp:~/jenkins# kubectl get po -n ci
root@iZbp:~/jenkins# kubectl get svc -n ci
NAME                   CLUSTER-IP       EXTERNAL-IP   PORT(S)         AGE
jenkins                172.19.0.10      40.112.120.10 53/UDP,53/TCP   34m
```

然后，您就可以通过地址 `http://40.112.120.10` 访问 Jenkins 服务了。

## 步骤 5 配置 Jenkins {#section_iqq_bb1_12b .section}

1.  访问上面的 Jenkins 地址 `http://40.112.120.10`，如下图所示。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6950/15354406634720_zh-CN.png)

2.  您需要使用 token 来初始化 Jenkins。该 token 文件存放在 secrets/initialAdminPassword 下，您需要手动挂载 NAS 到本地目录后查看 token 的内容。您需要将以下命令中的 `yournas-xxxx.cn-hangzhou.nas.aliyuncs.com`替换为您自己的挂载点。

    ```
    root@iZbp:~/jenkins# mkdir -p mnt
    root@iZbp:~/jenkins# mount.nfs yournas-xxxx.cn-hangzhou.nas.aliyuncs.com:/ mnt
    root@iZbp:~/jenkins# cat mnt/secrets/initialAdminPassword
    7b48377b16ef49a8ba678031dd12111a
    ```

    **说明：** 如果提示未找到 `mount.nfs` 命令，则需要先安装 `nfs-utils` 包。

3.  将上面输出的 token 粘贴到界面上的输入框中并单击**Continue**。
4.  单击**Select plugins to install**，安装插件。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6950/15354406634721_zh-CN.png)

5.  由于连接国外的网络问题，ant 插件不一定能下载成功，建议您先去掉 ant 插件安装。单击**Install**并等待安装完成。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6950/15354406634722_zh-CN.png)

6.  设置用户名和密码，并单击**Save and Finish**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6950/15354406634723_zh-CN.png)

    至此，您就成功的创建了一个 Jenkins server。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6950/15354406634724_zh-CN.png)


## 步骤 6 配置 Kubernetes-jenkins-plugin 实现 dynamic slaves provision {#section_fkk_ctb_wdb .section}

1.  安装 Kubernetes-plugin。
    1.  单击**系统管理** \> **管理插件** \> **可选插件**，搜索 `kubernetes plugin`，勾选要安装的插件，然后单击**直接安装**。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6950/15354406634725_zh-CN.png)

    2.  勾选**安装完成后重启 Jenkins**。等待重启完成后 kubernetes-plugin 就安装完成了。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6950/15354406634726_zh-CN.png)

2.  配置 Jenkins kubernetes-plugin 插件。
    1.  单击**系统管理** \> **系统设置**，找到云，然后单击**新增一个云**。
    2.  填写配置参数，并单击**Apply**。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6950/15354406644727_zh-CN.png)

        **其中：**

        -   **Name**：kubernetes
        -   **kubernetes URL**：`https://kubernetes.default.svc.cluster.loca`l
        -   **Disable https certificate check**：true
        -   **Jenkins URL**：`http://jenkins-svc.ci.svc.cluster.local`
        -   **Connect Timeout**：5
        -   **Read Timeout**:15

这样就配置好了 kubernetes-plugin，可以实现动态 jenkins-slaves in pod.

## 步骤 7 Jenkins CI {#section_kqj_db1_12b .section}

1.  新建一个 Pipeline 任务。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6950/15354406644728_zh-CN.png)

2.  将下面的内容粘贴到 pipeline Script 里，并单击**保存**。

    ```
    podTemplate(label: 'golang-pod',  containers: [
     containerTemplate(
             name: 'golang',
             image: 'registry.cn-hangzhou.aliyuncs.com/spacexnice/golang:1.8.3-docker',
             ttyEnabled: true,
             command: 'cat'
         ),
     containerTemplate(
             name: 'jnlp',
             image: 'registry.cn-hangzhou.aliyuncs.com/google-containers/jnlp-slave:alpine',
             args: '${computer.jnlpmac} ${computer.name}',
             command: ''
         )
    ]
    ,volumes: [
         /*persistentVolumeClaim(mountPath: '/home/jenkins', claimName: 'jenkins', readOnly: false),*/
         hostPathVolume(hostPath: '/root/work/jenkins', mountPath: '/home/jenkins'),
         hostPathVolume(hostPath: '/var/run/docker.sock', mountPath: '/var/run/docker.sock'),
         hostPathVolume(hostPath: '/tmp/', mountPath: '/tmp/'),
    ]) 
    {
     node ('golang-pod') {
         container('golang') {
             git url: 'https://github.com/spacexnice/blog.git' , branch: 'code'
             stage('Build blog project') {
                 sh("make")
             }
         }
     }
    }
    ```

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6950/15354406644729_zh-CN.png)

3.  单击**开始构建**。

    通过构建日志观察任务的执行情况，您会发现 Jenkins Server 会通过 Kubernetes 启动一个 Pod 作为 Jenkins slave 执行构建 Pipeline。


