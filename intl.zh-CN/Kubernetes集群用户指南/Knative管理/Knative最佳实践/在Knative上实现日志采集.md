# 在Knative上实现日志采集

日志服务（Log Service，简称 LOG）是针对日志类数据的一站式服务。您无需开发就能快捷完成日志数据采集、消费、投递以及查询分析等功能。在Knative中结合日志服务，能有效提升对Serverless应用的运维能力。

-   您已经开通日志服务，请参见[t17400.md\#](/intl.zh-CN/Kubernetes集群用户指南/可观测性/日志管理/通过日志服务采集Kubernetes容器日志.md)。
-   您已经部署Knative Service服务，请参见[快速部署Serverless应用](/intl.zh-CN/Kubernetes集群用户指南/Knative管理/Knative服务管理/快速部署Serverless应用.md)。

## 操作步骤

1.  为Helloworld接入日志采集。

    请参见[t13075.md\#](/intl.zh-CN/数据采集/Logtail采集/采集容器日志/通过DaemonSet-控制台方式采集Kubernetes标准输出.md)。

    1.  登录[日志服务管理控制台](http://sls.console.aliyun.com/)。

    2.  在**Project列表**区域，单击**创建Project**创建名称为hellword的项目。详细操作请参见[管理Logstore](/intl.zh-CN/数据采集/准备工作/管理Logstore.md)。

    3.  在helloworld项目页面的右上角，单击**接入数据**。

    4.  在接入数据页面选择**Docker标准输出**，进入Docker标准输出配置页面。

        ![指定采集模式](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9995659951/p49477.png)

    5.  在选择日志空间配置中，确认日志空间信息并单击**下一步**。

    6.  设置**创建机器组**。

        若您已经安装Logtail日志组件，请点击**使用现有机器组**。

        您可以选择Kubernetes或者标准Docker采集进行日志采集。具体操作，请参见[通过日志服务采集Kubernetes容器日志](/intl.zh-CN/Kubernetes集群用户指南/可观测性/日志管理/通过日志服务采集Kubernetes容器日志.md)和[采集标准Docker容器日志](/intl.zh-CN/数据采集/Logtail采集/采集容器日志/采集标准Docker容器日志.md)。

    7.  单击**确认安装完毕**。

    8.  设置**机器组配置**。

    9.  设置**数据源设置**。

        插件配置这里我们针对helloworld-go Service，设置采集的环境变量为：`"K_SERVICE": "helloworld-go"`。并且通过 processors 分割日志信息，如这里`"Keys": [ "time","level", "msg" ]`。插件配置示例如下。

        ```
        {
          "inputs": [
            {
              "detail": {
                "IncludeEnv": {
                  "K_SERVICE": "helloworld-go"
                },
                "IncludeLabel": {},
                "ExcludeLabel": {}
              },
              "type": "service_docker_stdout"
            }
          ],
          "processors": [
            {
              "detail": {
                "KeepSource": false,
                "NoMatchError": true,
                "Keys": [
                  "time",
                  "level",
                  "msg"
                ],
                "NoKeyError": true,
                "Regex": "(\\d+-\\d+-\\d+\\s+\\d+:\\d+:\\d+)\\s+(\\w+)\\s+(.*)",
                "SourceKey": "content"
              },
              "type": "processor_regex"
            }
          ]
        }
        ```

    10. 数据源设置完成后，单击**下一步**。

    11. 在查询分析配置页面，开启**全文索引**，设置查询字段索引属性。

    12. 单击**下一步**进入结束页面。

2.  执行以下命令，访问Hello World示例服务。

    此时会产生日志信息。

    ```
    curl -H "Host: helloworld-go.default.example.com" http://112.124.XX.XX
    ```

    ```
    Hello Knative!
    ```

3.  在日志库的目标日志右侧悬浮鼠标至图标![日志库图标](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9995659951/p94111.png)上，选择**![日志库图标](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9995659951/p94111.png)** \> **查询分析**，可以看到该logstore的日志信息。

4.  设置查询分析。

    请参见[分析简介](/intl.zh-CN/查询与分析/分析简介.md)。

    为了便于查看，您可以通过**列设置**显示所需要的列。下图中的示例设置了level、msg和time这三列。

    ![列](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9995659951/p149161.png)


## 总结

通过上面的介绍，可以帮助您了解如何在Knative中使用日志服务收集Serverless应用容器日志。在Knative中采用日志服务收集、分析业务日志，满足了生产级别的Serverless应用运维的诉求。

