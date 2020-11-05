# Monitor application performance

You can use Application Real-Time Monitoring Service \(ARMS\) to monitor Java or PHP applications that are deployed to a Container Service for Kubernetes \(ACK\) cluster. ARMS can automatically discover topologies, generate 3D topologies, discover and monitor API operations, and capture abnormal and slow transactions for applications. This improves the efficiency of online issue diagnosis.

-   [Create a cluster]()
-   [Activate ARMS](/intl.en-US/Quick start/Activate ARMS.md)

    **Note:** The PHP application monitoring feature is in public preview. You are not charged for this feature.


[ARMS](/intl.en-US/Product overview/Overview.md) is an application performance management \(APM\) product of Alibaba Cloud. After you deploy the ARMS application monitoring agent to an ACK cluster, you can enable comprehensive monitoring of Java applications without changing code. This helps you identify abnormal and slow API operations, view request parameters, detect memory leaks, and discover application bottlenecks. In this case, you can diagnose issues online in an efficient manner. For more information, see [Overview](/intl.en-US/Application monitoring/Overview.md).

## Install the ARMS application monitoring agent

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Marketplace** \> **App Catalog**. On the **App Catalog** page, click **ack-arms-pilot**.

3.  On the App Catalog - ack-arms-pilot page, select the cluster that you want to manage in the Deploy section and click **Create**.


## Enable ARMS application monitoring for Java applications

The following steps demonstrate how to enable ARMS application monitoring for a new application or an existing application.

To enable ARMS application monitoring when you create an application, perform the following steps.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, click **Workload**.

5.  On the Deployments tab, click **Create from Template** in the upper-right corner.

6.  On the page that appears, select a template from the **Sample Template** drop-down list, and add the following `annotations` to the spec \> template \> metadata section.

    **Note:** Replace <your-deployment-name\> with your application name.

    ```
    annotations:
      armsPilotAutoEnable: "on"
      armsPilotCreateAppName: "<your-deployment-name>"                                
    ```

    ![YAML Example](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5354934061/p53707.png)

    The following YAML template shows how to create an application and enable ARMS application monitoring for the application.


## Enable ARMS application monitoring for PHP applications

The following steps demonstrate how to enable ARMS application monitoring for a new application or an existing application.

To enable ARMS application monitoring when you create an application, perform the following steps.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, click **Workload**.

5.  On the Deployments tab, click **Create from Template** in the upper-right corner.

6.  On the page that appears, select a template from the **Sample Template** drop-down list, and add the following `annotations` to the spec \> template \> metadata section.****

    **Note:** Replace <your-deployment-name\> with your application name.

    ```
    annotations:
      armsPilotAutoEnable: "on"
      armsPilotCreateAppName: "<your-deployment-name>"
      armsAppType: PHP                                
    ```

7.  If you install the ARMS application monitoring agent for the first time, you can modify the arms-php.ini ConfigMap in the arms-pilot namespace. The content of the file is the same as that of the php.ini file of the PHP application.

    **Note:** By default, the configuration extension=/usr/local/arms/arms-php-agent/arms-7.2.so exists in the arms-php.ini ConfigMap. arms-7.2.so indicates that the version of the PHP application is 7.2. You can change the version to 5.4, 5.5, 5.6, 7.0, 7.1, or 7.2.

8.  Add the content of the arms-php.ini ConfigMap to the spec \> template \> spec \> containers section in the php.ini file. Set mountPath to the path of the php.ini file.

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

    The following YAML template shows how to create an application and enable ARMS application monitoring for the application.


To enable ARMS application monitoring for an existing application, perform the following steps.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, click **Workload**.

5.  Click the Deployments tab or the StatefulSets tab. Select the **namespace** that you want to manage, find the application for which you want to enable ARMS application monitoring, and then choose **More** \> **View in YAML** in the **Actions** column.

6.  In the Edit YAML dialog box, add the following `annotations` to the spec \> template \> metadata section and click **Update**.

    **Note:** Replace <your-deployment-name\> with your application name.

    ```
    annotations:
      armsPilotAutoEnable: "on"
      armsPilotCreateAppName: "<your-deployment-name>"
      armsAppType: PHP                                
    ```

7.  Add the content of the arms-php.ini ConfigMap to the spec \> template \> spec \> containers section in the php.ini file. Set mountPath to the path of the php.ini file.

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


On the Deployments tab or the StatefulSets tab, find the PHP application and verify that the **ARMS Console** button appears in the **Actions** column.

**Note:** If you cannot find **ARMS Console** in the **Actions** column, check whether you have authorized ACK to access ARMS.

