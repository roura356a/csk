---
keyword: [MnsOss事件源, 人脸识别, OSS事件通知]
---

# 在Knative上使用MnsOss事件源

通过MnsOss事件源采集OSS事件，然后驱动服务对事件进行处理，可用于人脸识别等场景。本文介绍如何在Knative上使用MnsOss事件源。

-   已安装Knative Serving和Knative Eventing。具体操作，请参见[一键部署Knative](/intl.zh-CN/Kubernetes集群用户指南/Knative/Knative组件管理/一键部署Knative.md)。
-   已在OSS控制台创建Bucket。具体操作，请参见[创建存储空间](/intl.zh-CN/控制台用户指南/存储空间管理/创建存储空间.md)。
-   已开通MNS服务。具体操作，请参见[开通消息服务MNS]()。

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

3.  单击**设置**，然后单击**创建规则**。

4.  选择左侧导航栏的**基础设置** \> **事件通知**。

5.  在创建规则面板配置以下参数：

    |参数|说明|
    |--|--|
    |**规则名称**|设置事件通知规则的名称。规则只能包含大小写字母、数字和短划线（-），且长度不超过85个字符。 |
    |**事件类型**|Object操作类型。例如CopyObject表示通过拷贝文件创建或覆盖文件。若多条规则涉及同一Object，则事件类型不允许相同。例如规则A针对前缀examplefolder设置了**CopyObject**事件，若规则B涉及examplefolder前缀下任意Object时，事件类型不能包含**CopyObject**。

事件类型对应的Object操作，请参见[事件类型](/intl.zh-CN/开发指南/事件通知.md)。 |
    |**资源描述**|设置事件通知涉及的目标Object。    -   **全名**：输入目标Object的完整路径，例如examplefolder/myphoto.jpg。
    -   **前后缀**：输入前后缀以匹配目标Object。配置示例如下：
        -   如需匹配Bucket内所有Object，则前缀和后缀均置空。
        -   如需匹配Bucket根目录下examplefolder目录中的所有文件，则前缀填写**examplefolder/**，后缀置空。
        -   如需匹配Bucket内所有的JPG格式的文件，则前缀置空，后缀填写**.jpg**。
        -   如需匹配Bucket根目录下examplefolder目录中MP3格式的文件，则前缀填**examplefolder/**，后缀填写**.mp3**。
您可以单击**添加**按钮，创建最多5条**资源描述**。 |
    |**接收终端**|设置事件的接收终端，支持**HTTP**和**队列**。    -   **HTTP**：填写接收事件通知的HttpEndpoint地址，例如`http://198.51.100.1:8080`。搭建HttpEndpoint的具体操作，请参见[主题使用手册]()和[HttpEndpoint]()。
    -   **队列**：填写您在MNS中创建的队列名称。创建队列的具体操作，请参见[创建队列]()。
您可以单击**添加**按钮，创建最多5个**接收终端**。 |

6.  单击**确定**。

    OSS事件通知完成后，会在[消息服务MNS控制台](https://mns.console.aliyun.com/)的主题页面生成一条主题。


## 步骤三：创建MNS Token

1.  登录[消息服务MNS控制台](https://mns.console.aliyun.com/)。

2.  在控制台左侧导航栏单击**主题列表**。

3.  在顶部菜单栏，选择地域。

4.  在主题列表页面单击目标主题。

5.  在主题页面单击**获取Endpoint**。

6.  在主题详情页面**接入点**区域复制公网访问链接。

7.  获取AK和SK信息。具体操作，请参见[如何获取AccessKeyId和AccessKeySecret](/intl.zh-CN/常见问题/一般性问题/如何获取AccessKeyId和AccessKeySecret.md)。

8.  执行以下命令，对获取到的公网访问链接、AK和SK进行Base64编码处理，生成访问Token。

    ```
    echo '{ "url":"https://xxxx.mns.cn-shanghai.aliyuncs.com/", "accessKeyId":"xxx","accessKeySecret":"xx" }' | base64
    ```

9.  创建Secret，用于管理和存储Token。

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

        将`mns`替换成步骤[8](#step_k36_cbs_rqi)生成的Token。

    2.  执行以下命令，创建Secret。

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

    2.  执行以下命令，创建Service Account。

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

2.  执行以下命令，创建MnsOss事件源。

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

2.  执行以下命令，创建Knative Service。

    ```
    kubectl apply -f service.yaml
    ```


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

2.  执行以下命令，创建Trigger。

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

