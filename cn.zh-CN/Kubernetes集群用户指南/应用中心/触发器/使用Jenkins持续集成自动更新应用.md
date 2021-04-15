---
keyword: [Jenkins CI, 自动更新应用, 镜像版本]
---

# 使用Jenkins持续集成自动更新应用

应用中心触发器支持更新ACK编排模板源（仅限编排模板）中的容器镜像版本，并触发应用重新部署。您可以使用Jenkins持续集成构建并更新编排模板中的容器镜像版本，然后使用触发器自动更新应用。本文介绍如何使用Jenkins CI自动更新应用。

-   已创建应用，并生成触发器。具体操作，请参见[使用编排模板创建应用](/cn.zh-CN/Kubernetes集群用户指南/应用中心/应用管理/使用编排模板创建应用.md)和[使用触发器自动更新应用](/cn.zh-CN/Kubernetes集群用户指南/应用中心/触发器/使用触发器自动更新应用.md)。
-   已部署Jenkins系统。具体操作，请参见[快速搭建Jenkins环境并完成流水线作业](/cn.zh-CN/最佳实践/DevOps/快速搭建Jenkins环境并完成流水线作业.md)。

本文以提前创建的Red版本的demo应用为例，使用Jenkins将应用的镜像由Red版本修改为Green版本，然后通过触发器更新应用。最后通过浏览器访问应用，验证使用触发器自动更新应用是否成功。如果应用页面由红色变为绿色，说明应用镜像由Red版本更新为Green版本，使用Jenkins持续集成自动更新应用成功。

## 操作步骤

1.  创建Jenkins构建任务并构建和推送`registry.cn-hangzhou.aliyuncs.com/acs/rollouts-demo:green`镜像。具体操作，请参见[快速搭建Jenkins环境并完成流水线作业](/cn.zh-CN/最佳实践/DevOps/快速搭建Jenkins环境并完成流水线作业.md)。

2.  执行以下脚本，触发应用中心使用`registry.cn-hangzhou.aliyuncs.com/acs/rollouts-demo:green`镜像，并重新部署应用。

    ```
    ## 4. trigger appcenter
    DEPLOYMENT_NAME=demo
    CONTAINER_NAME=demo
    IMAGE=registry.cn-hangzhou.aliyuncs.com/acs/rollouts-demo:green
    cat <<EOF > patch.json
    {
        "resource":{
            "deployment":{
                "metadata":{
                    "name":"$DEPLOYMENT_NAME"
                },
                "spec": {
                    "template": {
                        "spec": {
                            "containers": [
                                {
                                    "name":"$CONTAINER_NAME",
                                    "image":"$IMAGE"
                                }]
                        }
                    }
                }
            }
        }
    }
    EOF
    
    curl -X POST -H 'content-type: application/json' \
        https://cs.console.aliyun.com/hook/trigger?token=xxxxxxxx \
        -d "$(cat patch.json)"
    {"code":"200","message":"","requestId":"151c92fd-d97b-4eff-a6da-4669114fa4f0"}
    ```


## 结果验证

1.  获取应用的端点。

    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

    2.  在控制台左侧导航栏中，单击**集群**。

    3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

    4.  在集群管理页左侧导航栏中，选择**服务与路由** \> **路由**。

    5.  在路由页面查看demo的端点。

2.  在hosts文件输入以下内容。

    ```
    <端点> app.demo.example.com 
    ```

3.  在浏览器中访问app.demo.example.com。

    浏览器显示以下页面，说明应用已由Red版本更新为Green版本。

    ![结果验证](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6502548161/p263738.png)


**相关文档**  


[触发器概述](/cn.zh-CN/Kubernetes集群用户指南/应用中心/触发器/触发器概述.md)

[使用Jenkins持续集成自动更新应用](#task_2068340)

[使用容器镜像触发器自动更新应用]()

