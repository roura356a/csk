# View blockchain network logs {#concept_nb3_3pv_vdb .concept}

Logs generated when the blockchain network is running are output as container logs of Peer, Orderer, CA, Kafka, and ZooKeeper  node types.  The Container Service blockchain solution allows you to view these logs in the Container Service console, by using Kubernetes commands, or by means of Alibaba Cloud Log Service.  This document introduces these three methods in details.

## View logs in Container Service console {#section_djh_kpv_vdb .section}

The Container Service console allows you to conveniently view logs on GUI.  After deploying the blockchain network, you can follow these steps to view the container logs of the corresponding node.

**Procedure**

1.  Log on to the [Container Service console](https://cs.console.aliyun.com/).
2.  Under **Kubernetess**, click **Clusters** in the left-side navigation pane. Click **Dashboard** at the right of the cluster.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16620/15362008789850_en-US.png)

3.  Click **Pods** in the left-side navigation pane and then click the **Logs** icon.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16620/15362008799851_en-US.png)

4.  View the log details.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16620/15362008799852_en-US.png)

5.  You can also click the name of a pod and click **LOGS**. Then, view the log details.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16620/15362008799853_en-US.png)


## View logs by using Kubernetes commands {#section_jjh_kpv_vdb .section}

You can also use the standard kubectl logs command to view the Container Service logs of the blockchain network.

**Procedure**

1.  Log on to the Container Service console. Click Kubernetes \> Clusters in the left-side navigation pane. Click **Manage** at the right of the cluster in which the blockchain network is deployed. Obtain the  **Master node SSH IP address**.
2.  Log on to the master node of the Kubernetes cluster by using SSH. Enter the username root and the password configured when creating the cluster.
3.  Run the kubectl get pod command to obtain the pod list and select the name of the pod whose logs you want to view.
4.  Run the kubectl logs pod name command to view the logs.
5.  If a pod contains multiple containers, you can run the kubectl logs pod name container name command to view the logs of a container.

## View logs by using Alibaba Cloud Log Service {#section_ljh_kpv_vdb .section}

The log function of the Container Service console and the Kubernetes commands can basically meet the common requirements of log viewing. However, enterprise level requirements may include advanced functions such as log storage, real-time query and analysis, alarm, and visualized report. Then, you can integrate with [Alibaba Cloud Log Service](../../../../intl.en-US/Product Introduction/What is Log Service.md#) for expansion.

Container Service blockchain solution supports integrating with Alibaba Cloud Log Service. The basic procedures are as follows. For more information about integrating Container Service Kubernetes clusters with Alibaba Cloud Log Service, see [../../../../dita-oss-bucket/SP\_235/DNcsk1877477/EN-US\_TP\_16694.md\#](../../../../intl.en-US/User Guide/Kubernetes cluster/Logs/Overview.md#).

You may be charged by using Alibaba Cloud Log Service. For more information, see [Log Service billing](../../../../intl.en-US/Pricing/Billing method.md#) method.

**Procedure**

1.  Log on to the [Log Service console](https://sls.console.aliyun.com/) and activate Log Service as instructed.
2.  Click **Project Management** in the left-side navigation pane, and click **Create Project**in the upper-right corner.
3.  Enter the Project Name, and select the Region in which the blockchain network resides. Then, click **Confirm**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16620/15362008809854_en-US.png)

4.  Click **Create** in the displayed dialog box to create the Logstore.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16620/15362008809855_en-US.jpg)

5.  The Create Logstore dialog box appears. Enter the Logstore name. Complete the other configurations as per your needs. Then, click **Confirm**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16620/15362008809856_en-US.jpg)

6.  Click **Data Import Wizard** in the displayed dialog box.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16620/15362008809857_en-US.png)

7.  Select Docker Stdout under Third-Party Software. Then, click **Next**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16620/15362008809858_en-US.png)

8.  In the Plug-In Configuration field, enter the following example configuration.  For more information about the configurations, see [Container standard output](../../../../intl.en-US/User Guide/Logtail collection/Data Source/Containers-standard output.md#).  Then, click **Next**.

    ```
    {
      "inputs": [
          {
              "type": "service_docker_stdout",
              "detail": {
                  "Stdout": true,
                  "Stderr": true,
                  "IncludeLabel": {
                  },
                  "ExcludeLabel": {
                  }
              }
          }
      ]
     }
    ```

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16620/15362008809859_en-US.png)

9.  Click **Create Machine Group**.

    The Create Machine Group dialog box appears.Enter the custom machine group name in the Group Name field. Select **User-defined Identity** from the Machine Group Identification drop-down list. In the User-defined Identity field, enter the same content as that in the Group Name field. Then, click **Confirm**. The example is as follows:

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16620/15362008809860_en-US.png)

10. Select the created machine group and then click **Apply to Machine Group**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16620/15362008809861_en-US.png)

11. Add the key \(for example, \_pod\_name\_\) for creating the index as per your needs. After completing the configurations, click **Next**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16620/15362008809862_en-US.png)

12. Click **Confirm**. Then, the creation and initial configuration of Alibaba Cloud Log Service are complete. Deploy a new blockchain network by using the blockchain solution 
13. and configure the parameters integrated with Log Service on the Values page of the blockchain solution.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16620/15362008809863_en-US.png)

    Set the parameter enabled to true, which indicates to enable Log Service. Set the parameter machineGroup to the user-defined identity configured in the machine group. In this example, it is blockchain-network01.

    To set the parameter region, see [Linux](../../../../intl.en-US/User Guide/Logtail collection/Install/Linux .md#) to search for the corresponding installation commands and the region ID. For example, cn\_hangzhou indicates to write logs from the Alibaba Cloud intranet in the region Hangzhou to Log Service and no Internet bandwidth is consumed.

    To set the parameter userID, see the following figure.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16620/15362008809864_en-US.png)

14. Log on to the Log Service console. Click the project name and then click **Search** at the right of the Logstore.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16620/15362008819865_en-US.png)

    The blockchain network log example is as follows:

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16620/15362008819866_en-US.png)

15. Log Service supports complex queries. For more information about the query syntax and other advanced functions of Log Service, see [Query syntax](https://www.alibabacloud.com/help/zh/faq-detail/29060.htm).

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16620/15362008819867_en-US.png)


