# 在Knative上观测服务的QPS、RT和Pod扩缩容趋势

在服务运行过程中，您可以在Knative上观测当前服务运行的状况，包括每秒查询数QPS（Query per Second）、平均响应时RT（Response Time）以及Pod扩缩容趋势。本文主要介绍如何配置和观测这些信息。

阿里云Serverless Kubernetes（ASK）集群已开通日志服务和Knative功能。更多信息，请参见[开启Knative](/cn.zh-CN/Serverless Kubernetes集群用户指南/Knative管理/Knative组件管理/开启Knative.md)。

## 创建并配置Logstore

1.  登录[日志服务控制台](https://sls.console.aliyun.com)。

2.  在Project列表区域，单击目标ASK集群对应的日志服务Project名称。

3.  在**日志存储**页面的**日志库**页签，单击**+**图标。

4.  在创建Logstore面板中配置好相关参数后，单击**确定**。更多关于**创建Logstore**参数的相关描述，请参见[管理Logstore](/cn.zh-CN/数据采集/准备工作/管理Logstore.md)。

5.  在**创建成功**对话框中，单击**数据接入导向**。

6.  在数据接入区域，单击**Docker标准输出**。

    ![86](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4874619061/p205975.png)

7.  在**创建机器组**步骤，单击**使用现有机器组**。

8.  在**机器组配置**步骤，选择已有机器组后，单击**下一步**。

9.  在**数据源设置**步骤，使用以下模板样例设置日志服务的采集日志规则后，单击**下一步**。

    ```
    {
        "inputs": [
            {
                "detail": {
                    "IncludeEnv": {
                        "QUEUE_SERVING_PORT": "8012"
                    },
                    "ExcludeLabel": {}
                },
                "type": "service_docker_stdout"
            }
        ]
    }
    ```

10. 在**查询分析配置**步骤，保留选择默认勾选项，单击**下一步**。

11. 在**结束**步骤页签的**查询日志**区域，单击**立即查询**。

    1.  单击**查询分析**观察日志输出。

        ![92-1](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4874619061/p206185.png)

    2.  从**查询分析属性**列表中选择**属性**。

        ![92-2](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4874619061/p206189.png)

    3.  在**查询分析**面板单击**自动生成索引**，在弹出的**自动生成索引属性**对话框中，单击**追加**，然后在**content**字段中添加以下参数。

        |参数名称|类型|别名|
        |----|--|--|
        |httpRequest.latency|text|latency|
        |httpRequest.status|long|status|
        |httpRequest.revisionName|text|revision|


## 配置QPS统计

1.  登录[日志服务控制台](https://sls.console.aliyun.com)。

2.  在Project列表区域，单击目标ASK集群对应的日志服务Project名称。

3.  在**日志存储**页面的**日志库**的页签，单击目标Logstore。

4.  在**查询/分析**文本框中输入以下SQL查询命令。

    ![97-ch](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4676329061/p207338.png)

    ```
    httpRequest | select date_trunc('minute' ,  __time__) as stamp, count(*) as num, revision from log group by stamp,revision order by stamp
    ```

5.  单击**流图**，进行**属性配置**之后，单击**添加到仪表盘**。

    ![91-ch](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4874619061/p206816.png)


## 配置RT统计

1.  登录[日志服务控制台](https://sls.console.aliyun.com)。

2.  在Project列表区域，单击目标ASK集群对应的日志服务Project名称。

3.  在**日志存储**页面的**日志库**的页签，单击目标Logstore。

4.  在**查询/分析**文本框中输入以下SQL查询命令。

    ```
    httpRequest|select  stamp as timestamp, trt as rt, revision from (select  date_trunc('minute' ,  __time__)  as stamp, round (avg(cast(replace(latency,'s') as double)), 6) as trt, revision   from log   group by   stamp, revision
           order by stamp 
           limit 100000)
    ```

5.  单击**流图**，进行**属性配置**之后，单击**添加到仪表盘**。

    ![93-ch](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4874619061/p206866.png)


## 配置Pod扩缩容趋势统计

1.  登录[日志服务控制台](https://sls.console.aliyun.com)。

2.  在Project列表区域，单击目标ASK集群对应的日志服务Project名称。

3.  在**日志存储**页面的**日志库**的页签 ，单击audit名称开头的Logstore。

4.  从**查询分析属性**列表中选择**属性**。

5.  在**查询分析**面板的**指定字段查询**区域的**responseObject**字段添加以下参数。

    |参数名称|参数类型|别名|
    |----|----|--|
    |metadata.labels.serving.knative.dev/revision|text|revision|
    |metadata.labels.serving.knative.dev/service|text|service|
    |status.readyReplicas|long|readyReplicas|
    |status.replicas|long|replicas|

6.  在**查询/分析**文本框中输入以下SQL查询命令。

    ```
    objectRef.resource: deployments and responseStatus.code: 200 and ( verb: update or verb: get)| select case when "revision" is null then '无' else "revision" end as "revision", "t" as timestamp, total,concat('total:', cast(total AS varchar), ', ready: ', cast(ready AS varchar), ', notReady',cast((total-ready) AS varchar)) as detail from (select date_trunc('minute' ,  __time__) as t, max(replicas) as total, max(readyReplicas) as ready, revision from log where service='event-display-common' group by t, revision order by t)
    ```

7.  单击**流图**，进行**属性配置**之后，单击**添加到仪表盘**。

    ![94-ch](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4874619061/p206950.png)


## 观测服务运行状况

为了更好地观测服务运行状况的图表数据，您可将多个图表放在一个仪表盘进行展示，具体有以下两种方式：

-   添加仪表盘时，选择相同名称的仪表盘。
-   创建一个全局的仪表盘，然后将目标图表导入即可。具体操作，请参见[创建仪表盘](/cn.zh-CN/可视化与告警/仪表盘/创建仪表盘.md)和[添加统计图表到仪表盘](/cn.zh-CN/可视化与告警/仪表盘/添加统计图表到仪表盘.md)。

    ![96](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4874619061/p206990.png)


