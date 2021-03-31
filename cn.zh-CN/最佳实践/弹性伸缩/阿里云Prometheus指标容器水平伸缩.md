---
keyword: [容器水平伸缩, 阿里云prometheus, adapter, arms]
---

# 阿里云Prometheus指标容器水平伸缩

默认HPA只支持基于CPU/内存的自动伸缩，并不能满足日常的运维需求。阿里云Prometheus监控全面对接开源Prometheus生态，支持类型丰富的组件监控，提供多种开箱即用的预置监控大盘，且提供全面托管的Prometheus服务。本文介绍如何将阿里云Prometheus指标转换成HPA可用的指标，从而为应用提供更加便捷的扩缩机制。

## 前提条件

在将阿里云Prometheus指标转换成HPA可用的指标前，您需要部署相关组件。

1.  部署阿里云Prometheus监控组件。

    有关部署阿里云Prometheus的详细步骤，请参见[开启阿里云Prometheus监控](/cn.zh-CN/Kubernetes集群用户指南/可观测性/监控管理/阿里云Prometheus监控.md)。

2.  部署alibaba-cloud-metrics-adapter组件。

    有关部署alibaba-cloud-metrics-adapter组件详细步骤，请参见[部署alibaba-cloud-metrics-adapter组件](/cn.zh-CN/Kubernetes集群用户指南/弹性伸缩/阿里云指标容器水平伸缩.md)。


## 示例

本文举例如何配置alibaba-cloud-metrics-adapter，实现将阿里云Prometheus指标转换为HPA可用指标，并实现该指标自动伸缩。

1.  部署工作负载。

    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

    2.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

    3.  在集群管理页左侧导航栏中，选择**工作负载** \> **无状态**。

    4.  在**无状态**页面，单击**使用模板创建**。

    5.  在使用模板创建页面，部署以下YAML文件创建一个应用及对应的Service，然后单击**创建**。

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

    4.  在**服务发现**页签选择ServiceMonitor页签，单击**添加ServiceMonitor**。

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
            #2. 这里填入arms-prometheus的地址
            url: http://arms-prometheus-proxy.aliyun.com:9090/api/v1/prometheus/8cba801fff65546a3012e9a6843afd/1240538168824185/ce63742a509f948dda8ef18e75e703356/cn-shenzhen
          ramRoleType: __ACK_RAM_ROLE_TYPE__
          replicas: 1
          service:
            type: ClusterIP
          serviceAccountName: admin
          tolerations: []
        ```

5.  检查apiService指标。

    ```
    kubectl get apiservice
    ```

    预期输出：

    ```
    v1beta1.custom.metrics.k8s.io          kube-system/ack-alibaba-cloud-metrics-adapter-custom-service     True        73m
    ```

    执行以下命令查看apiService指标详情。

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


## adapter配置文件详解

上述步骤[修改alibaba-cloud-metrics-adapter配置](#step_j8i_w1p_mpy)涉及到的adapter的配置主要分为4个：

-   Discovery

    指定需要处理的Prometheus的metrics。通过seriesQuery挑选需要处理的指标集合。您可以通过seriesFilters精确过滤metrics。seriesQuery可以根据标签进行查找（示例代码如下），也可以直接指定metrics名称查找。

    ```
    seriesQuery: '{__name__=~"^container_.*_total",container_name!="POD",namespace!="",pod_name!=""}'
    seriesFilters:
      - isNot: "^container_.*_seconds_total"
    ```

    **说明：** seriesFilters：

    -   is：<regex\>，匹配包含该正则表达式的metrics。
    -   isNot：<regex\>，匹配不包含该正则表达式的metrics。
-   Association

    设置metrics与Kubernetes中的资源映射关系，Kubernetes资源可以通过`kubectl api-resources`命令查看。overrides会将Prometheus metric label与一个Kubernetes资源（下例为Deployment）关联。

    **说明：** 需要注意的是该label必须是一个真实的Kubernetes资源，如metrics的pod\_name可以映射为Kubernetes的资源Pod，但不能将container\_image映射为Kubernetes的资源Pod，映射错误会导致无法通过custom metrics API获取正确的值。这也表示metrics中必须存在一个真实的资源名称，将其映射为Kubernetes资源。

    ```
    resources:
      overrides:
        microservice: {group: "apps", resource: "deployment"}
    ```

-   Naming

    用于将Prometheus metrics名称转化为custom metrics API所使用的metrics名称，但不会改变其本身的metrics名称，即通过`curl http://$(kubectl get service sample-app -o jsonpath='{ .spec.clusterIP }')/metrics`获得的仍然是老的指标名称。如果不需要可以不执行这一步。

    ```
    # match turn any name <name>_total to <name>_per_second
    # e.g. http_requests_total becomes http_requests_per_second
    name:
      matches: "^(.*)_total$"
      as: "${1}_per_second"
    ```

    HPA后续可以通过`/apis/{APIService-name}/v1beta1/namespaces/{namespaces-name}/pods/*/http_requests_per_second`获取metrics。

-   Querying

    处理调用custom metrics API获取到的metrics的值，该值最终提供给HPA进行扩缩容。

    ```
    # convert cumulative cAdvisor metrics into rates calculated over 2 minutes
    metricsQuery: "sum(rate(<<.Series>>{<<.LabelMatchers>>,container_name!="POD"}[2m])) by (<<.GroupBy>>)"
    ```

    此字段使用Go语言模板将URL请求转变为Prometheus的请求，它会提取custom metrics API请求中的字段，并将其划分为<metric name\>.<group-resource\>.<以及group-resource中的一个或多个对象\>，对应以下字段：

    -   Series：metrics名称。
    -   LabelMatchers：以逗号分割的对象，当前表示特定group-resource加上命名空间的label（如果该group-resource是在namespace内）。
    -   GroupBy：以逗号分割的label的集合，当前表示LabelMatchers中的group-resource label假设metrics。 http\_requests\_per\_second示例代码如下。

        ```
        http_requests_per_second{pod="pod1",service="nginx1",namespace="somens"}
        http_requests_per_second{pod="pod2",service="nginx2",namespace="somens"}
        ```

    当调用`kubectl get --raw "/apis/{APIService-name}/v1beta1/namespaces/somens/pods/*/http_request_per_second"`时，metricsQuery字段的模板的实际内容如下。

    ```
    Series: "http_requests_total"
    LabelMatchers: "pod=~"pod1|pod2",namespace="somens"
    GroupBy: pod
    ```


