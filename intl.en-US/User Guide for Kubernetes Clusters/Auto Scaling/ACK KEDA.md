# ACK KEDA

Container Service for Kubernetes \(ACK\) supports Kubernetes-based Event Driven Autoscaling \(KEDA\). You can install KEDA in the ACK console to enable event-driven scaling for clusters. This topic describes what KEDA is, how KEDA works, and how to use KEDA.

## Overview

For Kubernetes, Horizontal Pod Autoscaler \(HPA\) is the most commonly used solution to enable auto scaling of pods. HPA determines scaling strategies based on the difference between the resource usage and predefined threshold. HPA is an easy-to-use tool that supports a wide array of resource metrics. However, it does not support real-time scaling. For example, it cannot scale resources when certain events are detected. To address this issue, you can install KEDA in ACK. ACK KEDA supports event-driven scaling that can be used in various event-driven scenarios, such as video and audio transcoding on demands, event-driven jobs, and stream processing.

## How KEDA works

ACK KEDA is an enhanced version of the open source KEDA. It supports event-driven scaling. The following figure shows how ACK KEDA works.

![How KEDA works](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1822342161/p208275.png)

ACK KEDA periodically consumes data from event sources. The increase of pending messages immediately triggers ACK KEDA to scale a batch of jobs. After the next period starts, the next batch of jobs is scaled asynchronously. ACK KEDA supports the following features:

-   Supports a wide array of event sources

    ACK KEDA supports data sources such as Kafka, MySQL, RabbitMQ, and MongoDB. For more information, see [RabbitMQ Queue](https://keda.sh/docs/2.0/scalers/rabbitmq-queue/).

-   Controls the concurrency of jobs

    When a large number of jobs are submitted, the stability of the underlying control system is affected. It must control resources, quotas, and API requests. ACK KEDA can control the concurrency of jobs in one or more batches to ensure system stability.

-   Clears metadata after jobs are completed

    A large amount of metadata is retained after a great number of jobs are completed. The increase of metadata degrades the stability of the API server. The performance and stability of the cluster are also degraded and may even adversely affect other services. ACK KEDA can automatically clear metadata after jobs are completed.


## Step 1: Deploy ACK KEDA

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Marketplace** \> **App Catalog**.

3.  On the App Catalog page, enter ack-keda in the search box in the upper-right corner to search for ACK KEDA. Then, click the ack-keda card.

4.  On the App Catalog - ack-keda page, select the cluster where you want to deploy ACK KEDA in the **Deploy** section, and click **Create**.

    In the left-side navigation pane, click **Clusters**. On the Clusters page, find the cluster and click **Details** in the **Actions** column. In the left-side navigation pane of the cluster details page, choose **Workloads** \> **Deployments**. In the upper-left corner of the **Deployments** page, select the kube-system **Namespace**. ACK KEDA is displayed.


## Step 2: Add an event source

In this example, MongoDB is added as an event source. ACK KEDA periodically checks the data entries that meet the `{"region":"eu-1","state":"running","plan":"planA"}` condition in MongoDB. ACK KEDA scales out jobs in the ACK cluster to consume the queried data. The `state` of data entries that have been processed by jobs is marked as done.

1.  Deploy MongoDB.

    If you have already deployed MongoDB, skip this step.

    **Note:** Do not use this database in the production environment.

    1.  Create the following mongo\_sta\_nodeport.yaml file:

        ```
        apiVersion: apps/v1
        kind: Deployment
        metadata:
          name: mongodb
        spec:
          replicas: 1
          serviceName: mongodb
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
          name: mongo-test
        spec:
          type: NodePort
          ports:
          - name: mongodb
            port: 27017
            nodePort: 30075
            targetPort: 27017
            protocol: TCP
          selector:
            name: mongodb
        ```

    2.  Deploy MongoDB.

        ```
         kubectl apply -f mongo_sta_nodeport.yaml
        ```

2.  Log on to MongoDB and create a user account.

    1.  Log on to MongoDB.

        ```
        use sample
        ```

    2.  Create a user account.

        ```
        db.createUser({ user:"test_user",pwd:"test_password",roles:[{ role:"readWrite", db: "sample"}]})
        ```

    3.  Create a collection.

        ```
        db.createCollection("demo")
        ```

3.  Deploy a ScaledJob definition and a TriggerAuthentication resource.

    ACK KEDA uses TriggerAuthentication for authentication when you want ACK KEDA to check for a specified type of event. MongoDB is added as an event source in this example. In this case, the `secretTargetRef` field in TriggerAuthentication identifies the connection method in the specified secret and sends the connection method to ACK KEDA. Then, ACK KEDA authenticates logon requests that are sent to MongDB.

    1.  Create the following authentication.yaml file:

        ```
        apiVersion: keda.sh/v1alpha1
        kind: TriggerAuthentication
        metadata:
          name: mongo-trigger
          namespace: kube-system
        spec:
          secretTargetRef:
            - parameter: connectionString
              name: mongo-secret
              key: connect
        ---
        apiVersion: v1
        kind: Secret
        metadata:
          name: mongo-secret
          namespace: kube-system
        type: Opaque
        data:
          connect: bW9uZ29kYjovL3Rlc3RfdXNlcjp0ZXN0X3Bhc3N3b3JkQDguNDYuMTAuMjE2OjI3MDE3L3NhbXBsZQ==
        ```

    2.  Deploy TriggerAuthentication and a Secret.

        ```
         kubectl apply -f authentication.yaml
        ```

    3.  Create the following job.yaml file:

        ```
        apiVersion: keda.sh/v1alpha1
        kind: ScaledJob
        metadata:
          name: mongo-job
          namespace: kube-system
        spec:
          jobTargetRef:
            template:
              spec:
                containers:
                  - name: mongodb-update
                    image: 1314520999/mongodb-update:latest
                    args:
                    - --connectStr=mongodb://test_user:test_password@8.46.10.216:27017/sample
                    - --dataBase=sample
                    - --collection=demo
                    imagePullPolicy: IfNotPresent
                restartPolicy: Never
            backoffLimit: 1
          pollingInterval: 10
          maxReplicaCount: 30
          successfulJobsHistoryLimit: 0
          failedJobsHistoryLimit: 10
          triggers:
            - type: mongo
              metadata:
                dbName: "sample"
                collection: "demo"
                query: '{"region":"eu-1","state":"running","plan":"planA"}'
                queryValue: "1"
              authenticationRef:
                name: mongo-trigger
        ```

        `query`: Set a condition. When ACK KEDA detects data entries that meet the specified condition, jobs are started.

    4.  Deploy ScaledJob.

        ```
         kubectl apply -f job.yaml
        ```


## Step 3: Check whether ACK KEDA works as expected

1.  Use the mongo shell to insert the following data entry:

    ```
    db.demo.insert({"region":"eu-1","state":"running","plan":"planA","goods":"orange"})
    ```

2.  Run the following command to query jobs:

    Based on the condition specified in [Step 2: Add an event source](#section_14x_42n_nbu), ACK KEDA detects the data entry inserted in Step [1](#step_glc_krj_m6q). Therefore, a job is started to process the event.

    ```
    watch -n 1 kubectl get job -n kube-system
    ```

    ![Jobs](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1822342161/p208358.png)

    The preceding figure shows that a job is completed. ACK can scale pods upon MongoDB events.


