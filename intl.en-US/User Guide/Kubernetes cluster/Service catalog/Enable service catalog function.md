# Enable service catalog function {#task_qj5_sd4_vdb .task}

1.   Log on to the [Container Service console](https://cs.console.aliyun.com). 
2.   Click Kubernetes \>**Store \>** \> **Service Catalog in the left-side navigation pane.**Select the cluster from the Cluster drop-down list in the upper-right corner. 
3.   If you have not deployed the service catalog, click to install the service catalog as instructed on the page. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6924/15330273174588_en-US.png)

4.   After the installation, the service broker, which is installed by default, is displayed on the Service Catalog page. You can click the mariadb-broker to view the details. 

    **Note:** The service catalog is implemented as an extension API server and a controller. After Alibaba Cloud Container Service installs the service catalog function, the namespace catalog is created.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6924/15330273174590_en-US.png)

5.   Click **Clusters** in the left-side navigation pane. Click **Dashboard** at the right of a cluster. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6924/15330273174591_en-US.png)

6.   In the Kubernetes dashboard, select catalog as the Namespace in the left-side navigation pane. You can see the resource objects related to catalog apiserver and controller are installed under this namespace. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/6924/15330273174592_en-US.png)


Then, you have successfully enabled the service catalog function. You can create a managed service by using the service broker in the service catalog, and apply the managed service to your applications.

