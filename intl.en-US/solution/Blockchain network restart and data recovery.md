# Blockchain network restart and data recovery {#task_smy_4rx_32b .task}

Container Service blockchain solution supports restarting the blockchain network. At the same time, the original blockchain configuration and data can be reused.

-   You have created a Kubernetes cluster. For more information, see [Create a Kubernetes cluster](../../../../intl.en-US/User Guide/Kubernetes cluster/Clusters/Create a cluster.md#). 
-   Blockchain network is already running on the Kubernetes cluster. For more information, see [Quick start](intl.en-US/Block Chain/Quick start.md#). 
-   You have connected to the Kubernetes cluster by using SSH, see [Access Kubernetes clusters by using SSH](../../../../intl.en-US/User Guide/Kubernetes cluster/Clusters/Access Kubernetes clusters by using SSH.md#).

1.  Use SSH to log on to a master node of the Kubernetes cluster as the root account. 
2.  Run the helm delete --no-hooks --purge <blockchain network Helm Release name\> command to delete blockchain network.  

    **Note:** Use the --no-hooks parameter to avoid deleting the original data directory.

    ```
    helm delete --no-hooks --purge network01 #In this example, the Helm Release name is network01
    release "network01" deleted
    ```

3.  Use the same blockchain network name as the fabricNetwork variable value to create a new blockchain network  by using the Container Service console or the Helm command line. Therefore, you can reuse the original data directory. For more information, see [Configure and deploy blockchain network](intl.en-US/Block Chain/Configure and deploy blockchain network.md#). An example of Helm command is as follows. 

    ```
    helm install --name network01 --set "sharedStorage=029bb489d2-ikw80.cn-hangzhou.nas.aliyuncs.com" incubator/ack-hyperledger-fabric #Replace with your NAS mount address
    
    ```

    To back up the blockchain data directory, use the following command example to mount the NAS file system to ECS. In this example, blockchain network name is network01.

    ```
    mkdir /data
    mount -t nfs -o vers=4.0 987a6543bc-abc12.cn-hangzhou.nas.aliyuncs.com:/ /data #Replace with your NAS mount address
    ```

    **Note:** Now, you must back up the /data/fabric/network01 data directory.

    Using this method, you can perform blockchain network data backup, migration, and recovery.


