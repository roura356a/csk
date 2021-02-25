# Use Knative to handle MnsOss event sources

You can collect Object Storage Service \(OSS\) events from MnsOss event sources and handle the events accordingly. This is suitable for scenarios where facial recognition is required. This topic describes how to use Knative to handle MnsOss event sources.

-   Knative Serving and Knative Eventing are installed. For more information, see [Deploy Knative](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Manage Knative components/Deploy Knative.md).
-   An OSS bucket is created in the OSS console. For more information, see [Create buckets](/intl.en-US/Console User Guide/Manage buckets/Create buckets.md).
-   Message Service \(MNS\) is activated. For more information, see [Activate Message Service]().

## Step 1: Deploy MnsOss

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster, or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  On the details page of the cluster, choose **Application** \> **Knative**.

5.  On the **Components** tab, find MnsOss and click **Deploy** in the **Actions** column.

6.  In the**Deploy MnsOSS** dialog box, click **OK**.


## Step 2: Configure OSS event notification settings

1.  Log on to the [OSS console](https://oss.console.aliyun.com/).

2.  Click **Buckets**, and then click the name of the target bucket.

3.  In the left-side navigation pane, choose **Basic Settings** \> **Event Notification**. In the Event Notification section, click **Configure**.

4.  Click **Create Rule**. In the **Create Rule** panel, configure an event notification rule.

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3867549951/p11309.png)

    The following table describes the parameters.

    |Parameter|Description|
    |---------|-----------|
    |**Rule Name**|Specify the name of the event notification rule.|
    |**Events**|Select one or more events that require message notifications. An event can be selected only once for a resource. Event types correspond to different operations on OSS resources. For more information about event types and their descriptions, see [Event types](/intl.en-US/Developer Guide/Event notification.md).|
    |**Resource Description**|Set the information of the object that you want to follow. The resource description can be a full name, a prefix, a suffix, or a combination of a prefix and a suffix. Different resource descriptions cannot overlap. After this parameter is specified, this event notification rule sends message notifications only when the corresponding object changes.     -   **Full Name**: Set the complete path of an object to receive notifications about the object. Example: test/myphoto/photo1.jpg.
    -   **Prefix and Suffix**: Set the prefix and suffix to receive notifications about all or multiple objects in a bucket. For example, for a bucket named `nightbucket`:
        -   To receive notifications about all objects, do not set Prefix or Suffix.
        -   To receive notifications about objects in the movie folder, set Prefix to movie/ and leave Suffix unspecified.
        -   To receive notifications about all .jpg images, set Suffix to .jpg and leave Prefix unspecified.
        -   To receive notifications about videos in the MP3 format in the movie folder, set Prefix to movie/ and Suffix to .mp3.
**Note:** You can click **Add** to specify a maximum of five entries for **Resource Description**. |
    |**Endpoint**|Add one or more endpoints. Event notifications can be sent to one or more HTTP endpoints or queues.|

    **Note:**

    -   When a notification rule is triggered by an operation, OSS event notification generates a message and publishes the message to an MNS topic. MNS sends the message to the subscribed endpoint.
    -   MNS is a paid service. For more information, see [t1835565.dita\#concept\_2028746]().
    -   For more information about error messages, see [Error codes]().
5.  Click **OK**.

    After you configure the OSS event notification settings, a topic is created on the Topics page in the [MNS](https://mns.console.aliyun.com/) console.


## Step 3: Create an MNS token

1.  Log on to the [MNS console](https://mns.console.aliyun.com/).

2.  In the top navigation bar of the MNS console, select the region where your cluster is deployed.

3.  On the Topics page, click **Get Endpoint** in the upper-right corner of the page.

4.  In the **Get Endpoint** dialog box, copy the Internet endpoint.

5.  Obtain an AccessKey ID and AccessKey secret of the current account. For more information, see [How can I obtain an AccessKey ID and AccessKey secret?](/intl.en-US/FAQ/General FAQ/How can I obtain an AccessKey ID and AccessKey secret?.md).

6.  Run the following command to encode the Internet endpoint, AccessKey ID, and AccessKey secret by using Base 64. A token is generated.

    ```
    echo '{ "url":"https://xxxx.mns.cn-shanghai.aliyuncs.com/", "accessKeyId":"xxx","accessKeySecret":"xx" }' | base64
    ```

7.  Create a Secret to store and manage the token.

    1.  Create a file named mnsoss-secret.yaml.

        ```
        apiVersion: v1
        kind: Secret
        metadata:
          name: mnsoss-secret
        type: Opaque
        data:
          mns: eyAidXJsIjoiaHR0cHM6Ly94eHh4Lm1ucy5jbi1zaGFuZ2hhaS5hbGl5dW5jcy5jb20vIiwgImFjY2Vzc0tleUlkIjoieHh4IiwiYWNjZXNzS2V5U2VjcmV0IjoieHgiIH0K
        ```

        Replace the value of `mns` with the token that is generated in Step [6](#step_k36_cbs_rqi).

    2.  Run the following command to apply the configuration in the mnsoss-secret.yaml file.

        ```
        kubectl apply -f mnsoss-secret.yaml
        ```


## Step 4: Create a service account and a service broker

1.  Create a service account.

    1.  Create a file named mnsoss-sa.yaml.

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

    2.  Run the following command to apply the configuration in the mnsoss-sa.yaml file.

        ```
        kubectl apply -f mnsoss-sa.yaml
        ```

2.  Run the following command to create a service broker.

    ```
    kubectl label namespace default knative-eventing-injection=enabled
    ```


## Step 5: Create an MnsOss event source

To receive MnsOss events, you must create an MnsOss event source.

1.  Create a file named mnsoss-source.yaml.

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

    Set `topic` to the name of the topic that is generated in the MNS console.

2.  Run the following command to apply the configuration in the mnsoss-source.yaml file.

    ```
    kubectl  apply -f mnsoss-source.yaml
    ```


## Step 6: Create a Knative Service

Create a Knative Service to test whether the created MnsOss event source works as normal. A Knative Service named event-display is created in this example.

1.  Create a file named service.yaml.

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

2.  Run the following command to apply the configuration in the service.yaml file.


## Step 7: Create a trigger

Create a trigger to subscribe to OSS events.

1.  Create a file namedtrigger.yaml.

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

2.  Run the following command to apply the configuration in the trigger.yaml file.

    ```
    kubectl apply -f trigger.yaml
    ```


## Check results

When you upload files to the OSS bucket, event notifications are sent to pods.

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

