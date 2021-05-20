---
keyword: [MnsOss event sources, facial recognition, OSS event notification]
---

# Use Knative to manage MnsOss event sources

You can collect Object Storage Service \(OSS\) events from MnsOss event sources and manage the events accordingly. This is suitable for scenarios where facial recognition is required. This topic describes how to use Knative to manage MnsOss event sources.

-   Knative Serving and Knative Eventing are installed. For more information, see [Deploy Knative](/intl.en-US/User Guide for Kubernetes Clusters/Knative/Manage Knative components/Deploy Knative.md).
-   An OSS bucket is created in the OSS console. For more information, see [Create buckets](/intl.en-US/Console User Guide/Manage buckets/Create buckets.md).
-   Message Service \(MNS\) is activated. For more information, see [Activate Message Service]().

## Step 1: Deploy MnsOss

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  On the details page of the cluster, choose **Applications** \> **Knative**.

5.  On the **Components** tab, find MnsOss and click **Deploy** in the **Actions** column.

6.  In the**Deploy MnsOss** message, click **Confirm**.


## Step 2: Configure OSS event notification settings

1.  Log on to the [OSS console](https://oss.console.aliyun.com/).

2.  Click **Buckets**, and then click the name of the target bucket.

3.  In the Event Notification section, click **Configure**. On the page that appears, click **Create Rule**.

4.  In the left-side navigation pane, choose **Basic Settings** \> **Event Notification**.

5.  In the Create Rule panel, configure the rule parameters. The parameters are described in the following table:

    |Parameter|Description|
    |---------|-----------|
    |**Rule Name**|Specify the name of the event notification rule.The name of an event notification rule can contain only letters, digits, and hyphens \(-\). The name cannot exceed 85 characters in length. |
    |**Events**|Select one or more events that can trigger the event notification rule from the drop-down list. For example, if you select CopyObject, the event notification rule is triggered when specific objects are created or overwritten by copying objects.If multiple event notification rules apply to the same object, the values of this parameter in these rules must be different. For example, if you configure Events as **CopyObject** when you create an event notification rule for objects whose names contain the examplefolder prefix, you cannot select **CopyObject** as the value for Events when you create another event notification rule for objects whose names contain the same prefix.

For more information about the events, see [Events](/intl.en-US/Developer Guide/Event notification.md). |
    |**Resource Description**|Specify the objects to which the event notification rule applies.    -   **Full Name**: Enter the complete path of the object to which the event notification rule applies. Example: examplefolder/myphoto.jpg.
    -   **Prefix and Suffix**: Enter the prefix and suffix of the objects to which the event notification rule applies. The following examples show how to configure the prefix and suffix:
        -   To create a rule that applies to all objects in the bucket, leave Prefix and Suffix empty.
        -   To create a rule that applies to all objects in the examplefolder folder in the root folder of the bucket, set Prefix to **examplefolder/** and leave Suffix empty.
        -   To create a rule that applies to all JPG objects in the bucket, leave Prefix empty and set Suffix to **.jpg**.
        -   To create a rule that applies to all MP3 objects in the examplefolder folder in the root folder of the bucket, set Prefix to **examplefolder/** and Suffix to **.mp3**.
To create a Resource Description, click **Add**. You can create up to five **Resource Description** entries. |
    |**Endpoint**|Configure the endpoint to which notifications are sent. Valid values: **HTTP** and **Queue**.    -   **HTTP**: Enter the address of the HTTP endpoint to which notifications are sent. Example: `http://198.51.100.1:8080`. For more information about how to enable an HTTP endpoint, see [Manage topics]() and [HttpEndpoint]().
    -   **Queue**: Enter the name of an MNS queue. For more information about how to create a queue, see [Create a queue]().
To create an endpoint, click **Add**. You can create up to five endpoints. |

6.  Click **OK**.

    After you configure the OSS event notification settings, a topic is created on the Topics page in the [MNS](https://mns.console.aliyun.com/) console.


## Step 3: Create an MNS token

1.  Log on to the [MNS console](https://mns.console.aliyun.com/).

2.  In the left-side navigation pane, click **Topics**.

3.  In the top navigation bar of the MNS console, select the region where your cluster is deployed.

4.  On the Topics page, click the topic that you want to manage.

5.  On the Topics page, click **Get Endpoint** in the upper-right corner of the page.

6.  In the **Endpoint** section of the Topic Details page, copy the public endpoint.

7.  Obtain an AccessKey ID and AccessKey secret of the current account. For more information, see [How can I obtain an AccessKey ID and AccessKey secret?](/intl.en-US/FAQ/General FAQ/How can I obtain an AccessKey ID and AccessKey secret?.md).

8.  Run the following command to encode the public endpoint, AccessKey ID, and AccessKey secret by using Base64. Then, a token is generated.

    ```
    echo '{ "url":"https://xxxx.mns.cn-shanghai.aliyuncs.com/", "accessKeyId":"xxx","accessKeySecret":"xx" }' | base64
    ```

9.  Create a Secret to store and manage the token.

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

        Replace the value of `mns` with the token that is generated in Step [8](#step_k36_cbs_rqi).

    2.  Run the following command to create a Secret:

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

    2.  Run the following command to create a service account:

        ```
        kubectl apply -f mnsoss-sa.yaml
        ```

2.  Run the following command to create a service broker:

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

2.  Run the following command to create an MnsOss event source:

    ```
    kubectl  apply -f mnsoss-source.yaml
    ```


## Step 6: Create a Knative Service

Create a Knative Service to verify whether the created MnsOss event source works as normal. A Knative Service named event-display is created in this example.

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

2.  Run the following command to create a Knative Service:

    ```
    kubectl apply -f service.yaml
    ```


## Step 7: Create a trigger

Create a trigger to subscribe to OSS events.

1.  Create a file named trigger.yaml.

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

2.  Run the following command to create a trigger:

    ```
    kubectl apply -f trigger.yaml
    ```


## Check results

When you upload files to the OSS bucket, notifications are sent to pods.

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
            "userIdentity": {"principalId": "104120891425****"}}]} "137138904F2E18D307D04EB38EA44CDA",
                    "key": "timg.jpg",
                    "size": 12990},
                "ossSchemaVersion": "1.0",
                "ruleId": "demo-i****"},
            "region": "cn-beijing",
            "requestParameters": {"sourceIPAddress": "42.120.7*.***"},
            "responseElements": {"requestId": "5D08884070BC12B192C6****"},
            "userIdentity": {"principalId": "104120891425****"}}]}
```

