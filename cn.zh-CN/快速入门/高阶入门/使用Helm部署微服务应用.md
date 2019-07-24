# 使用Helm部署微服务应用 {#concept_xfh_dnr_n2b .concept}

本文介绍如何将一个较复杂的应用部署到阿里云Kubernetes容器服务上，下面将从基础设施和应用部署的不同组合方式，来部署一个复杂的SpringCloud应用。

## 部署方式 {#section_dgh_2nr_n2b .section}

-   基础设施（Eureka，ConfigServer）和应用一起部署。
-   在容器服务上搭建好基础设施后，再部署应用。

## 示例应用PiggyMetrics {#section_egh_2nr_n2b .section}

[PiggyMetrics](https://github.com/sqshq/PiggyMetrics?spm=a2c4e.11153940.blogcont610430.14.40667737Uedu2h)是github上的一个SpringCloud应用项目，Star数目3400多。这个项目主体采用Docker Compose部署，包含了完整的源代码以及构建好的容器镜像，是非常不错的SpringCloud容器化示例。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16138/15639303737362_zh-CN.png)

这个项目包含了3个业务微服务，分别是统计服务（Statistics Service）、账户服务（Account Service）和通知服务（Notification Service）。每个服务分别对应一个独立的MongoDB。微服务架构图示（采用作者原图）如下：

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16138/15639303737363_zh-CN.png)

SpringCloud基础组件为负责服务注册和registry服务（Eureka服务注册），config服务（配置管理），gateway（API网关，同时也是JavaScript Web界面），monitor服务（Hystrix Dashboard/Turbine）等。

## 场景一 用helm一键部署所有服务 {#section_hgh_2nr_n2b .section}

1.  修改PiggyMetrics应用程序的Docker编写文件。
    1.  切换配置文件版本。

        PiggyMetrics的部署采用docker-compose YAML部署到单机，如果要部署到Kubernetes环境中 ，需要转换成为Kubernetes deployment YAML。

        **说明：** PiggyMetrics中的[docker compose](https://github.com/sqshq/PiggyMetrics/blob/master/docker-compose.yml?spm=a2c4e.11153940.blogcont610430.17.40667737Uedu2h&file=docker-compose.yml)模版为2.1，kompose不支持该版本，所以需要把compose文件改为版本2。

    2.  在docker-compose.yml文件中。

        去除kompose不支持的语法。

        ``` {#codeblock_n86_s7d_saf}
        depends_on:
              config:
                condition: service_healthy  # 不支持 condition
        ```

        增加Kubernetes server type annotation。

        ``` {#codeblock_tcg_v6p_ptn}
        depends_on:
          - config
        labels:
          kompose.service.type: loadbalancer
        ```

    3.  在docker-compose.dev.yml文件中，将PiggyMetrics应用程序使用的四个MongoDB数据库的外部端口更改为27017。

        **说明：** PiggyMetrics应用包含四个MongoDB数据库，分别由 `auth-mongodb`, 、`account-mongodb`、`statistics-mongodb`和 `notification-mongodb`这四个字段定义。

        完整的YAML文件示例如下：

        ``` {#codeblock_em4_u4h_vgy}
        version: '2'
        services:
          rabbitmq:
            image: rabbitmq:3-management
            restart: always
            labels:
              kompose.service.type: nodeport
            ports:
              - 5672
              - 15672:15672
            logging:
              options:
                max-size: "10m"
                max-file: "10"
        
          config:
            environment:
              CONFIG_SERVICE_PASSWORD: $CONFIG_SERVICE_PASSWORD
            image: sqshq/piggymetrics-config
            restart: always
            ports:
              - 8888
            logging:
              options:
                max-size: "10m"
                max-file: "10"
        
          registry:
            environment:
              CONFIG_SERVICE_PASSWORD: $CONFIG_SERVICE_PASSWORD
            image: sqshq/piggymetrics-registry
            restart: always
            depends_on:
              - config
            labels:
              kompose.service.type: loadbalancer
            ports:
              - 8761:8761
            logging:
              options:
                max-size: "10m"
                max-file: "10"
        
          gateway:
            environment:
              CONFIG_SERVICE_PASSWORD: $CONFIG_SERVICE_PASSWORD
            image: sqshq/piggymetrics-gateway
            restart: always
            depends_on:
              - config
            labels:
              kompose.service.type: loadbalancer
            ports:
              - 4000:4000
            logging:
              options:
                max-size: "10m"
                max-file: "10"
        
          auth-service:
            environment:
              CONFIG_SERVICE_PASSWORD: $CONFIG_SERVICE_PASSWORD
              NOTIFICATION_SERVICE_PASSWORD: $NOTIFICATION_SERVICE_PASSWORD
              STATISTICS_SERVICE_PASSWORD: $STATISTICS_SERVICE_PASSWORD
              ACCOUNT_SERVICE_PASSWORD: $ACCOUNT_SERVICE_PASSWORD
              MONGODB_PASSWORD: $MONGODB_PASSWORD
            image: sqshq/piggymetrics-auth-service
            restart: always
            ports:
              - 5000
            depends_on:
              - config
            logging:
              options:
                max-size: "10m"
                max-file: "10"
        
          auth-mongodb:
            environment:
              MONGODB_PASSWORD: $MONGODB_PASSWORD
            image: sqshq/piggymetrics-mongodb
            restart: always
            ports:
              - 27017
            logging:
              options:
                max-size: "10m"
                max-file: "10"
        
          account-service:
            environment:
              CONFIG_SERVICE_PASSWORD: $CONFIG_SERVICE_PASSWORD
              ACCOUNT_SERVICE_PASSWORD: $ACCOUNT_SERVICE_PASSWORD
              MONGODB_PASSWORD: $MONGODB_PASSWORD
            image: sqshq/piggymetrics-account-service
            restart: always
            ports:
              - 6000
            depends_on:
              - config
            logging:
              options:
                max-size: "10m"
                max-file: "10"
        
          account-mongodb:
            environment:
              INIT_DUMP: account-service-dump.js
              MONGODB_PASSWORD: $MONGODB_PASSWORD
            image: sqshq/piggymetrics-mongodb
            restart: always
            ports:
              - 27017
            logging:
              options:
                max-size: "10m"
                max-file: "10"
        
          statistics-service:
            environment:
              CONFIG_SERVICE_PASSWORD: $CONFIG_SERVICE_PASSWORD
              MONGODB_PASSWORD: $MONGODB_PASSWORD
              STATISTICS_SERVICE_PASSWORD: $STATISTICS_SERVICE_PASSWORD
            image: sqshq/piggymetrics-statistics-service
            restart: always
            ports:
              - 8888
            depends_on:
              - config
            logging:
              options:
                max-size: "10m"
                max-file: "10"
        
          statistics-mongodb:
            environment:
              MONGODB_PASSWORD: $MONGODB_PASSWORD
            image: sqshq/piggymetrics-mongodb
            restart: always
            ports:
              - 27017
            logging:
              options:
                max-size: "10m"
                max-file: "10"
        
          notification-service:
            environment:
              CONFIG_SERVICE_PASSWORD: $CONFIG_SERVICE_PASSWORD
              MONGODB_PASSWORD: $MONGODB_PASSWORD
              NOTIFICATION_SERVICE_PASSWORD: $NOTIFICATION_SERVICE_PASSWORD
            image: sqshq/piggymetrics-notification-service
            restart: always
            ports:
              - 8000
            depends_on:
              - config
            logging:
              options:
                max-size: "10m"
                max-file: "10"
        
          notification-mongodb:
            image: sqshq/piggymetrics-mongodb
            restart: always
            environment:
              MONGODB_PASSWORD: $MONGODB_PASSWORD
            ports:
              - 27017
            logging:
              options:
                max-size: "10m"
                max-file: "10"
        
          monitoring:
            environment:
              CONFIG_SERVICE_PASSWORD: $CONFIG_SERVICE_PASSWORD
            image: sqshq/piggymetrics-monitoring
            restart: always
            depends_on:
              - config
            labels:
              kompose.service.type: loadbalancer
            ports:
              - 9000:8080
              - 8989:8989
            logging:
              options:
                max-size: "10m"
                max-file: "10"
        ```

2.  使用Kompose生成Kubernetes配置文件来部署PiggyMetrics应用。
    1.  设定PiggyMetrics部署所需的环境变量。

        ``` {#codeblock_iwu_x3d_bii}
        export NOTIFICATION_SERVICE_PASSWORD=passw0rd
        export CONFIG_SERVICE_PASSWORD=passw0rd
        export STATISTICS_SERVICE_PASSWORD=passw0rd
        export ACCOUNT_SERVICE_PASSWORD=passw0rd
        export MONGODB_PASSWORD=passw0rd
        ```

    2.  运行以下命令将compose文件转换为Kubernetes配置文件。

        [kompose](https://github.com/kubernetes/kompose)工具可以一键将compose文件转换为Kubernetes配置文件。

        ``` {#codeblock_wm3_nhw_dsj}
        kompose convert -f docker-compose.yml -f docker-compose.dev.yml -o piggymetrics -c
        ```

    3.  运行helm install命令，在Kubernetes集群中部署PiggyMetrics应用。

        例如，您可以运行以下命令，在命名空间pm中部署名为piggy的应用。

        ``` {#codeblock_hhm_49i_579}
        helm install --namespace pm --name piggy piggymetrics/
        ```

3.  确保本地的Helm客户端与远程的Helm客户端版本相同。

    **说明：** 如果本地的Helm客户端与远程的Helm客户端版本不匹配时，会弹出如下错误提示：

    ``` {#codeblock_711_c5v_d7p}
    Error: incompatible versions client[v2.14.1] server[v2.11.0]
    ```

    如果没有显示此错误消息，您可以直接忽略此步骤。

    -   如果是您是MAC系统，请运行以下命令获取Helm客户端的2.11.0版本。

        ``` {#codeblock_bp7_y1t_ckx}
        brew unlink kubernetes-helm
        brew install https://raw.githubusercontent.com/Homebrew/homebrew-core/ee94af74778e48ae103a9fb080e26a6a2f62d32c/Formula/kubernetes-helm.rb               
        ```

    -   如果您使用的是Windows或Linux系统，请按照相应的Helm客户端。
4.  部署完成后可以看输出成功信息。

    您可以在自己的Minikube上尝试，或者部署到阿里云容器服务Kubernetes版：[https://www.aliyun.com/product/kubernetes](https://www.aliyun.com/product/kubernetes?spm=a2c4e.11153940.blogcont610430.21.40667737Uedu2h)。部署完成后进入服务列表页面，可以看到所有服务以及对应LoadBalancer类型Service对外暴露的访问地址及端口号。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16138/15639303747369_zh-CN.png)

    -   单击**registry service**可以进入到PiggyMetrics的界面。

        PiggyMetrics是个人理财服务，用户输入收入和支出后可以展现漂亮的报表。

    -   访问registry service，可以看到所有注册到Eureka Server上的服务。
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16138/15639303747371_zh-CN.png)

5.  您可以执行如下命令，删除PiggyMetrics。

    ``` {#codeblock_xof_wv9_xth}
    helm delete --purge piggymetrics
    release "piggymetrics" deleted
    ```


## 场景二 部署SpringCloud基础组件 {#section_ngh_2nr_n2b .section}

上面您看到的是如何将全部基础组件（Eureka，Zuul，ConfigServer，Hystrix Dashboard）和业务应用（gateway，notification，statistics）都统一用一个helm chart部署成功。在实际工作中，更常见的情况是在集群中已经有了Eureka等基础组件，您只需部署和升级维护业务应用。

1.  在阿里云容器服务Kubernetes版中，单击左侧导航栏**市场** \> **应用目录**，在右侧的应用目录中可以看到包含了SpringCloud基本组件。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16138/15639303747390_zh-CN.png)

2.  单击**ack-springcloud-eureka**组件，进入如下界面：

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16138/15639303747391_zh-CN.png)

    您也可以单击**参数**，进入参数页面，查看或更改配置：

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16138/15639303747392_zh-CN.png)

3.  在这里选择不改变任何参数，单击右侧**创建**，启动部署。

    部署成功后，在阿里云容器服务控制台中，单击左侧导航栏中**路由与负载均衡** \> **服务**，选择集群和命名空间，进入服务列表页面。可以看到，EurekaServer有两个示例，对外暴露的服务地址为`ack-springcloud-eureka-default-ack-springcloud-eureka-svc`。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16138/15639303747393_zh-CN.png)

4.  您可以参考[场景一](#section_hgh_2nr_n2b)部署一个没有Eureka的PiggyMetrics应用。

    所有服务启动成功后，访问Eureka，可以看到所有PiggyMetrics服务均已正确地注册到了EurekaServer中。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16138/15639303747397_zh-CN.png)

    PiggyMetrics应用已经部署到了包含EurekaServer的环境上。访问`GATEWAY`即可看到熟悉的登录界面。


