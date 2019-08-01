# 在Knative 上实现日志服务 {#concept_645533 .concept}

日志服务（Log Service，简称 LOG）是针对日志类数据的一站式服务。您无需开发就能快捷完成日志数据采集、消费、投递以及查询分析等功能。在 Knative 中结合日志服务，能有效提升对 Serverless 应用的运维能力。

## 前提条件 {#section_llt_cmh_amq .section}

-   您已经开通日志服务，参见[使用日志服务进行Kubernetes日志采集](intl.zh-CN/Kubernetes集群用户指南/日志管理/使用日志服务进行Kubernetes日志采集.md#)。
-   您已经部署 Knative Service 服务。参考[部署 Serving Hello World 应用示例](intl.zh-CN/Kubernetes集群用户指南/Knative 管理/部署 Serving Hello World 应用.md#)。

## 操作步骤 {#section_hnp_6n3_wpf .section}

1.  为helloworld 接入日志采集。参考[容器标准输出](../../../../intl.zh-CN/数据采集/Logtail采集/容器日志采集/容器标准输出.md#)。
    1.  登录[日志服务管理控制台](http://sls.console.aliyun.com/)，单击目标Project名称，在**日志库**页签的Logstore列表中，可以看到步骤1创建的Logstore。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/545058/156464533449476_zh-CN.png)

    2.  在目标Logstore右侧单击**数据接入向导**，弹出helloword-log页面。
    3.  在自建软件区域选择**Docker标准输出**，进入指定采集模式页面。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/545058/156464534549477_zh-CN.png)

    4.  填写配置名称和插件配置完成后，单击**下一步**。

        插件配置这里我们针对 helloworld-go Service, 设置采集的环境变量为：`"K_SERVICE": "helloworld-go"`。并且通过 processors 分割日志信息，如这里`"Keys": [ "time","level", "msg" ]`。插件配置示例如下：

        ``` {#codeblock_qnx_9x4_ngl}
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

    5.  进入应用到机器组页面，勾选目标Project名称并单击**应用到机器组**。
    6.  进入自定义页面，开启**全文索引属性**，设置查询字段索引属性，并单击**下一步**。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/545058/156464534549478_zh-CN.png)

    7.  进入投递 & ETL页面，确认无误后，单击**确认**。
2.  执行如下命令，访问 Hello World 示例服务。

    此时会产生日志信息。

    ``` {#codeblock_pwb_eso_4ep}
    $ curl -H "Host: helloworld-go.default.example.com" http://112.124.XX.XX
    Hello Knative!
    ```

3.  在Logstore列表中，在目标Logstore右侧单击**查询**，可以看到该logstore的日志信息。
4.  设置查询分析。参考[实时分析简介](../../../../intl.zh-CN/查询与分析/实时分析简介.md#)。
    1.  为了便于查看，你可以通过**列设置**显示所需要的列。下图中的示例设置了level、msg和time这三列。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/545058/156464534649479_zh-CN.png)


## 总结 {#section_klm_51q_jro .section}

通过上面的介绍，可以帮助您了解如何在 Knative 中使用日志服务收集 Serverless 应用容器日志。在 Knative 中采用日志服务收集、分析业务日志，满足了生产级别的 Serverless 应用运维的诉求。

