# 基于Java应用的DevOps示例 {#concept_jyd_jlc_5db .concept}

阿里云研发了开源持续交付工具 CodePipeline，致力于让代码的持续交付更简单、安全、高效。它提供多种语言的持续交付向导模板，通过模板快速填写进行持续集成，从而实现多平台、多环境的持续交付。

CodePipeline 与阿里云容器服务进行了深度集成，提供了容器化的完整解决方案，实现开发过程中代码构建、部署和验证管理等全生命周期操作。通过 CodePipeline 可以构建您的代码工作流模板，从代码编译/测试，到容器镜像的构建，再到容器环境的发布，打通容器环境下应用发布全过程自动化。

更多的应用场景，请参考[CodePipeline 应用场景](https://help.aliyun.com/document_detail/56518.html)。

本示例将演示一个 java 类型项目，执行镜像构建和发布，部署到 Kubernetes 这两个步骤的构建流程。需要用到阿里云code 代码仓库、容器镜像服务、容器服务 Kubernetes 集群，以及 CodePipeline，来实现一个 DevOps 实践案例。

## 前提条件 {#section_evs_zmc_5db .section}

**准备阿里云 code 代码仓库**

首先登录 [阿里云 code 管理控制台](https://code.aliyun.com/)。然后 Fork 本示例使用的 CodePipeline 官方示例项目 Java-demo，项目地址是 [https://code.aliyun.com/CodePipeline/java-demo](https://code.aliyun.com/CodePipeline/java-demo) 。

**创建 kubernetes 集群**

登录[容器服务管理控制台](https://cs.console.aliyun.com/)创建一个Kubernetes 集群，具体请参见[快速创建Kubernetes集群](cn.zh-CN/快速入门/基础入门/快速创建Kubernetes集群.md#)。

## 创建阿里云容器镜像仓库 {#section_dtp_cmc_5db .section}

1.  登录 [阿里云容器镜像服务](https://cr.console.aliyun.com/)。
2.  在左侧导航栏中，单击**镜像列表**，再单击右上角**创建镜像仓库**。
3.  配置镜像仓库。选择与容器集群相同的地域，设置代码源为前面准备的代码仓库 java-demo。最后单击 **创建镜像仓库**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16046/15354497857400_zh-CN.png)

4.  镜像仓库创建成功后，会出现在镜像仓库列表下。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16046/15354497857401_zh-CN.png)


如果您是首次使用阿里云容器镜像服务，需要设置 Registry 登录密码，请单击**修改 Registry 登录密码** 进行设置。

## 使用 CodePipeline 进行持续构建 {#section_k1w_fqc_5db .section}

**配置用户证书**

1.  登录 [CodePipeline 管理控制台](https://cds.console.aliyun.com/)。
2.  单击左侧导航栏中的**证书**，然后单击右上角**添加证书**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16046/15354497857402_zh-CN.png)

3.  您需要配置用户证书，来对某些构建步骤进行授权。目前 CodePipeline 提供用户名和密码、Docker授权，Registry授权、SSH用户名和私钥四种类型的证书。
4.  本例中需要配置授权证书，具体如何配置可参见[证书配置](https://help.aliyun.com/document_detail/56529.html)。在这里我们先准备两个证书以备后用。也可以在后续的构建配置中实时创建添加。
    -   第一个是镜像仓库的证书，选择用户名密码的方式；按下图填入以下内容，在这里最好添加描述信息，方便后续使用。ID不用填，在点击添加后会自动生成。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16046/15354497857403_zh-CN.png)

    -   第二个证书是kubernetes的证书，选择docker授权的方式。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16046/15354497857404_zh-CN.png)

        这里需要填入客户端key\(key.pem\)和客户端证书\(cert.pem\)，这两个内容可以在容器服务平台上获取。在容器服务平台上单击**集群** \> **管理**，进入基本信息页面，在这里可以找到以上内容。如下图所示：

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16046/15354497857405_zh-CN.png)

        将上图中的client-certificate-data填入客户端证书\(cert.pem\)；client-key-data填入客户端key\(key.pem\)，单击确定完成配置。


## 修改 deployment.yaml 文件 {#section_ltp_cmc_5db .section}

在本例中，会执行两个构建步骤。

-   通过镜像构建和发布，会将镜像推送到准备好的镜像仓库中，您可以修改 Dockerfile 文件，本例中不做修改。
-   部署到 Kubernetes 集群中。本例中使用 deployment.yaml 文件，为了验证镜像从构建、发布，再到创建应用的完整流程，要修改该示例项目中的 deployment.yaml 文件，将其镜像地址修改为本例中的镜像仓库地址 `registry.cn-hangzhou.aliyuncs.com/test/codepipeline_registry`。示例编排如下所示。

    ```
    apiVersion: extensions/v1beta1
    kind: Deployment
    metadata:
      name: java-demo
    spec:
      replicas: 2
      selector:
        matchLabels:
          app: java-demo
      template:
        metadata:
          labels:
            app: java-demo
        spec:
          containers:
          - name: java-demo
            image: registry.cn-shanghai.aliyuncs.com/registry-test/java-demo   #修改处, BUILD_NUMBER会在后面用到
            imagePullPolicy: Always
            ports:
            - containerPort: 8080
    ```


## 开始进行应用构建 {#section_d1l_rqc_5db .section}

1.  登录 [CodePipeline 管理控制台](https://cds.console.aliyun.com/)。
2.  单击右上角 **新建**，开始构建一个新项目。本例中选择构建一个 java 类型的应用，名称为 java-demo，然后单击**下一步**。
3.  配置项目参数，您可以直接输入 Repositories 地址，证书、分支、构建指令和测试指令。部分参数可以使用默认值。完成配置后，单击 **下一步**。

    **说明：** 如果您选择使用阿里云 code 进行源码管理，可跳过该步骤。后面可以直接绑定阿里云 code 代码仓库。CodePipeline 用户会直接绑定阿里云 Code 账户，自动列出您的代码仓库及其对应的分支或 Tag。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16046/15354497857408_zh-CN.png)

4.  选择**部署到 kubernetes**，对镜像构建与发布，部署 Kubernetes 这两个步骤进行参数配置，完成配置后，单击**下一步**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16046/15354497857409_zh-CN.png)

    -   镜像构建与发布。
        -   镜像仓库名称：镜像仓库名格式为`{namespace}/{image name}`。
        -   镜像版本号：自由设置，为会构建的镜像打上这个 tag，默认为latest。
        -   Registry 地址：用来配置 Docker registry 地址。如果为空，默认使用 Docker hub registry ；如果使用阿里云 registry， 需要根据实际情况进行更改，其他的都不需要更改，比如https://registry.cn-hangzhou.aliyuncs.com/v2/ 只需修改hangzhou这个地域。
        -   Registry 证书：您需要设置 Regostry 证书来添加授权信息。需要预先添加 Registry 授权类型的证书。
        -   Dockerfile 路径：填写 Dockerfile 文件在该项目工作空间的相对路径。如果该配置为空，则默认使用工作空间目录下命名为 Dockerfile 的文件。
    -   部署 Kubernetes。
        -   证书认证：CodePipeline目前支持证书认证、用户名密码认证 和 Token认证三种认证方式。本例使用证书认证。
        -   API服务器地址：即API Server公网连接地址，您可在容器服务控制台上获取，单击**集群** \> **管理**，在连接信息中查看该地址。
        -   证书：前面配置的Docker授权类型的证书。
        -   部署配置文件：填写yaml格式的Kubernetes部署配置文件，本例中是根目录下的 deployment.yaml 编排文件。
        -   状态检查配置：支持检验的 Kubernetes Kind: `pods,daemonsets,deployments,replicasets,replicationcontrollers,statefulsets`。如果检验的不是 `default` namespace 下的资源，请在首行填写 namespace 名称。
        -   变量申明配置：支持系统环境变量，可以通过 $\{JENKINS\_URL\}/env-vars.html/ 查看。如果使用了多个变量，请用”,”分隔。
5.  进入最终确认页面，在此页面中，大部分内容已经配置完毕，属于配置确认界面。还需要完成两件事情。
    1.  在前面的步骤中，代码仓库管理没有配置，所以还需要在这里配置这一项，其他的都不需要配置。

        **说明：** 我们使用阿里云code，会自动进行关联。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16046/15354497857419_zh-CN.png)

    2.  您也可选择是否构建触发器，进行快速构建。生成并复制触发器地址，将其粘贴到浏览器中，按下回车键，浏览器窗口会提示您开始了一次构建。具体请参考 [构建触发器](https://help.aliyun.com/document_detail/59648.html)。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16046/15354497867420_zh-CN.png)

        这里，我们可以将该url填入到代码仓库的webhook中，当代码有变动时会通过该url触发codepipeline进行构建。Webhook配置如下图：

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16046/15354497867422_zh-CN.png)

6.  单击 **提交**。
7.  返回概览页面，在项目列表中，选择对应的项目，并单击右侧的**执行构建**。该项目会出现在构建队列下，单击该任务，进入项目详情页面。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16046/15354497867423_zh-CN.png)

8.  在构建历史下，单击对应的构建任务，然后单击左侧导航栏中的**控制台输出**，您可以查看该次构建过程输出的日志。

    本例中，输出日志最终提示成功创建对应的 deployment 对象。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16046/15354497867424_zh-CN.png)

9.  您可以登录 [容器服务管理控制台](https://cs.console.aliyun.com/)，在目标集群右侧的**控制台** \> **部署** 下看到成功部署了 java-demo 对象 。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16046/15354497867425_zh-CN.png)


