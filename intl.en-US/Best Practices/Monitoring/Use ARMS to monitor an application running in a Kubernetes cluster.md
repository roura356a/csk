# Use ARMS to monitor an application running in a Kubernetes cluster {#concept_w3d_nn1_kgb .concept}

This topic describes how to use Application Real-Time Monitoring Service \(ARMS\) to monitor an application running in Alibaba Cloud Container Service for Kubernetes.

## Overview about ARMS {#section_zrv_441_kgb .section}

ARMS is a Java application performance management \(APM\) monitoring product developed

by Alibaba Cloud. If you use ARMS to monitor a Java application, you only need to mount a probe in the application startup script without modifying any code. By comprehensively monitoring the application, the probe helps you quickly locate faulty and slow interfaces, reproduce parameter calling, detect memory leaks, and discover system bottlenecks, more efficiently diagnosing problems online. For more information, see [ARMS](https://help.aliyun.com/product/34364.html).

**ARMS monitors an application as follows:**

-   Automatically discovers the application topology

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/92331/155007443237130_en-US.png)

-   Automatically discovers and monitors interfaces
-   Captures exception transactions and slow transactions, and provides SQL analysis
-   Provides a Java exception report
-   Provides trace-based transaction snapshot queries

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/92331/155007443237131_en-US.png)

-   Provides multidimensional ad-hoc troubleshooting, including multidimensional trace searches and exception trace searches
-   Integrates PaaS platforms

## Prerequisites {#section_bqm_3wp_kgb .section}

-   You have created a Kubernetes cluster. For more information, see [Create a Kubernetes cluster](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).
-   You have activated ARMS, see [Activate ARMS](https://help.aliyun.com/document_detail/65257.html).

## Install ARMS components {#section_f32_vwp_kgb .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane, choose **Store** \> **App Catalog**. On the App Catalog page, select **ack-arms-pilot**.
3.  On the App Catalog - ack-arms-pilot page, click **DEPLOY**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/92331/155007443237035_en-US.png)


In the left-side navigation pane, choose **Application** \> **Deployment**, and then select the target cluster and namespace. An application named ack-arms-pilot-default-ack-arms-pilot is displayed on the page.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/92331/155007443237071_en-US.png)

## Grant permission to use ARMS {#section_q5g_fgq_kgb .section}

1.  In the left-side navigation pane of Container Service console, click **Clusters**.

    **Note:** To perform the operations required in this section, you must use the primary account to log on to the Container Service console.

2.  Click the target cluster name to view the cluster details.
3.  In the Cluster Resources area, click **Worker RAM Role**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/92331/155007443237044_en-US.png)

    **Note:** 

    This topic uses the latest version of the RAM console.

    If you use an earlier version of the RAM console, you can modify the target policy document by using one of the following methods:

    **Method 1**

    1.  In the left-side navigation pane, click **Roles**, and then enter the **Worker RAM Role** name in the **Role Name** box. Click the target **Role Name**.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/92331/155007443237085_en-US.png)

    2.  In the **Basic Information** area, click **Edit Basic Information** in the upper-right corner.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/92331/155007443237088_en-US.png)

    **Method 2**

    In the lower-right corner of the RAM dashboard page, click **New Version** to switch to the latest version of the RAM console. In the Container Service console, click **Worker RAM Role** to log on to the RAM console.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/92331/155007443237079_en-US.png)

4.  On the RAM Roles page, click the policy name on the Permissions tab page.
5.  On the Policies page, click **Modify Policy Document** on the Policy Document tab page.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/92331/155007443237053_en-US.png)

6.  In the Policy Document area, add the following fields and then click **OK**.

    ```
    {
        "Action": "arms:*",
        "Resource": "*",
        "Effect": "Allow"
    }
    ```

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/92331/155007443337054_en-US.png)


## Deploy ARMS monitoring for an application {#section_nmg_xlq_kgb .section}

In a YAML file used to create the target deployment, add the following `annotations` to deploy ARMS application monitoring.

```
annotations:
  armsPilotAutoEnable: "on"
  armsPilotCreateAppName: "<your-deployment-name>"
```

**Note:** 

-   In the YAML file, you must add `annotations` under `metadata` of `template` in the `spec` field.
-   The value of `armsPilotCreateAppName` is the name of the application monitored by ARMS.

1.  In the left-side navigation pane of the Container Service console, choose **Application** \> **Deployment**.
2.  In the upper-right corner, click **Create by Template**.
3.  Select the target cluster and namespace to create a deployment.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/92331/155007443338486_en-US.png)

    ```
    apiVersion: apps/v1beta1 # for versions before 1.8.0 use apps/v1beta1
    kind: Deployment
    metadata:
      name: arms-springboot-demo
      labels:
        app: arms-springboot-demo
    spec:
      replicas: 2
      selector:
        matchLabels:
          app: arms-springboot-demo
      template:
        metadata:
          annotations:
            armsPilotAutoEnable: "on"
            armsPilotCreateAppName: "arms-k8s-demo"
          labels:
            app: arms-springboot-demo
        spec:
          containers:
            - resources:
                limits:
                  cpu: 0.5
              image: registry.cn-hangzhou.aliyuncs.com/arms-docker-repo/arms-springboot-demo:v0.1
              imagePullPolicy: Always
              name: arms-springboot-demo
              env:
                - name: MYSQL_SERVICE_HOST
                  value: "arms-demo-mysql"
                - name: MYSQL_SERVICE_PORT
                  value: "3306"
    ---
    apiVersion: apps/v1beta1 # for versions before 1.8.0 use apps/v1beta1
    kind: Deployment
    metadata:
      name: arms-demo-mysql
      labels:
        app: mysql
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: mysql
      template:
        metadata:
          labels:
            app: mysql
        spec:
          containers:
            - resources:
                limits:
                  cpu: 0.5
              image: registry.cn-hangzhou.aliyuncs.com/arms-docker-repo/arms-demo-mysql:v0.1
              name: mysql
              ports:
                - containerPort: 3306
                  name: mysql
    ---
    apiVersion: v1
    kind: Service
    metadata:
      labels:
        name: mysql
      name: arms-demo-mysql
    spec:
      ports:
        # the port that this service should serve on
        - name: arms-mysql-svc
          port: 3306
          targetPort: 3306
      # label keys and values that must match in order to receive traffic for this service
      selector:
        app: mysql
    ---
    ```


**Verify the results**

1.  In the left-side navigation pane of the Container Service console, choose **Application** \> **Deployment**, and then select the target cluster and namespace to view the created deployment.
2.  In the **Action** column of the target deployment, click **ARMS console** to log on to the ARMS console to view application details such as **Application Overview**, **Interface Invocation**, and other information.

    **Note:** If **ARMS console** is not displayed in the **Action** column, check whether you have granted Container Service permission to use ARMS. For more information, see [Grant permission to use ARMS](#section_q5g_fgq_kgb).

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/92331/155007443337099_en-US.png)


