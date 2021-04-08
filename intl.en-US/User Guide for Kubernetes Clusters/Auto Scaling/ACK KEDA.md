# ACK KEDA

Container Service for Kubernetes \(ACK\) supports Kubernetes-based Event Driven Autoscaling \(KEDA\). You can install KEDA in the ACK console to implement event-driven scaling for clusters. This topic describes what KEDA is, how KEDA works, and how to use KEDA.

## Overview

For Kubernetes, Horizontal Pod Autoscaler \(HPA\) is the most commonly used solution to automatically scale pods. HPA determines scaling strategies based on the differences between the resource usage and predefined thresholds. HPA is an easy-to-use tool that supports a wide array of resource metrics. However, it does not support real-time scaling. For example, HPA cannot scale resources when certain events are detected. To address this issue, you can install KEDA in the ACK console. ACK KEDA supports event-driven scaling that can be used in various event-driven scenarios, such as video and audio transcoding on demands, event-driven jobs, and stream processing.

## How KEDA works

ACK KEDA is an enhanced version of the open source KEDA. It supports event-driven scaling. The following figure shows how ACK KEDA works.

![How KEDA works](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1822342161/p208275.png)

ACK KEDA periodically consumes data from event sources. When pending messages increase, ACK KEDA is triggered to scale a batch of jobs. After the next period starts, the next batch of jobs is asynchronously scaled. ACK KEDA supports the following features:

-   Supports a wide array of event sources

    ACK KEDA supports data sources such as Kafka, MySQL, PostgreSQL, RabbitMQ, and MongoDB. For more information, see [RabbitMQ Queue](https://keda.sh/docs/2.0/scalers/rabbitmq-queue/).

-   Controls the concurrency of jobs

    When a large number of jobs are submitted, the stability of the underlying control system is adversely affected because the system must holistically control resources, quotas, and API requests. ACK KEDA can control the concurrency of jobs in one or more batches to ensure system stability.

-   Clears metadata after jobs are completed

    A large amount of metadata is retained after a great number of jobs are completed. The increase of metadata degrades the stability of the API server. The performance and stability of the cluster are also degraded and other services may be adversely affected. ACK KEDA can automatically clear metadata after jobs are completed.


## Case study

A simple transcoding job is used in the following case. When a new job is received, a piece of data similar to the following example is inserted to MongoDB: `{"type":"mp4","state":"waiting","createTimeStamp":"1610332940","fileName":"World and peace","endTimeStamp":"","uuid":"1fae72ff-3239-42f5-af97-04711d8007e8"}`. ACK KEDA queries the database and detects data entries that meet the `"state":"waiting"` condition. Then, ACK KEDA creates pods to process the data entries. One pod is created for one data entry. After the transcoding is completed, the value of the `state` field is changed from `waiting` to `finished`. After the job is completed, the metadata is automatically cleared to reduce the load on the API Server. This allows you to manage jobs in a convenient manner.

## Step 1: Deploy ACK KEDA

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Marketplace** \> **App Catalog**.

3.  On the App Catalog page, enter ack-keda in the search box in the upper-right corner to search for ACK KEDA. Then, click the ack-keda card.

4.  On the App Catalog - ack-keda page, select the cluster where you want to deploy ACK KEDA in the **Deploy** section, and click **Create**.

    In the left-side navigation pane, click **Clusters**. On the Clusters page, select the cluster where ACK KEDA is deployed and click the cluster name or click **Details** in the **Actions** column. In the left-side navigation pane of the cluster details page, choose **Workloads** \> **Deployments**. In the upper-left corner of the **Deployments** page, set **Namespace** to kube-system and verify that ACK KEDA is displayed.


## Step 2: Add MongoDB as an event source

1.  Deploy MongoDB.

    If you have already deployed MongoDB, skip this step.

    **Note:** The database is used only for testing purposes. Do not use the database in production environments.

    1.  Create a YAML file named mongoDB.yaml and add the following code to the file:

        ```
        apiVersion: apps/v1
        kind: Deployment
        metadata:
          name: mongodb
        spec:
          replicas: 1
          selector:
            matchLabels:
              name: mongodb
          template:
            metadata:
              labels:
                name: mongodb
            spec:
              containers:
              - name: mongodb
                image: mongo:4.2.1
                imagePullPolicy: IfNotPresent
                ports:
                - containerPort: 27017
                  name: mongodb
                  protocol: TCP
        ---
        kind: Service
        apiVersion: v1
        metadata:
          name: mongodb-svc
        spec:
          type: ClusterIP
          ports:
          - name: mongodb
            port: 27017
            targetPort: 27017
            protocol: TCP
          selector:
            name: mongodb
        ```

    2.  Deploy MongoDB to the mongodb namespace of the cluster.

        ```
         kubectl apply -f mongoDB.yaml -n mongodb
        ```

2.  Log on to MongoDB and create a user account.

    1.  Add a user account.

        ```
        //Create a user account
        kubectl exec -n mongodb mongodb-xxxxx -- mongo --eval 'db.createUser({ user:"test_user",pwd:"test_password",roles:[{ role:"readWrite", db: "test"}]})'
        ```

    2.  Log on to MongoDB.

        ```
        //Logon authentication.
        kubectl exec -n mongodb mongodb-xxxxx -- mongo --eval  'db.auth("test_user","test_password")'
        ```

    3.  Create a collection.

        ```
        // Create a collection
        kubectl exec -n mongodb mongodb-xxxxx -- mongo --eval  'db.createCollection("test_collection")'
        ```

3.  Deploy TriggerAuthentication and a Secret.

    ACK KEDA uses TriggerAuthentication to authenticate logon requests to event sources. In this case, MongoDB is added as an event source. ACK KEDA uses the `secretTargetRef` field in TriggerAuthentication to retrieve the connection information from the specified Secret and then uses the connection information to authenticate requests to MongDB.

    1.  Create a YAML file named auth.yaml and add the following code to the file:

        ```
        apiVersion: keda.sh/v1alpha1
        kind: TriggerAuthentication
        metadata:
          name: mongodb-trigger
        spec:
          secretTargetRef:
            - parameter: connectionString
              name: mongodb-secret
              key: connect
        ---
        apiVersion: v1
        kind: Secret
        metadata:
          name: mongodb-secret
        type: Opaque
        data:
          connect: bW9uZ29kYjovL3Rlc3RfdXNlcjp0ZXN0X3Bhc3N3b3JkQG1vbmdvZGItc3ZjLm1vbmdvZGIuc3ZjLmNsdXN0ZXIubG9jYWw6MjcwMTcvdGVzdA==
        ```

    2.  Deploy TriggerAuthentication to the mongodb-test namespace of the cluster.

        ```
        kubectl apply -f auth.yaml -n mongodb-test
        ```

4.  Deploy ScaledJob.

    ScaledJob is used to define the job template and specify the database to be queried and the query expression. In the following example, a job that queries the test\_collection collection in the test database and transcodes data entries that meet the `{"type":"mp4","state":"waiting"}` condition is created.

    1.  Create a YAML file named job.yaml and add the following code to the file:

        ```
        apiVersion: keda.sh/v1alpha1
        kind: ScaledJob
        metadata:
          name: mongodb-job
        spec:
          jobTargetRef:
            // Job template.
            template:
              spec:
                containers:
                  - name: mongo-update
                    image: registry.cn-hangzhou.aliyuncs.com/carsnow/mongo-update:v6
                    args:
                      - --connectStr=mongodb://test_user:test_password@mongodb-svc.mongodb.svc.cluster.local:27017/test
                      - --dataBase=test
                      - --collection=test_collection
                    imagePullPolicy: IfNotPresent
                restartPolicy: Never
            backoffLimit: 1
          pollingInterval: 15
          maxReplicaCount: 5
          successfulJobsHistoryLimit: 0
          failedJobsHistoryLimit: 10
          triggers:
            - type: mongodb
              metadata:
                dbName: test                               //The database to be queried.
                collection: test_collection                //The collection to be queried.
                query: '{"type":"mp4","state":"waiting"}'  //Create a job to process each data entry whose type is set to mp4 and state is set to waiting.
                queryValue: "1"
              authenticationRef:
                name: mongodb-trigger
        ```

        `query`: Set a condition. When ACK KEDA detects data entries that meet the specified condition, jobs are started.

    2.  Deploy ScaledJob to the mongodb-test namespace of the cluster.

        ```
        kubectl apply -f scaledJob.yaml -n mongodb-test
        ```

5.  Insert five data entries to MongoDB.

    ```
    //Insert five data entries to MongoDB.
    kubectl exec -n mongodb mongodb-xxxxx -- mongo --eval 'db.test_collection.insert([
      {"type":"mp4","state":"waiting","createTimeStamp":"1610352740","fileName":"My Love","endTimeStamp":"","uuid":"1gae72ff-3239-42f5-af97-04711d8007e8"},
      {"type":"mp4","state":"waiting","createTimeStamp":"1610350740","fileName":"Harker","endTimeStamp":"","uuid":"1gae72ff-3239-42f5-af97-04711d8007e8"},
      {"type":"mp4","state":"waiting","createTimeStamp":"1610152940","fileName":"The World","endTimeStamp":"","uuid":"1gae72ff-3239-42f5-af97-04711d87767e8"},
      {"type":"mp4","state":"waiting","createTimeStamp":"1610390740","fileName":"Mother","endTimeStamp":"","uuid":"1gae72ff-3239-42f5-af97-04799d8007e8"},
      {"type":"mp4","state":"waiting","createTimeStamp":"1610344740","fileName":"Jagger","endTimeStamp":"","uuid":"1gae72ff-3239-42f5-af97-04711d80099e8"},
      ])'
    ```


## Step 3: Check whether ACK KEDA works as expected

Run the following command to query jobs:

```
//watch job
watch -n 1 kubectl get job -n mongodb-test
```

![Job](../images/p230844.png)

Verify that five jobs are created. Log on to MongoDB and check the inserted data. Verify that the state of each data entry that you inserted is changed from `waiting` to `finished`.

![Database](../images/p230845.png)

