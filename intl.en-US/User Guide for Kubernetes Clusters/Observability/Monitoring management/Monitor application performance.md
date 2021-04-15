# Monitor application performance

You can use Application Real-Time Monitoring Service \(ARMS\) to monitor Java and PHP applications that are deployed in a Container Service for Kubernetes \(ACK\) cluster. ARMS can automatically discover application topology, generate 3D topologies, discover and monitor API endpoints, and detect abnormal and slow transactions. ARMS makes it easier for you to diagnose and troubleshoot application issues.

-   [Create a cluster]()
-   [Activate and upgrade ARMS](/intl.en-US/Quick Start/Activate and upgrade ARMS.md)

    **Note:** The PHP application monitoring feature is in public preview. You are not charged for this feature.


[ARMS](/intl.en-US/Product Overview/What is ARMS?.md) is an application performance management \(APM\) service that is provided by Alibaba Cloud. After you install the ARMS application monitoring agent, you can thoroughly monitor your Java applications in an ACK cluster without the need to modify the application code. ARMS allows you to quickly locate abnormal and slow transactions, reproduce the parameters of API calls, detect memory leaks, and discover system bottlenecks. This significantly improves the efficiency to diagnose and troubleshoot application issues. For more information, see [Overview](/intl.en-US/Application Monitoring/Overview.md).

## Install the ARMS application monitoring agent

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Marketplace** \> **App Catalog**. On the **Alibaba Cloud Apps** tab, find and click **ack-arms-pilot**.

3.  On the App Catalog - ack-arms-pilot page, select the created ACK cluster in the Deploy section and click **Create**.


## Authorize ACK to access ARMS

Perform the following steps to grant ACK the permissions to access ARMS.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  On the **Cluster Resources** tab of the details page for the cluster, click the name of the worker Resource Access Management \(RAM\) role.

5.  You are redirected to the **RAM Roles** page in the **RAM** console. On the RAM Roles page, click the policy name on the **Permissions** tab.

6.  On the **Policy Document** tab, click **Modify Policy Document**. On the Modify Policy Document panel, copy the following content to the Policy Document field and click **OK**.

    ```
    {
        "Action": "arms:*",
        "Resource": "*",  
        "Effect": "Allow"
        }   
    ```


## Enable ARMS application monitoring for Java applications

The following steps describe how to enable ARMS application monitoring for a newly created or existing application.

To enable ARMS application monitoring when you create an application, perform the following steps.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

5.  In the upper-right corner of the Deployments tab, click **Create from Template**.

6.  On the Create from Template page, select a Deployment template from the **Sample Template** drop-down list and copy the following `annotations` to the spec \> template \> metadata section in the **template**.

    **Note:** Replace <your-deployment-name\> with your application name.

    ```
    annotations:
      armsPilotAutoEnable: "on"
      armsPilotCreateAppName: "<your-deployment-name>"                                
    ```

    ![YAML Example](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5354934061/p53707.png)

    The following YAML template shows how to create a Deployment application and enable ARMS application monitoring for the application.


## Enable ARMS application monitoring for PHP applications

The following steps describe how to enable ARMS application monitoring for a newly created or existing application.

To enable ARMS application monitoring when you create an application, perform the following steps.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

5.  In the upper-right corner of the Deployments tab, click **Create from Template**.

6.  On the Create from Template page, select a Deployment template from the **Sample Template** drop-down list and copy the following `annotations` to the spec \> template \> metadata section in the **template**.

    **Note:** Replace <your-deployment-name\> with your application name.

    ```
    annotations:
      armsPilotAutoEnable: "on"
      armsPilotCreateAppName: "<your-deployment-name>"
      armsAppType: PHP                                
    ```

7.  If you install the ARMS application monitoring agent for the first time, you can modify the arms-php.ini ConfigMap in the arms-pilot namespace. The content of the file is the same as that of the php.ini file of the PHP application.

    **Note:** In the extension=/usr/local/arms/arms-php-agent/arms-7.2.so configuration, arms-7.2.so indicates that the version of the PHP application is V7.2. You can change the version value to 5.4, 5.5, 5.6, 7.0, 7.1, or 7.2.

8.  Copy the content of the arms-php.ini ConfigMap to the spec \> template \> spec \> containers section in the php.ini file. Set mountPath to the path of the php.ini file.

    ```
    volumeMounts:
            - name: php-ini
              mountPath: /etc/php/7.2/fpm/php.ini
              subPath: php.ini
    ```

    ```
    volumes:
          - name: php-ini
            configMap:
              name: arms-php.ini
    ```

    The following YAML template shows how to create a Deployment application and enable ARMS application monitoring for the application.


To enable ARMS application monitoring for an existing application, perform the following steps.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

5.  Click the Deployments tab or the StatefulSets tab. Select the **cluster** and **namespace** where the application that you want to manage is deployed. Find the application and choose **More** \> **View in YAML** in the **Actions** column.

6.  In the Edit YAML dialog box, add the following `annotations` to the spec \> template \> metadata section and click **Update**.

    **Note:** Replace <your-deployment-name\> with your application name.

    ```
    annotations:
      armsPilotAutoEnable: "on"
      armsPilotCreateAppName: "<your-deployment-name>"
      armsAppType: PHP                                
    ```

7.  Copy the content of the arms-php.ini ConfigMap to the spec \> template \> spec \> containers section in the php.ini file. Set mountPath to the path of the php.ini file.

    ```
    volumeMounts:
            - name: php-ini
              mountPath: /etc/php/7.2/fpm/php.ini
              subPath: php.ini
    ```

    ```
    volumes:
          - name: php-ini
            configMap:
              name: arms-php.ini
    ```


On the Deployments tab or the StatefulSets tab, find the application and check whether the **ARMS Console** button appears in the **Actions** column.

**Note:** If **ARMS Console** does not appear in the **Actions** column, check whether you have authorized ACK to access ARMS.

After you complete the preceding steps, ARMS Alibaba Cloud Container Service for Kubernetes is enabled for the application that is deployed in application monitoring. In the target application's **Action**Column click **ARMS console**. The Application Monitoring page of the ARMS console appears. ARMS application monitoring have the following capabilities:

1. Displays the overall application performance through key metrics. Automatically discovers the application topology.

2. The 3D topology shows the health status of applications, services, and hosts, as well as the upstream and downstream dependencies of the applications. It helps you quickly locate the services that induce faults, the applications affected by the faults, and the associated hosts, and comprehensively diagnose the root cause of the fault.

3. Capture abnormal and slow transactions, and obtain slow SQL statements, MQ accumulation analysis reports, or exception classification reports of the interface. Then, analyze common problems such as right or wrong and slow in more detail.

4. Automatically discovers and monitors common Web frameworks and RPC frameworks in application code, and automatically collects statistics on metrics such as the call volume, response time, and number of errors of Web interfaces and RPC interfaces.

