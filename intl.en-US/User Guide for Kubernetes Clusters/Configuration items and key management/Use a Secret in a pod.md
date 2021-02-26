# Use a Secret in a pod

This topic describes how to use a Secret in a pod.

-   The Secret and pod are in the same cluster of Container Service for Kubernetes \(ACK\) and belong to the same namespace.
-   You are connected to a master node of the cluster. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md).

You can use a Secret in a pod in the following scenarios:

-   Mount a Secret as a volume to a pod.
-   Use a Secret to configure environment variables for a pod.

For more information, see [Secrets](https://kubernetes.io/zh/docs/concepts/configuration/secret/).

## Create a Secret

In this example, a Secret named secret-test is created.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster, or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

5.  In the upper-right corner of the **Deployments** tab, click **Create from Template**.

6.  Select a cluster and a namespace, select a sample template or enter a custom template, and then click **Create**.

    The following YAML template is an example:

    ```
    apiVersion: v1
    kind: Secret
    metadata:
      name: secret-test
    type: Opaque
    data:
      username: admin
      password: 12345  # The value must be encoded in Base64.
    ```


For more information about how to create a Secret in the ACK console, see [t16704.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Configuration items and key management/Create a Secret.md).

## Mount a Secret as a volume to a pod

You can mount a Secret as a volume to a pod by using the following methods:

You can use a YAML file to mount a Secret as a volume to a pod. A mounted Secret can be used as a file in a pod. In this example, the username and password information of secret-test is stored in a file under the /srt directory.

1.  Create a file named example0.yaml and paste the following content into the file:

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

2.  Run the following command to execute the file:

    ```
    kubectl apply -f  example0.yaml
    ```

    **Note:** Replace example0.yaml with the name of the YAML file that is used.


You can also mount a Secret as a volume to a pod in the Container Service for Kubernetes \(ACK\) console.

1.  In the left-side navigation pane of the ACK console, click **Clusters**.

2.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster, or click **Details** in the **Actions** column. The details page of the cluster appears.

3.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

4.  In the upper-right corner of the **Deployments** tab, click **Create from Image**.

    For more information, see [Deploy a stateless application from an image](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Deploy a stateless application from an image.md).

5.  On the **Basic Information** wizard page, set the parameters based on your requirements and click **Next**.

6.  On the **Container** wizard page, click **Add Local Storage** in the **Volume** section. In this example, select Secret from the PV Type drop-down list, select the Secret that is created in [Create a Secret](#section_yxv_25m_zs0) from the Mount Source drop-down list, and specify a container path to mount the volume.

    The following figure shows the configuration of the volume.

    ![Configure the volume](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4645359951/p49492.png)

7.  On the **Advanced** wizard page, set the parameters based on your requirements and click **Create**.


## Use a Secret to configure environment variables for a pod.

You can use a Secret to configure environment variables for a pod in the following ways:

You can use a YAML file to configure environment variables for a pod. In this example, the username and password information of secret-test is configured as environment variables of a pod.

1.  Create a YAML file named example1.yaml and paste the following content into the file.

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

2.  Run the following command to configure the environment variables:

    ```
    kubectl apply -f  example1.yaml
    ```

    **Note:** Replace example1.yaml with the name of the YAML file that is used.


You can also use a Secret to configure environment variables for a pod in the ACK console.

1.  In the left-side navigation pane of the ACK console, click **Clusters**.

2.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster, or click **Details** in the **Actions** column. The details page of the cluster appears.

3.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

4.  In the upper-right corner of the **Deployments** tab, click **Create from Image**.

    For more information, see [Deploy a stateless application from an image](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Deploy a stateless application from an image.md).

5.  On the **Basic Information** wizard page, set the parameters based on your requirements and click **Next**.

6.  On the **Container** wizard page, click ![Environments](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7766105061/p49493.png) in the **Environments** section. In this example, select **Secret** from the Type drop-down list and select the Secret that is created in [Create a Secret](#section_yxv_25m_zs0) from the Value/ValueFrom drop-down list. After you select the Secret, you must specify the key of the key-value pair that you want to reference and also specify a name for the environment variable.

    The following figure shows the configurations.

    ![Variable name](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4645359951/p49494.png)

7.  On the **Advanced** wizard page, set the parameters based on your requirements and click **Create**.


