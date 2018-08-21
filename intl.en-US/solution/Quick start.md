# Quick start {#concept_qqp_vqt_vdb .concept}

This document is a quick start guide for deploying the blockchain network from the beginning, in which most settings use the default or example values. For more information about the configurations, see the subsequent sections.

## Limits {#section_gny_zqt_vdb .section}

-   You must register an account and activate Container Service.
-   The container cluster and the NAS file system must be in the same region. Make sure the selected region supports both the Kubernetes cluster and the NAS file system \(intersection of both\). For the list of regions that support the NAS file system, see **Products** \> **Storage & CDN** \> **NAS** \> **File System List**.
-   The usage of a file system only applies to the development and test phases of blockchain applications and solutions. For deployment in the production environment, contact us to further discuss specific business and technical needs and determine the most suitable method.

## Step 1. Create a Kubernetes cluster {#section_gln_lrt_vdb .section}

1.  Log on to the [Container Service console](https://cs.console.aliyun.com/).
2.  Under Kubernetes, click **Clusters** in the left-side navigation pane. Click **Create Kubernetes Cluster** in the upper-right corner.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7392/15348225782535_en-US.png)

3.  Configure the basic information for the cluster. In this example, complete the configurations as follows:
    -   Enter the Cluster Name. For example, k8s-blockchain.
    -   Select **China East 1 \(Hangzhou\)** as the Region.
    -   Select **China East 1 Zone B** as the Zone.
    -   Select **Auto Create** to create a **Virtual Private Cloud \(VPC\)** together with the Kubernetes cluster, or Use Existing to use an existing VPC.
    -   Configure the **Logon Password** and **Confirm Password**.
    -   Configure instance specifications and quantity. The blockchain network deployment is resource-intensive. Therefore, we recommend that you use the default configurations.
    -   Select the **Enable SSH access for Internet** check box.
    -   Click **Create Cluster**. Wait a few minutes until the cluster is successfully created.
4.  On the Cluster List page, click **Manage** at the right of the created cluster \(k8s-blockchain\).

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7392/15348225782536_en-US.png)

5.  On the Basic Information page of the cluster, record the Master node SSH IP address, which is an Internet address.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7392/15348225782544_en-US.png)

6.  On the Node List page, record the IP addresses \(intranet addresses\) of the cluster nodes \(both master nodes and worker nodes\).

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7392/15348225782545_en-US.png)


## Step 2. Bind an EIP to a worker node {#section_ppn_s5t_vdb .section}

1.  Log on to the [Elastic IP Address \(EIP\) console](https://ip.console.aliyun.com/).
2.  Click **Elastic IP Addresses** in the left-side navigation pane. Click **Create EIP**.
3.  Select the Region in which the cluster k8s-blockchain resides. Complete the other configurations as per your needs. Click **Buy Now**.
4.  After activating the EIP, wait until the purchased EIP is displayed in the list and then click **Bind** at the right of the EIP.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7392/15348225782546_en-US.png)

5.  Select ECS Instance from the Instance Type drop-down list and select a worker node \(the instance name begins with node\). Then, click **OK**.
6.  After binding the EIP to the worker node, record the IP address of the purchased EIP on the Elastic IP Addresses page.

## Step 3. Create a file system and add a mount point {#section_z3m_vwt_vdb .section}

1.  Log on to the [NAS console](https://nas.console.aliyun.com/).
2.  Select **China East 1 \(Hangzhou\)** in the region list and click **Create File System**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7392/15348225792547_en-US.png)

3.  Complete the configurations in the displayed Create File System dialog box.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7392/15348225792548_en-US.png)

    -   **Region**: Select **China East 1 \(Hangzhou\)**. Select the region in which the container cluster resides.
    -   **Storage Type**: In this example, select **Capacity-type**.
    -   **Protocol Type**: Select **NFS**.
    -   **Zone**: Select **China East 1 Zone B**. Different zones in the same region are interconnected.
    -   Click **OK**.
4.  Click **Click to go** in the displayed dialog box.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7392/15348225792550_en-US.jpg)

5.  Configure the NAS storage package.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7392/15348225792551_en-US.png)

    -   **Storage Type**: Select **Capacity-Type**.
    -   **Order Duration**: In this example, select one month. Select the duration as per your needs.
    -   Click **Buy Now**.
6.  If **Use Existing** is selected in the VPC field when you create the Kubernetes cluster, skip this step. If **Auto Create** is selected in the VPC field when you create the Kubernetes cluster, go to the [VPC console](https://vpcnext.console.aliyun.com/vpc/). Change the VPC instance name to one that is easy to identify, for example, blockchain\_huadong1.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7392/15348225792552_en-US.png)

7.  Click **Add Mount Point** at the right of the created file system. Complete the configurations in the displayed Add Mount Point dialog box.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7392/15348225792553_en-US.png)

    -   **Mount Point Type**: Select **VPC**.
    -   **VPC:** Select the VPC used when creating the container cluster.
    -   **VSwitch**: Select the VSwitch used when creating the container cluster.
    -   **Permission Group**: Select **VPC default permission group \(allow all\)**.
    -   Click **OK**.
8.  On the File System List page, click **Manage** at the right of the file system. Record the Mount Address on the File System Details page.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7392/15348225792557_en-US.png)


## Step 4. Configure and deploy blockchain network {#section_lc1_zqt_vdb .section}

1.  Under Kubernetes, click **Store** \> **App Catalog** in the left-side navigation pane. Click **ack-hyperledger-fabric** , and select the region.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7392/15348225792560_en-US.png)

2.  Select the **cluster** in which the blockchain network is to be deployed from the Clusters drop-down list and enter the **Release Name**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7392/15348225792561_en-US.png)

3.  Click the **Values** tab. View or modify the corresponding deployment parameters.

    -   sharedStorage: Create a file system and add a NAS file system mount address recorded in the mount point \(required, otherwise the deployment fails\).
    -   dockerImageRegistry: Enter the container image repository address in the annotations as the value according to the region in which the blockchain network is to be deployed \(inside or outside China\).
    -   externalAddress: Enter the EIP bound to the worker node to generate the connection profile.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7392/15348225792562_en-US.png)

4.  Click **Deploy**.

    **Note:** If sharedStorage is not configured, the spec. NFS. error for server: required value error is reported during the deployment process. When the error occurs, you must delete the corresponding publication and then enter the sharedStorage parameter values, and redeploy.

5.  Enter the cluster dashboard. Check the status of the pods related to the blockchain network. Wait until the statuses of all the pods become Running.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7392/15348225792563_en-US.png)


The Kubernetes service of the container dashboard also supports deploying the blockchain network by using Helm. For more information, see [Configure and deploy blockchain network](intl.en-US/Block Chain/Configure and deploy blockchain network.md#).

## Step 5. Test blockchain network by using CLI {#section_h5k_ryt_vdb .section}

1.  On a master node of the Kubernetes cluster, run the following command to enter the CLI container.

    ```
    kubectl exec -it <fabricNetwork>-fabric-cli bash
    ```

2.  Run the following command to start the CLI test.

    ```
    ./cli-test.sh
    ```

3.  During the test, press any key to continue.
4.  If no errors occur during the test and the following words are displayed, it indicates that the test is successfully completed.

    ```
      ===================== Query on PEER4 on channel 'bankchannel' is successful ===================== 
      Press any key to continue...
      ===================== All GOOD, End-2-End execution completed =====================
    ```


## Step 6. Access blockchain explorer {#section_rcr_1zt_vdb .section}

1.  Log on to the [Container Service console](https://cs.console.aliyun.com/).
2.  Under the Kubernetes, click **Application** \> **Services** in the left-side navigation pane. Select the target cluster and namespace, find the <Network name\>-explorer service and access external endpoint.

    You can also run the kubectl get svc command on the master node of the Kubernetes cluster, or log on to the Container Service console. Then, go to the Kubernetes clusters, and click **Services** in the left-side navigation pane. Then view the EXTERNAL-IP \(external endpoint\) of the <Network name\>-explorer service.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7392/15348225792564_en-US.png)

3.  Access the external endpoint in the browser.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7392/15348225792565_en-US.png)


## Step 7.  Delete blockchain network {#section_yc1_zqt_vdb .section}

1.  Under the Kubernetes, click **Application** \> **Release** in the left-side navigation pane. Select the cluster from the Clusters drop-down list. Click **Delete** at the right of the release name of the blockchain network.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7392/15348225796451_en-US.png)

2.  Click **OK** in the displayed dialog box.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7392/15348225796106_en-US.png)


Then, the environment preparations, configuration and deployment, test, and deletion of the blockchain network are complete. For further development and test, you can repeat the steps of configuration, deployment, test, and deletion of the blockchain network. You can also customize the blockchain network environment according to actual requirements and product documents.

