---
keyword: [调度Pod, 节点标签, 配置模板]
---

# 调度应用Pod至指定节点

由于业务场景需要，有时您需要将服务部署到特定节点，或者将某些服务部署到具有SSD盘的节点上。通过容器服务管理控制台，您可以快速地将Pod调度到指定的节点上。本文介绍如何通过设置节点标签、配置模板等操作，将Pod调度到指定节点上。

[创建Kubernetes托管版集群](/intl.zh-CN/Kubernetes集群用户指南/集群/创建集群/创建Kubernetes托管版集群.md)。

您可通过设置节点标签，然后通过配置`nodeSelector`强制约束Pod调度，将Pod调度到指定的Node节点上。关于nodeSelector的详细实现原理，请参见[nodeselector](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#nodeselector)。

## 步骤一：设置节点标签

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**节点管理** \> **节点**。

5.  在页面右上角单击**标签与污点管理**，进入标签与污点管理页面。

6.  在标签页签中，选择目标节点，然后单击左下角的**添加标签**。

7.  在弹出的添加对话框中，输入标签的**名称**和**值**，然后单击**确定**。

    -   **名称**：标签名称由字母、数字、短划线（-）、下划线（\_）、小数点（.）组成，且必须以字母或者数字开头和结尾。
    -   **值**：标签值可以为空字符串或由字母、数字、短划线（-）、下划线（\_）、小数点（.）组成，且必须以字母或者数字开头和结尾。

## 步骤二：部署Pod到指定节点

1.  在控制台左侧导航栏中，单击**集群**。

2.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

3.  在集群管理页左侧导航栏中，选择**工作负载** \> **无状态**。

4.  在**无状态**页面，单击页面右上角的**使用YAML创建资源**。

5.  对模板进行相关配置，部署一个Pod。

    -   **命名空间**：选择资源对象所属的命名空间，本示例中设置命名空间为**default**。
    -   **示例模板**：本示例选择自定义模板。
    本示例的YAML模板如下。

    ```
     apiVersion: v1
     kind: Pod
     metadata:
       labels:
         name: hello-pod
       name: hello-pod
     spec:
       containers:
         - image: nginx
           imagePullPolicy: IfNotPresent
           name: hello-pod
           ports:
             - containerPort: 8080
               protocol: TCP
           resources: {}
           securityContext:
             capabilities: {}
             privileged: false
           terminationMessagePath: /dev/termination-log
       dnsPolicy: ClusterFirst
       restartPolicy: Always
       nodeSelector:                    
         group: worker                           ##注意与前面配置的节点标签一致。
       status: {}
    ```

6.  单击**创建**后，会提示部署状态信息。


## 验证结果

您可以选择以下任意一种方式查看Pod是否已成功部署到指定节点上。

**方式一：部署成功后单击页面Pod名称**

1.  部署成功后，单击页面底部提示信息中的Pod名称，进入容器组页面。

    ![创建Pod](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3606194161/p245822.png)

2.  在容器组页面，单击目标Pod名称，进入该Pod的详情页。

    您可看到Pod的标签、所处的节点ID等信息，表明该Pod已经成功部署到具有`group:worker`标签的指定节点上。

    ![查看pod详情](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3085659951/p10934.png)


**方式二：容器组页面查找目标容器**

1.  在控制台左侧导航栏中，单击**集群**。

2.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

3.  在集群管理页左侧导航栏中，选择**工作负载** \> **容器组**。

4.  在容器组页面，单击目标Pod名称，进入该Pod的详情页。

    您可看到Pod的标签、所处的节点ID等信息，表明该Pod已经成功部署到具有`group:worker`标签的指定节点上。


