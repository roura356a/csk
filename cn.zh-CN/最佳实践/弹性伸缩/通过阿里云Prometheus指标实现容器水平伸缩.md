---
keyword: [容器水平伸缩, 阿里云prometheus, adapter, arms]
---

# 通过阿里云Prometheus指标实现容器水平伸缩

默认HPA只支持基于CPU和内存的自动伸缩，并不能满足日常的运维需求。阿里云Prometheus监控全面对接开源Prometheus生态，支持类型丰富的组件监控，提供多种开箱即用的预置监控大盘，且提供全面托管的Prometheus服务。本文介绍如何将阿里云Prometheus指标转换成HPA可用的指标，从而为应用提供更加便捷的扩缩机制。

## 前提条件

在将阿里云Prometheus指标转换成HPA可用的指标前，您需要部署相关组件。

1.  部署阿里云Prometheus监控组件。

    有关部署阿里云Prometheus的详细步骤，请参见[开启阿里云Prometheus监控](/cn.zh-CN/Kubernetes集群用户指南/可观测性/监控管理/阿里云Prometheus监控.mdsection_o0u_mkk_58y)。

2.  部署alibaba-cloud-metrics-adapter组件。

    有关部署alibaba-cloud-metrics-adapter组件详细步骤，请参见[部署alibaba-cloud-metrics-adapter组件](/cn.zh-CN/Kubernetes集群用户指南/弹性伸缩/阿里云指标容器水平伸缩.md)。


## 示例

本文举例如何配置alibaba-cloud-metrics-adapter，实现将阿里云Prometheus指标转换为HPA可用指标，并实现该指标自动伸缩。

1.  部署工作负载。

    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

    2.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

    3.  在集群管理页左侧导航栏中，选择**工作负载** \> **无状态**。

    4.  在**无状态**页面，单击**使用模板创建**。

    5.  在使用模板创建页面，部署以下YAML文件创建一个名为sample-app的应用及对应的Service，然后单击**创建**。

        **说明：** 此容器暴露出http\_requests\_total的指标用来标识访问次数。

        ```
        apiVersion: apps/v1
        kind: Deployment
        metadata:
          name: sample-app
          labels:
            app: sample-app
        spec:
          replicas: 1
          selector:
            matchLabels:
              app: sample-app
          template:
            metadata:
              labels:
                app: sample-app
            spec:
              containers:
              - image: luxas/autoscale-demo:v0.1.2
                name: metrics-provider
                ports:
                - name: http
                  containerPort: 8080
        ---
        apiVersion: v1
        kind: Service
        metadata:
          name: sample-app
          namespace: default
          labels:
            app: sample-app
        spec:
          ports:
            - port: 8080
              name: http
              protocol: TCP
              targetPort: 8080
          selector:
            app: sample-app
          type: ClusterIP
        ```

2.  添加ServiceMonitor。

    1.  登录[ARMS控制台](https://arms.console.aliyun.com/#/home)。

    2.  在左侧导航栏中单击**Prometheus监控**。

    3.  在目标集群右侧的**操作**列单击**设置**。

    4.  单击**服务发现**页签，然后单击ServiceMonitor页签，单击**添加ServiceMonitor**。

    5.  复制以下YAML文件，然后单击**确定**。

        ```
        apiVersion: monitoring.coreos.com/v1
        kind: ServiceMonitor
        metadata:
          name: sample-app
          namespace: default
        spec:
          endpoints:
          - interval: 30s
            port: http
            path: /metrics
          namespaceSelector:
            any: true
          selector:
            matchLabels:
              app: sample-app
        ```

3.  确认监控状态。

    单击**Targets\(beta\)**，如果看到**default/sample-app/0\(1/1 up\)**，则说明您成功在阿里云Prometheus监控到了部署的应用。

    ![monitor](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9974480061/p169390.png)

4.  修改alibaba-cloud-metrics-adapter配置。

    1.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

    2.  在集群管理页左侧导航栏中，单击**发布**。

    3.  找到alibaba-cloud-metrics-adapter，并单击右侧的**更新**。

    4.  复制[alibaba-cloud-metrics-adapter](https://github.com/AliyunContainerService/alibaba-cloud-metrics-adapter/blob/master/docs/metrics/arms_prometheus.md)中完整的YAML文件内容，然后单击**更新**。

        以下为部分YAML文件内容。

        ```
        AlibabaCloudMetricsAdapter:
          affinity: {}
          commonLabels: ""
          env:
          - AccessKeyId: ""
          - AccessKeySecret: ""
          - Region: ""
          fullnameOverride: ""
          image:
            pullPolicy: IfNotPresent
            repository: registry.aliyuncs.com/acs/alibaba-cloud-metrics-adapter-amd64
            tag: v0.2.0-alpha-0c00ec2                        
          listenPort: 443
          nameOverride: ""
          nodeSelector: {}
          podAnnotations: {}
          prometheus:
            adapter:
              rules:
                custom: 
                # 添加新的转换规则，请确保阿里云Prometheus中指标标签和此处一致，如果不一致，请照阿里云Prometheus中指标标签修改。
                - seriesQuery: 'http_requests_total{kubernetes_namespace!="",kubernetes_pod_name!=""}'
                  resources:
                    overrides:
                      kubernetes_namespace: {resource: "namespace"}
                      kubernetes_pod_name: {resource: "pod"}
                  name:
                    matches: "^(.*)_total"
                    as: "${1}_per_second"
                  metricsQuery: 'sum(rate(<<.Series>>{<<.LabelMatchers>>}[2m])) by (<<.GroupBy>>)'
                default: true
            #1. 这里设置为true，打开Prometheus adapter。
            enabled: true             
            logLevel: 2
            metricsRelistInterval: 1m
            tls:
              ca: ""
              certificate: ""
              enable: false
              key: ""
            #2. 这里填入阿里云Prometheus控制的地址。
            url: http://arms-prometheus-proxy.aliyun.com:9090/api/v1/prometheus/8cba801fff65546a3012e9a6843afd/1240538168824185/ce63742a509f948dda8ef18e75e703356/cn-shenzhen
          ramRoleType: __ACK_RAM_ROLE_TYPE__
          replicas: 1
          service:
            type: ClusterIP
          serviceAccountName: admin
          tolerations: []
        ```

        有关ack-alibaba-cloud-adapter配置文件详细说明，请参见[ack-alibaba-cloud-adapter配置文件详解](#section_6lm_rqv_j2t)。

5.  检查HPA可用指标。

    执行以下命令查看HPA可用指标详情。

    ```
    kubectl get --raw "/apis/custom.metrics.k8s.io/v1beta1/namespaces/default/pods/*/http_requests_per_second" | jq .
    ```

    ```
    {
      "kind": "MetricValueList",
      "apiVersion": "custom.metrics.k8s.io/v1beta1",
      "metadata": {
        "selfLink": "/apis/custom.metrics.k8s.io/v1beta1/namespaces/default/pods/%2A/http_requests_per_second"
      },
      "items": [
        {
          "describedObject": {
            "kind": "Pod",
            "namespace": "default",
            "name": "sample-app-579bc6774c-rmjfd",
            "apiVersion": "/v1"
          },
          "metricName": "http_requests_per_second",
          "timestamp": "2020-09-22T08:42:58Z",
          "value": "33m",
          "selector": null
        }
      ]
    }
    ```

6.  使用以下YAML文件部署HPA，然后执行`kubectl apply -f hpa.yaml`创建HPA应用。

    ```
    kind: HorizontalPodAutoscaler
    apiVersion: autoscaling/v2beta1
    metadata:
      name: sample-app
    spec:
      scaleTargetRef:
        apiVersion: apps/v1
        kind: Deployment
        name: sample-app
      minReplicas: 1
      maxReplicas: 10
      metrics:
      - type: Pods
        pods:
          metricName: http_requests_per_second
          targetAverageValue: 500m
    ```

7.  执行压测验证。

    ```
    ab -c 50 -n 2000 ClusterIP(sample-app):8080
    ```

    执行以下命令查看HPA详情。

    ```
    kubectl get hpa sample-app
    ```

    预期输出：

    ```
    NAME         REFERENCE               TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
    sample-app   Deployment/sample-app   33m/500m   1         10        1          72m
    ```


## ack-alibaba-cloud-adapter配置文件详解

ack-alibaba-cloud-adapter通过以下步骤将Prometheus中的指标转换成HPA可用的指标：

1.  [Discovery](#li_jow_zkq_s2x)：ack-alibaba-cloud-adapter会从Prometheus发现可用的指标。
2.  [Association](#li_dr0_luj_pw9)：将指标与Kubernetes资源（Pod、Node、Namespace）相关联。
3.  [Naming](#li_1s7_ard_e32)：定义转换后的HPA可用指标名称。
4.  [Querying](#li_pex_lqt_gqo)：定义查询Prometheus语句。

以上文中sample-app容器中暴露出来的`http_requests_total`指标转换成HPA中的`http_requests_per_second`为例，完整的ack-alibaba-cloud-adapter配置文件如下。

```
```
- seriesQuery: 'http_requests_total{kubernetes_namespace!="",kubernetes_pod_name!=""}'
  resources:
    overrides:
      kubernetes_namespace: {resource: "namespace"}
      kubernetes_pod_name: {resource: "pod"}
  name:
    matches: "^(.*)_total"
    as: "${1}_per_second"
  metricsQuery: 'sum(rate(<<.Series>>{<<.LabelMatchers>>}[2m])) by (<<.GroupBy>>)'
```
```

-   Discovery

    指定待转换的Prometheus指标，您可以通过seriesFilters精确过滤指标。seriesQuery可以根据标签进行查找（示例代码如下）。

    ```
    ```
    seriesQuery: '{kubernetes_namespace!="",kubernetes_pod_name!=""}'
    seriesFilters:
        - isNot: "^container_.*_seconds_total"
    或者
    seriesQuery: 'http_requests_total{kubernetes_namespace!="",kubernetes_pod_name!=""}'
    ```
    ```

    **说明：** seriesFilters为非必填项，用来过滤指标：

    -   is：<regex\>，匹配包含该正则表达式的指标。
    -   isNot：<regex\>，匹配不包含该正则表达式的指标。
-   Association

    设置Prometheus指标标签与Kubernetes中的资源映射关系。`http_requests_total`指标的标签包括`kubernetes_namespace!=""`和`kubernetes_pod_name!=""`。

    ```
    ```
    - seriesQuery: 'http_requests_total{kubernetes_namespace!="",kubernetes_pod_name!=""}'
      resources:
        overrides:
          kubernetes_namespace: {resource: "namespace"}
          kubernetes_pod_name: {resource: "pod"}
    ```
    ```

-   Naming

    用于将Prometheus指标名称转换成HPA的指标名称，但不会改变Prometheus本身的指标名称。如果使用Prometheus原来的指标，可以不设置。

    **说明：** 您可以通过执行命令`kubectl get --raw "/apis/custom.metrics.k8s.io/v1beta1"`查看HPA可用的所有指标。

    ```
    ```
    - seriesQuery: 'http_requests_total{kubernetes_namespace!="",kubernetes_pod_name!=""}'
      resources:
        overrides:
          kubernetes_namespace: {resource: "namespace"}
          kubernetes_pod_name: {resource: "pod"}
      name:
        matches: "^(.*)_total"
        as: "${1}_per_second"
    ```
    ```

-   Querying

    查询Prometheus API的模板。ack-alibaba-cloud-adapter会根据HPA中的参数，填充参数到此模板中，然后发送给Prometheus API请求，并将获得的值最终提供给HPA进行弹性扩缩。

    ```
    ```
    - seriesQuery: 'http_requests_total{kubernetes_namespace!="",kubernetes_pod_name!=""}'
      resources:
        overrides:
          kubernetes_namespace: {resource: "namespace"}
          kubernetes_pod_name: {resource: "pod"}
      name:
        matches: "^(.*)_total"
        as: "${1}_per_second"
      metricsQuery: 'sum(rate(<<.Series>>{<<.LabelMatchers>>}[2m])) by (<<.GroupBy>>)'
    ```
    ```


**相关文档**  


[阿里云Prometheus监控](/cn.zh-CN/Kubernetes集群用户指南/可观测性/监控管理/阿里云Prometheus监控.md)

[容器水平伸缩（HPA）](/cn.zh-CN/Kubernetes集群用户指南/弹性伸缩/容器水平伸缩（HPA）.md)

