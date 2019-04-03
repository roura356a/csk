# 基于Istio实现服务的灰度发布 {#concept_tq1_5qh_hhb .concept}

为解决微服务的分布式应用架构在运维、调试、和安全管理等维度存在的问题，可通过部署Istio创建微服务网络，并提供负载均衡、服务间认证以及监控等能力。阿里云容器服务Kubernetes基于Istio提供了服务治理能力，对应用服务提供多版本管理、灵活的流量治理策略，以支持多种灰度发布场景。

## 前提条件 {#section_lf2_nrh_hhb .section}

-   您已经成功创建一个 Kubernetes 集群，参见[创建Kubernetes集群](../../../../../intl.zh-CN/用户指南/Kubernetes集群/集群管理/创建Kubernetes集群.md#)。
-   您已成功在该集群中[部署Istio](../../../../../intl.zh-CN/用户指南/Kubernetes集群/Istio管理/部署Istio.md#)。

## 操作步骤 {#section_iyf_bsh_hhb .section}

1.  启用Sidecar自动注入。
    1.  登录 [容器服务管理控制台](https://cs.console.aliyun.com/)。
    2.  单击左侧导航栏中的**集群** \> **命名空间**，进入命令空间页面。
    3.  单击**创建**，创建一个新的命名空间。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155425568142293_zh-CN.png)

    4.  在弹出的创建命名空间对话框中，输入**名称**为demo，添加以下**标签**后，单击**添加**。

        -   变量称为istio-injection
        -   变量值为enabled
        **说明：** 注意一定要单击**添加**后再单击**确定**按钮，以确保输入的标签添加成功。

    5.  单击**确定**。
    6.  查看添加的命名空间。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155425568143151_zh-CN.jpg)

2.  创建虚拟服务（Virtual Service）。
    1.  登录 [容器服务管理控制台](https://cs.console.aliyun.com/)。
    2.  单击左侧导航栏中**服务网格** \> **虚拟服务**，进入虚拟服务（Virtual Service）页面。
    3.  单击**创建**，创建一个新的虚拟服务。![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155425568143152_zh-CN.jpg)
    4.  在创建应用页面进行设置，然后单击**下一步** 进入容器配置页面。

        -   应用名称：设置应用的名称。
        -   应用版本：设置应用的版本，此处用于做多版本管理。
        -   部署集群：设置应用部署的集群。
        -   命名空间：设置应用部署所处的命名空间，此处是指步骤1所创建的命名空间demo。
        -   副本数量：即应用包含的Pod数量。
        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155425568143153_zh-CN.jpg)

    5.  设置**容器配置**。本例中指定镜像名称为**nginx**，镜像版本为**1.14**，其他配置可以参考[镜像创建无状态Deployment应用](../../../../../intl.zh-CN/用户指南/Kubernetes集群/应用管理/镜像创建无状态Deployment应用.md#)中的设置容器配置步骤。完成容器配置后，单击**下一步**进入服务配置页面。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155425568143154_zh-CN.jpg)

    6.  设置**服务配置**。
        1.  在服务栏单击**创建**，在弹出的对话框中进行配置。

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155425568143155_zh-CN.jpg)

            -   名称：可自主设置，例如nginx。
            -   类型：虚拟集群 IP。选择该项，才能通过Istio服务网格来对服务进行治理。
            -   端口映射：需要添加服务端口的名称、服务端口和容器端口，及TCP协议。**服务端口必须进行命名**。端口名称只允许是<协议\>\[-<后缀\>-\]模式，其中<协议\>部分可选择范围包括 grpc、http、http2、https、mongo、redis、tcp、tls 以及 udp，Istio 可以通过对这些协议的支持来提供路由能力。
            -   注解：为该服务添加一个注解（annotation）。
            -   标签：您可为该服务添加一个标签，标识该服务。
            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155425568143156_zh-CN.jpg)

        2.  配置完成后，单击**创建**。

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155425568143157_zh-CN.jpg)

    7.  单击**创建**，所有配置完成后，默认进入创建完成页面，会列出应用包含的对象，您可以单击查看应用详情进行查看。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155425568143158_zh-CN.jpg)

3.  创建服务客户端sleep应用。
    1.  将如下内容保存为sleep.yaml文件。

        ```
        apiVersion: v1
        kind: Service
        metadata:
          name: sleep
          labels:
            app: sleep
        spec:
          ports:
          - port: 80
            name: http
          selector:
            app: sleep
        ---
        apiVersion: extensions/v1beta1
        kind: Deployment
        metadata:
          name: sleep
        spec:
          replicas: 1
          template:
            metadata:
              labels:
                app: sleep
            spec:
              serviceAccountName: sleep
              containers:
              - name: sleep
                image: pstauffer/curl
                command: ["/bin/sleep", "3650d"]
                imagePullPolicy: IfNotPresent
        ```

    2.  在命名空间**demo**下执行如下命令。

        `kubectl apply -f sleep.yaml -n demo`

    3.  执行`kubectl exec -it -n demo <podName> bash`命令，登录到sleep应用的pod中，执行如下命令调用nginx服务。

        ```
        for i in `seq 1000`
        do
        curl -I http://nginx.demo:8080; 
        echo '';
        sleep 1; 
        done;
        ```

        当出现如红框所示内容时，显示调用成功返回。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155425568143159_zh-CN.png)

    4.  如果在安装Istio时打开**启用 Kiali 可视化服务网格**功能，可以通过访问Kiali控制台，查看调用效果。

        请先执行`kubectl port-forward svc/kiali -n istio-system 20001:20001`命令，在本地打开20001端口。在浏览器中输入http://localhost:20001，打开Kiali控制台，查看调用效果。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155425568243160_zh-CN.jpg)

4.  增加灰度版本。
    1.  登录 [容器服务管理控制台](https://cs.console.aliyun.com/)。
    2.  单击左侧导航栏中**服务网格** \> **虚拟服务**，进入虚拟服务（Virtual Service）页面。
    3.  单击对应虚拟服务右侧的**管理**。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155425568243161_zh-CN.jpg)

    4.  进入详细页面后，单击**增加灰度版本**，来增加灰度版本。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155425568243162_zh-CN.jpg)

    5.  设置应用版本、副本数量。然后单击**下一步** 进入容器配置页面。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155425568243163_zh-CN.jpg)

    6.  设置**容器配置**。本例中指定镜像名称为**nginx**，镜像版本为**1.15**，其他配置可以参考[镜像创建无状态Deployment应用](../../../../../intl.zh-CN/用户指南/Kubernetes集群/应用管理/镜像创建无状态Deployment应用.md#)中的设置容器配置步骤。完成容器配置后，单击**下一步**。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155425568243164_zh-CN.jpg)

    7.  指定灰度策略，例如**策略类型**选择**基于流量比例发布**，**v2流量配比**为30%，**v1流量配比**为70%。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155425568243165_zh-CN.jpg)

    8.  单击**创建**，所有配置完成后，单击**服务网格** \> **虚拟服务**，进入虚拟服务（Virtual Service）页面。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155425568243166_zh-CN.jpg)

    9.  单击对应服务右侧的**管理**，进入详情页面。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155425568243167_zh-CN.jpg)

    10. 执行`kubectl exec -it -n demo <podName> bash`命令，登录到sleep应用的pod中，执行如下命令调用nginx服务。

        ```
        for i in `seq 1000`
        do
        curl -I http://nginx.demo:8080; 
        echo '';
        sleep 1; 
        done;
        ```

        当出现如红框所示内容时，显示调用成功返回。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155425568143159_zh-CN.png)

    11. 如果在安装Istio时打开**启用 Kiali 可视化服务网格**功能，可以通过访问Kiali控制台，查看调用效果（如果已经打开请忽略）。

        请先执行`kubectl port-forward svc/kiali -n istio-system 20001:20001`命令，在本地打开20001端口。在浏览器中输入http://localhost:20001，打开Kiali控制台，可以看到流量按照比例分发到不同的版本上。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155425568243168_zh-CN.jpg)

5.  修改灰度策略。
    1.  在详情页中单击**修改灰度策略**，在弹出的对话框中进行配置。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155425568343169_zh-CN.jpg)

    2.  修改流量比例，**版本v1**为20%，**版本v2**为80%。单击**确定**。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155425568343170_zh-CN.jpg)

    3.  如果页面没有刷新，可以单击页面右上方的**刷新**。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155425568343171_zh-CN.jpg)

    4.  执行`kubectl exec -it -n demo <podName> bash`命令，登录到sleep应用的pod中，执行如下命令调用nginx服务。

        ```
            for i in `seq 1000`
            do
            curl -I http://nginx.demo:8080; 
            echo '';
            sleep 1; 
            done;
        ```

        当出现如红框所示内容时，显示调用成功返回。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155425568143159_zh-CN.png)

    5.  在浏览器中输入http://localhost:20001，打开Kiali控制台，可以看到如下调用效果。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155425568343172_zh-CN.jpg)

6.  接管旧版本流量。
    1.  登录 [容器服务管理控制台](https://cs.console.aliyun.com/)。
    2.  单击左侧导航栏中**服务网格** \> **虚拟服务**，进入虚拟服务（Virtual Service）页面。
    3.  单击对应虚拟服务右侧的**管理**，进入详细页面。
    4.  在**版本管理**区域单击版本号**v2**右侧的**接管所有流量**，版本v2将会接管所有流量。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155425568343173_zh-CN.jpg)

    5.  此时详情页面中 ，版本号**v2**对应的灰度策略为**流量比例100%**。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155425568343174_zh-CN.jpg)

    6.  执行`kubectl exec -it -n demo <podName> bash`命令，登录到sleep应用的pod中，执行如下命令调用nginx服务。

        ```
            for i in `seq 1000`
            do
            curl -I http://nginx.demo:8080; 
            echo '';
            sleep 1; 
            done;
        ```

        当出现如红框所示内容时，显示调用成功返回。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155425568143159_zh-CN.png)

    7.  在浏览器中输入http://localhost:20001，再次打开Kiali控制台，可以看到如下调用效果。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155425568443175_zh-CN.jpg)

7.  下线旧版本。
    1.  登录 [容器服务管理控制台](https://cs.console.aliyun.com/)。
    2.  单击左侧导航栏中**服务网格** \> **虚拟服务**，进入虚拟服务（Virtual Service）页面。
    3.  单击对应虚拟服务右侧的**管理**，进入详细页面。
    4.  在**版本管理**区域单击版本号**v1**右侧的**删除版本**，删除v1版本。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155425568443176_zh-CN.jpg)

    5.  旧版本下线之后，只有版本v2在使用中。如果有新版本v3需要灰度，则可以按照如上方式再次进行。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/152533/155425568443177_zh-CN.jpg)


