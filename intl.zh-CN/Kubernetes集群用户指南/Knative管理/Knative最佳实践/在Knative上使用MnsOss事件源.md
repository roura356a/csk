# 在Knative上使用MnsOss事件源

通过MnsOss事件源采集OSS事件，然后驱动服务对事件进行处理，可用于人脸识别等场景。本文介绍如何在Knative上使用MnsOss事件源。

-   已安装Knative Serving和Knative Eventing。具体操作，请参见[部署Knative](/intl.zh-CN/Kubernetes集群用户指南/Knative管理/Knative组件管理/部署Knative.md)。
-   已在OSS控制台创建Bucket。具体操作，请参见[创建存储空间](/intl.zh-CN/控制台用户指南/存储空间管理/创建存储空间.md)。
-   已开通MNS服务。具体操作，请参见[开通MNS服务]()。

## 步骤一：安装MnsOss事件源

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页面选择**应用** \> **Knative**。

5.  在**组件管理**页签下单击MnsOss**操作**列的**部署**。

6.  在**部署MnsOSS**对话框中单击**确定**。


## 步骤二：创建OSS事件通知

1.  登录[OSS管理控制台](https://oss.console.aliyun.com/)。

2.  单击**Bucket列表**，之后单击目标Bucket名称。

3.  单击**基础设置** \> **事件通知**，之后单击**设置**。

4.  单击**创建规则**，在**创建规则**对话框配置事件通知。

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1594459951/p11309.png)

    各配置项说明见下表：

    |配置项|说明|
    |---|--|
    |**规则名称**|事件通知规则的名称，自定义。|
    |**事件类型**|选择一个或多个需要进行消息通知的事件，同样的事件不可以多次配置在同一资源上。事件类型对应OSS资源的不同操作，具体类型和含义请参见[事件类型](/intl.zh-CN/开发指南/事件通知.md)。|
    |**资源描述**|填写您希望关注的Object信息，可以是全名、前缀、后缀以及前后缀，不同资源描述不能有交集。填写之后，此条事件通知规则仅在对应Object发生改变时发出消息通知。     -   **全名**：输入某个Object的完整路径可以精确关注到此Object，例如test/myphoto/photo1.jpg。
    -   **前后缀**：设置Object的前后缀可以关注一个Bucket中的全部或者部分Object。例如对于名为`nightbucket`的Bucket：
        -   要关注其中所有的文件，则前缀、后缀都不填。
        -   要关注其中目录movie下的所有文件，则前缀填 movie/，后缀不填。
        -   要关注其中所有的.jpg的图片，则前缀不填，后缀填 .jpg。
        -   要关注其中目录movie下的.mp3格式的影片，则前缀填 movie/，后缀填 .mp3。
**说明：** 您可以通过**添加**按钮，创建最多5条**资源描述**。 |
    |**接收终端**|添加一个或多个接收终端。事件通知消息的接收端目前支持HttpServer和Queue。|

    **说明：**

    -   OSS事件通知功能在某个操作规则匹配之后，会生成相关消息描述，并将该消息发布到MNS的Topic中，然后根据该Topic上的订阅，将消息推送到具体的接收终端上。
    -   阿里云消息服务MNS是收费服务，计费详情请参见[t1835565.dita\#concept\_2028746]()。
    -   报错信息请参见[错误码]()。
5.  单击**确定**。

    OSS事件通知完成后，会在[消息服务MNS控制台](https://mns.console.aliyun.com/)的主题页面生成一条主题。


## 步骤三：创建MNS Token

1.  登录[消息服务MNS控制台](https://mns.console.aliyun.com/)。

2.  在顶部菜单栏，选择地域。

3.  在主题页面单击**获取Endpoint**。

4.  在**获取EndPoint**对话框中复制公网访问链接。

5.  获取AK和SK信息。具体操作，请参见[如何获取AccessKeyId和AccessKeySecret](/intl.zh-CN/常见问题/一般性问题/如何获取AccessKeyId和AccessKeySecret.md)。

6.  执行以下命令，对获取到的公网访问链接、AK和SK进行Base64编码处理，生成访问Token。

    ```
    echo '{ "url":"https://xxxx.mns.cn-shanghai.aliyuncs.com/", "accessKeyId":"xxx","accessKeySecret":"xx" }' | base64
    ```

7.  创建Secret，用于管理和存储Token。

    1.  创建mnsoss-secret.yaml。

        ```
        apiVersion: v1
        kind: Secret
        metadata:
          name: mnsoss-secret
        type: Opaque
        data:
          mns: eyAidXJsIjoiaHR0cHM6Ly94eHh4Lm1ucy5jbi1zaGFuZ2hhaS5hbGl5dW5jcy5jb20vIiwgImFjY2Vzc0tleUlkIjoieHh4IiwiYWNjZXNzS2V5U2VjcmV0IjoieHgiIH0K
        ```

        将`mns`替换成步骤[6](#step_k36_cbs_rqi)生成的Token。

    2.  执行以下命令，使mnsoss-secret.yaml生效。

        ```
        kubectl apply -f mnsoss-secret.yaml
        ```


## 步骤四：创建Service Account和Broker

1.  创建Service Account。

    1.  创建mnsoss-sa.yaml。

        ```
        apiVersion: rbac.authorization.k8s.io/v1
        kind: ClusterRoleBinding
        metadata:
          name: eventing-sources-mnsoss
        subjects:
        - kind: ServiceAccount
          name: mnsoss-sa
          namespace: default
        roleRef:
          apiGroup: rbac.authorization.k8s.io
          kind: ClusterRole
          name: eventing-sources-mnsoss-controller
        ---
        apiVersion: v1
        kind: ServiceAccount
        metadata:
          name: mnsoss-sa
        ```

    2.  执行以下命令，使mnsoss-sa.yaml生效。

        ```
        kubectl apply -f mnsoss-sa.yaml
        ```

2.  执行以下命令，创建Broker。

    ```
    kubectl label namespace default knative-eventing-injection=enabled
    ```


## 步骤五：创建MnsOss事件源

为了接收MnsOss产生的事件， 需要创建MnsOss Source用于接收事件。

1.  创建mnsoss-source.yaml。

    ```
    apiVersion: sources.eventing.knative.dev/v1alpha1
    kind: MnsOssSource
    metadata:
      labels:
        controller-tools.k8s.io: "1.0"
      name: mnsoss-face
    spec:
      # Add fields here
      serviceAccountName: mnsoss-sa
      accessToken:
        secretKeyRef:
          name: mnsoss-secret
          key: mns
      sink:
        apiVersion: eventing.knative.dev/v1alpha1
        kind: Broker
        name: default
      topic: mns-en-topics-oss-face-image-2381221888dds9129
    ```

    `topic`：表示MNS主题名称。

2.  执行以下命令，使mnsoss-source.yaml生效。

    ```
    kubectl  apply -f mnsoss-source.yaml
    ```


## 步骤六：创建Knative Service

为了验证MnsOss Source是否可以正常工作，需要创建Knative Service，以下使用的是event-display示例。

1.  创建service.yaml。

    ```
    apiVersion: serving.knative.dev/v1alpha1
    kind: Service
    metadata:
      name: event-dispaly
      namespace: default
    spec:
      template:
        spec:
          containers:
          - image: registry.cn-hangzhou.aliyuncs.com/knative-sample/event-display:1215
    ```

2.  执行以下命令，使service.yaml生效。


## 步骤七：创建Trigger

创建Trigger， 订阅事件信息。

1.  创建trigger.yaml。

    ```
    apiVersion: eventing.knative.dev/v1alpha1
    kind: Trigger
    metadata:
      name: oss-trigger
      namespace: default
    spec:
      subscriber:
        ref:
          apiVersion: serving.knative.dev/v1alpha1
          kind: Service
          name: event-dispaly
    ```

2.  执行以下命令，使trigger.yaml生效。

    ```
    kubectl apply -f trigger.yaml
    ```


## 执行结果

往OSS上传文件，会在Pod中收到事件通知。

```
2020/12/16 13:04:19 receive cloudevents.Event:
{"events": [{
            "eventName": "ObjectCreated:PostObject",
            "eventSource": "acs:oss",
            "eventTime": "2019-06-18T06:44:16.000Z",
            "eventVersion": "1.0",
            "oss": {
                "bucket": {
                    "arn": "acs:oss:cn-beijing:1041208914252405:testjian",
                    "name": "testjian",
                    "ownerIdentity": "1041208914252405",
                    "virtualBucket": ""},
                "object": {
                    "deltaSize": 0,
                    "eTag": "137138904F2E18D307D04EB38EA44CDA",
                    "key": "timg.jpg",
                    "size": 12990},
                "ossSchemaVersion": "1.0",
                "ruleId": "demo-i****"},
            "region": "cn-beijing",
            "requestParameters": {"sourceIPAddress": "42.120.7*.***"},
            "responseElements": {"requestId": "5D08884070BC12B192C6****"},
            "userIdentity": {"principalId": "104120891425****"}}]}
```

