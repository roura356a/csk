---
keyword: [Secrets, mount a Secret as a volume to a pod, expose a Secret as an environment variable for a pod]
---

# Use a Secret in a pod

We recommend that you use Secrets to store sensitive information in Kubernetes clusters. The information includes passwords and certificates. This topic describes how to create a Secret in the Container Service for Kubernetes \(ACK\) console. This topic also describes how to mount a Secret as a volume to a pod and expose a Secret as an environment variable for a pod. You can perform the operations by using the console or a CLI.

-   The Secret and pod are in the same cluster and belong to the same namespace.
-   You are connected to a master node of the cluster. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md).

You can use a Secret in a pod in the following scenarios:

-   Mount a Secret as a volume to a pod.
-   Expose a Secret as an environment variable for a pod.

For more information about Secrets, see [Secrets](https://kubernetes.io/zh/docs/concepts/configuration/secret/).

## Create a Secret

The following example shows how to create a Secret named secret-test.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

5.  In the upper-right corner of the **Deployments** page, click **Create from YAML**.

6.  Select a cluster and a namespace, select a sample template or enter a custom template, and then click **Create**.

    The following YAML template provides an example on how to create the Secret named secret-test:

    ```
    apiVersion: v1
    kind: Secret
    metadata:
      name: secret-test
    type: Opaque
    data:
      username: admin
      password: 12345  #The value must be encoded in Base64. 
    ```


For more information about how to create a Secret in the ACK console, see [t16704.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Configuration items and key/Manage Secrets.md).

## Mount a Secret as a volume to a pod

You can mount a Secret as a volume to a pod by using the following methods:

**Mount a Secret as a volume to a pod by using a CLI**

A mounted Secret can be used as a file in a pod. In this example, the secret-test Secret that contains the username and password information is stored as a file under the /srt directory.

1.  Create an example0.yaml file and copy the following content into the file:

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: pod0
    spec:
      containers:
      - name: redis
        image: redis
        volumeMounts:
        - name: srt
          mountPath: "/srt "
          readOnly: true
      volumes:
      - name: srt
        secret:
          secretName: secret-test
    ```

2.  Run the following command to create a pod to which the secret-test Secret is mounted:

    ```
    kubectl apply -f  example0.yaml
    ```

    **Note:** Replace example0.yaml with the name of the YAML file that is used.


**Mount a Secret as a volume to a pod in the ACK console**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

5.  In the upper-right corner of the **Deployments** page, click **Create from Image**.

    For more information, see [Use a Deployment to create a stateless application](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Use a Deployment to create a stateless application.md).

6.  On the **Basic Information** wizard page, set the parameters and click **Next**.

7.  On the **Container** wizard page, click **Add Local Storage** in the **Volume** section. Select Secret from the PV Type drop-down list, select the Secret that is created in [Create a Secret](#section_yxv_25m_zs0) from the Mount Source drop-down list, and specify a container path in the Container Path column. Click **Next**.

    The following figure shows an example on how to configure the volume.

    ![Configure the volume](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4645359951/p49492.png)

8.  On the **Advanced** wizard page, set the parameters and click **Create**.


## Expose a Secret as an environment variable for a pod

You can expose a Secret as an environment variable for a pod by using the following methods:

**Expose a Secret as an environment variable for a pod by using a CLI**

In this example, the username and password stored in the secret-test Secret are referenced in an environment variable of a pod.

1.  Create an example1.yaml file and copy the following content into the file:

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: pod1
    spec:
      containers:
      - name: redis
        image: redis
        env:
          - name: USERNAME
            valueFrom:
              secretKeyRef:
                name: secret-test
                key: username
          - name: PASSWORD
            valueFrom:
              secretKeyRef:
                name: secret-test
                key: password
    ```

2.  Run the following command to configure an environment variable:

    ```
    kubectl apply -f  example1.yaml
    ```

    **Note:** Replace example1.yaml with the name of the YAML file that is used.


**Expose a Secret as an environment variable for a pod in the ACK console**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

5.  In the upper-right corner of the **Deployments** page, click **Create from Image**.

    For more information, see [Use a Deployment to create a stateless application](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Use a Deployment to create a stateless application.md).

6.  On the **Basic Information** wizard page, set the parameters and click **Next**.

7.  On the **Container** wizard page, click ![Environment variable](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7766105061/p49493.png) in the **Environments** section. In this example, select **Secret** from the Type drop-down list and select the Secret that is created in [Create a Secret](#section_yxv_25m_zs0) from the Value/ValueFrom drop-down list. After you select the Secret, you must specify the key of the key-value pair that you want to reference and specify a name for the environment variable.

    The following figure shows an example on how to configure the environment variable.

    ![Variable name](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4645359951/p49494.png)

8.  On the **Advanced** wizard page, set the parameters and click **Create**.


