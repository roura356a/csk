# 使用 Grafana 展示监控数据 {#task_fds_wqb_wdb .task}

-   您已经成功部署一个 Kubernetes 集群，参见[创建Kubernetes集群](intl.zh-CN/用户指南/Kubernetes集群/集群管理/创建Kubernetes集群.md#)。
-   本示例使用的 Grafana 的镜像地址是 `registry.cn-hangzhou.aliyuncs.com/acs/grafana:5.0.4`，内置了相关监控模板。

在 kubernetes 的监控方案中，Heapster+Influxdb+Grafana 的组合相比 prometheus 等开源方案而言更为简单直接。而且 Heapster 在 kubernetes 中承担的责任远不止监控数据的采集，控制台的监控接口、HPA的 POD 弹性伸缩等都依赖于 Heapster 的功能。因此 Heapster 成为 kubernetes 中一个必不可少的组件，在阿里云的 Kubernetes 集群中已经内置了 Heapster+Influxdb 的组合，如果需要将监控的数据进行展示，只需要配置一个可用的 Grafana 与相应的 Dashboard 即可。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在 Kubernetes 菜单下，单击左侧导航栏中的**应用** \> **无状态**，进入无状态（Deployment）页面。
3.  单击页面右上角的**使用模板创建**。 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15812/155788432010516_zh-CN.png)

4.  对模板进行相关配置，部署 Grafana 的 deployment 和 service，完成配置后，单击**创建**。 

    -   集群：选择所需的集群。
    -   命名空间：选择资源对象所属的命名空间，必须是kube-system。
    -   示例模板：本示例选择自定义模板，其中包含一个 deployment 和 service。
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15812/155788432010517_zh-CN.png)

    本示例的编排模板如下。

    ```
    apiVersion: extensions/v1beta1
    kind: Deployment
    metadata:
       name: monitoring-grafana
       namespace: kube-system
    spec:
       replicas: 1
       template:
         metadata:
           labels:
             task: monitoring
             k8s-app: grafana
         spec:
           containers:
           - name: grafana
             image: registry.cn-hangzhou.aliyuncs.com/acs/grafana:5.0.4
             ports:
             - containerPort: 3000
               protocol: TCP
             volumeMounts:
             - mountPath: /var
               name: grafana-storage
             env:
             - name: INFLUXDB_HOST
               value: monitoring-influxdb
           volumes:
           - name: grafana-storage
             emptyDir: {}
     ---
    apiVersion: v1
    kind: Service
    metadata:
       name: monitoring-grafana
       namespace: kube-system
    spec:
       ports:
       - port: 80
         targetPort: 3000
       type: LoadBalancer
       selector:
         k8s-app: grafana
    ```

5.  完成部署后，返回无状态（Deployment）页面，选择所需集群，然后选择 kube-system，查看其下部署的应用。 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15812/155788432010518_zh-CN.png)

6.  单击 monitoring-grafana 的名称，查看部署状态，等待运行状态变为 running。 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15812/155788432010519_zh-CN.png)

7.  单击左侧导航栏中的**路由与负载均衡** \> **服务**，进入服务列表，选择所需的集群和命名空间（kube-system），查看外部端点。 

    这个地址是通过 LoadBalancer 类型的 service 自动创建的，对于要求更安全访问策略的开发者而言，建议考虑添加 IP 白名单或者使用配置证书等方式增强安全性。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15812/155788432010521_zh-CN.png)

8.  选择 monitoring-grafana 服务，单击右侧的**外部端点**，登录 Grafana 监控界面。 

    默认的 Grafana 的用户名和密码都是 admin，建议在登录后先修改为更复杂的密码。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15812/155788432010522_zh-CN.png)

9.  您可选择内置的监控模板 ，查看 Pod 和 Node 的监控 Dahsboard。 

    本示例使用的 Grafana 版本内置了两个模板，一个负责展示节点级别的物理资源，一个负责展示 Pod 相关的资源。开发者也可以通过添加自定义的 Dashboard 的方式进行更复杂的展现，也可以基于 Grafana 进行资源的告警等。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15812/155788432010523_zh-CN.png)

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15812/155788432110524_zh-CN.png)


