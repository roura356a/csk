---
keyword: [Knative, Prometheus, Pod并发请求数]
---

# 在Knative中通过Prometheus采集Pod并发请求数

在实际服务中，您经常需要根据当前服务并发请求数来进行业务指标判断，而Knative正是通过queue-proxy容器实现了对请求指标的采集。本文介绍如何结合Prometheus呈现Knative服务中Pod请求指标。

## 步骤一：安装Prometheus监控组件

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，选择**市场** \> **应用目录**。

3.  在应用目录页面的**阿里云应用**页签中，搜索并单击**ack-arms-prometheus**。

4.  在应用目录 - ack-arms-prometheus页面，单击**参数**页签。

5.  在应用目录 - ack-arms-prometheus右侧的**创建**区域中，选择目标集群，然后单击**创建**。

    **说明：** **命名空间**和**发布名称**默认为**arms-prom**。


## 步骤二：设置queue-proxy采集Job规则

1.  登录[ARMS 控制台](https://arms-intl.console.aliyun.com/)。

2.  在左侧导航栏中单击**Prometheus监控**。

3.  在Prometheus监控页面，单击目标集群**操作**列下的**设置**。

4.  单击**Prometheus设置**页签，在Prometheus.yaml文件中添加以下内容，然后单击**保存**。

    ```
    global:  
      scrape_interval: 30s
      scrape_timeout: 10s
      evaluation_interval: 30s
    scrape_configs:
    - job_name: queue-proxy
      scrape_interval: 3s
      scrape_timeout: 3s
      kubernetes_sd_configs:
      - role: pod
      relabel_configs:
      # Rename metadata labels to be reader friendly
      - source_labels: [__meta_kubernetes_pod_label_serving_knative_dev_revision, __meta_kubernetes_pod_container_port_name]
        action: keep
        regex: .+;http-autometric
      - source_labels: [__meta_kubernetes_namespace]
        target_label: namespace
      - source_labels: [__meta_kubernetes_pod_name]
        target_label: pod
      - source_labels: [__meta_kubernetes_service_name]
        target_label: service
    ```

5.  单击**指标**页签，查看queue-proxy相关指标。

    ![指标](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1006659951/p128175.png)


## 步骤三：获取Pod请求并发数

1.  在集群配置左侧导航栏，单击**大盘列表**。

2.  在大盘列表页面单击**Prometheus**。

3.  在Prometheus页面左侧，单击**Explore**图标。

    ![explore](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9670576161/p128189.png)

4.  在**Metrics**指标下拉框中，选择queue-proxy指标进行查询。

    本文举例查询Pod每秒请求并发数指标**queue\_requests\_per\_second**。

    ![metrics](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1006659951/p128193.png)

    您可以通过设置Namespace、Revision以及Pod名称查询Pod每秒请求连接数。示例如下。

    ```
    queue_requests_per_second{destination_configuration="helloworld-go",destination_namespace="default",destination_pod="helloworld-go-ttf52-deployment-5778d86bd6-dnxw2"}
    ```

    ![请求数](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1006659951/p128195.png)


