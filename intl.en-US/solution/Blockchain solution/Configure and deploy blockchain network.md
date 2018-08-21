# Configure and deploy blockchain network {#concept_kbx_dbv_vdb .concept}

After completing the environment preparations, you must configure and deploy the blockchain network. The blockchain network is a blockchain running environment, which is based on Hyperledger Fabric and consists of the following standard node types:

-   Orderer: Used to combine blockchain transactions into blocks. From the perspective of scalability, the blockchain solution uses the Orderer services of the Kafka type.
-   Kafka and ZooKeeper: Used to provide underlying services for Orderer in the form of clusters.
-   Peer: Used to store and maintain a ledger, create and run a chaincode container, and endorse transactions. From the perspective of high availability, the blockchain solution creates two peer nodes for each organization.
-   CouchDB: Used to store the state database of peer. The blockchain solution creates a CouchDB for each peer.
-   CA: Used to provide the PKI certificate service for applications. The blockchain solution creates a CA node for each organization.

For more information, see [Hyperledger Fabric official documentation](https://hyperledger-fabric.readthedocs.io/en/latest/).

To satisfy requirements of enterprise level applications, the blockchain solution provides master nodes with persistent data storage by using the shared file system created in [EN-US\_TP\_7393.md\#](intl.en-US/Block Chain/Environment preparations.md#).

The blockchain network is deployed and run on an Alibaba Cloud Container Service Kubernetes cluster. You can deploy multiple blockchain networks \(separated by namespaces\) in the same Kubernetes cluster or deploy one blockchain network in each Kubernetes cluster.

The blockchain network can be configured and deployed on the graphical interface or by using the Helm commands. Both of these methods are introduced in the following sections.

**Note:** Currently, the new organizations or peers cannot be dynamically added to an existing blockchain network. Therefore, you must delete the existing blockchain network and recreate one if you want to modify the blockchain network configurations.

## Configuration parameter description {#section_ydv_gbv_vdb .section}

The blockchain solution provides default values for most parameters to simplify your configuration process.  To customize the blockchain, you can view the following parameter descriptions and then configure the parameters.

|Parameter|Description  |
|:--------|:------------|
|sharedStorage|The mount address of the NAS file system. Parameters that must be provided to create a blockchain network, otherwise the creation fails.|
|storageCapacity|The initial size of the Persistent Volume Claim \(PVC\) of the NAS in Kubernetes. NAS supports dynamic scaling. The default size is 1G.|
|dockerImageRegistry|The Docker image repository URL: The image repository of the blockchain solution. Select an image repository based on the region in which the Kubernetes cluster resides.-   Inside China: registry.cn-hangzhou.aliyuncs.com/cos-solution
-   Outside China: registry.ap-southeast-1.aliyuncs.com/cos-solution

|
|fabricNetwork|Blockchain network name \(required\): The blockchain network is deployed as an application of Container Service. Therefore, the blockchain network name is an application name. Avoid using the name of a deployed application. The blockchain network name is also used as the name of the root directory for storing configurations and data in the shared file system.|
|fabricChannel|Blockchain network channel name: The channel name of Hyperledger Fabric. The blockchain solution automatically creates the channel with the specified name when the blockchain network is deployed.|
|externalAddress|External address \(required\): To access the blockchain network by using an application or administration and monitoring tool that is deployed outside the container cluster, you must provide the Internet address of a node in the Kubernetes cluster in which the blockchain network is deployed or the Internet address of the Server Load Balancer instance of the Kubernetes cluster as the external address. For more information about the configuration method, see [EN-US\_TP\_7393.md\#section\_ph2\_355\_vdb](intl.en-US/Block Chain/Environment preparations.md#section_ph2_355_vdb).|
|ordererDomain|Orderer domain: The Orderer domain in Hyperledger Fabric. Set this parameter based on actual requirements.|
|ordererNum|Number of Orderer nodes: This parameter applies to the Orderer services of the Kafka type, not the Solo type. Set this parameter to the number of Orderer nodes to be deployed. To modify the parameter value, you must modify the value of ordererExternalPortList at the same time to make sure the number of nodes and the number of external ports are the same. Otherwise, the blockchain network fails to be deployed.|
|peerDomain|Peer domain: The peer domain in Hyperledger Fabric. Set this parameter based on actual requirements.|
|orgNum|Number of organizations: The number of organizations in Hyperledger Fabric. The blockchain solution creates two peer nodes for each organization to guarantee the high availability and meet the requirements of business expansion. Specify the number of organizations according to your actual requirements. The number of actually deployed peer nodes is twice the number of organizations. To modify the parameter value, you must modify the values of peerExternalGrpcPortList, peerExternalEventPortList, and caExternalPortList at the same time to make sure the number of nodes and the number of external ports are the same. Otherwise, the blockchain network fails to be deployed.|
|ordererExternalPortList|Orderer external port list: To access the Orderer services by using an application outside the cluster, you must specify the external ports used by Orderer nodes or use the default port. Different blockchain networks must use different ports and make sure the number of ports in the list must be the same as the value of  ordererNum. Otherwise, the blockchain network fails to be deployed.|
|caExternalPortList|CA external port list: To access the CA services by using an application outside the cluster, you must specify the external ports used by CA nodes or use the default port. Different blockchain networks must use different ports and make sure the number of ports in the list must be the same as the value of orgNum. Otherwise, the blockchain network fails to be deployed.|
|peerExternalGrpcPortList|Peer gRPC external port list: To access the Peer services by using an application outside the cluster, which is based on the gRPC protocol by default, you must specify the external ports used by peer nodes or use the default port. Different blockchain networks must use different ports and make sure the number of ports in the list must be twice the value of orgNum. Otherwise, the blockchain network fails to be deployed.|
|peerExternalEventPortList|Peer event external port list: To access the Peer event services by using an application outside the cluster, you must specify the external ports used by peer nodes or use the default port. Different blockchain networks must use different ports and make sure the number of ports in the list must be twice the value of orgNum. Otherwise, the blockchain network fails to be deployed.|
|imagePullPolicy|Image pull policy: This is a Kubernetes parameter and is generally used for development and test.|
|hyperledgerFabricVersion|Hyperledger Fabric version: Currently, 1.1.0 is supported, and no configuration is required.|
|thirdPartyImageVersion|The image version of the third-party softwares \(such as CouchDB, Kafka, and ZooKeeper\) included in Hyperledger Fabric: Currently, 0.4.6 is supported, which corresponds to the Hyperledger Fabric 1.1.0, and no configuration is required.|
|explorer.enabled|Whether to automatically deploy the Hyperledger Explorer. During the deployment, the Server Load Balancer instance is created, and the blockchain explorer function based on Web UI is provided by using port 80. The default value is true.|
|logService.enabled|Whether to enable the support for Alibaba Cloud Log Service.  The default value is false.  For more information about Alibaba Cloud Log Service, see Collect Kubernetes cluster logs by using Alibaba Cloud Log Service. [../../../../dita-oss-bucket/SP\_235/DNCS011878073/EN-US\_TP\_6942.md\#](../../../../intl.en-US/User Guide/Kubernetes cluster/Logs/Collect Kubernetes logs.md#)|
|logService.region|Specify the region in which the Log Service project resides if logService.enabled is set to true. Set the parameter value as per your needs. For more information about Alibaba Cloud Log Service, see [../../../../dita-oss-bucket/SP\_235/DNCS011878073/EN-US\_TP\_6942.md\#](../../../../intl.en-US/User Guide/Kubernetes cluster/Logs/Collect Kubernetes logs.md#)|
|logService.userID|Specify the user ID of the Alibaba Cloud primary account if logService.enabled is set to true. For more information about Alibaba Cloud Log Service, see [../../../../dita-oss-bucket/SP\_235/DNCS011878073/EN-US\_TP\_6942.md\#](../../../../intl.en-US/User Guide/Kubernetes cluster/Logs/Collect Kubernetes logs.md#)|
|logService.machineGroup|Specify the machine group of the Log Service project if logService.enabled is set to true. For more information about Alibaba Cloud Log Service, see [../../../../dita-oss-bucket/SP\_235/DNCS011878073/EN-US\_TP\_6942.md\#](../../../../intl.en-US/User Guide/Kubernetes cluster/Logs/Collect Kubernetes logs.md#)|
|logLevel|Hyperledger Fabric log levels for different types of nodes \(peer, orderer, couchdb\).. Optional values are: CRITICAL | ERROR | WARNING | NOTICE | INFO | DEBUG, the default value is INFO. |

## Deploy blockchain network in Container Service console {#section_iby_r2v_vdb .section}

1.  Log on to the Container Service console. Under Kubernetes, click Store \> **App Catalog** in the left-side navigation pane. Click **ack-hyperledger-fabric** on the App Catalog page.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7395/15348360022670_en-US.png)

2.  Select the cluster in which the blockchain network is to be deployed from the **Clusters** drop-down list and enter the  **Release Name**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7395/15348360022671_en-US.png)

3.  Click the **Values** tab. View or modify the corresponding deployment parameters.

    -   sharedStorage: Enter the NAS file system mount address during the environment preparation \(required, otherwise the deployment fails\).
    -   dockerImageRegistry: Enter the container image repository address in the annotations as the value according to the region in which the blockchain network is to be deployed \(inside or outside China\).
    -   externalAddress: Enter the EIP bound to the worker node to generate the connection profile.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7395/15348360022672_en-US.png)

4.  Click **Deploy**.

    **Note:** If sharedStorage is not configured, the spec.nfs.server: Required value error is reported during the deployment process. When the error occurs, you must delete the corresponding release, then enter the sharedStorage parameter value and redeploy. 

5.  Enter the cluster dashboard. Check the status of the pods related to the blockchain network. Wait until the status of all the pods becomes Running.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7395/15348360022673_en-US.png)


## Deploy blockchain network by using Helm commands {#section_p2v_gbv_vdb .section}

For more information about how to use Helm to deploy applications in Container Service Kubernetes clusters, see [../../../../dita-oss-bucket/SP\_235/DNCS011878073/EN-US\_TP\_6898.md\#](../../../../intl.en-US/User Guide/Kubernetes cluster/Application Management/Simplify Kubernetes application deployment by using Helm.md#).

1.  Log on to a master node of the Kubernetes cluster by using SSH. Use the root account and the password configured when creating the Kubernetes cluster.
2.  Run Helm commands to deploy the blockchain network.
    -   To deploy the blockchain network by using the default parameter configurations, run the following command:

        ```
        helm install --name blockchain-network01 --set "sharedStorage=987a6543bc-abc12.cn-hangzhou.nas.aliyuncs.com" incubator/ack-hyperledger-fabric
        ```

        Wherein, `--name` indicates the Helm release name of the blockchain network. You can set the parameter value as per your needs.

    -   To deploy a blockchain network to the specified namespace \(for example, network01\), run the following command:

        ```
        helm install --namespace network01  --name blockchain-network01 --set "sharedStorage=987a6543bc-abc12.cn-hangzhou.nas.aliyuncs.com" incubator/ack-hyperledger-fabric
        ```

        Wherein, `--namespace` indicates the name of the namespace in which the blockchain network is to be deployed. You can set the parameter value as per your needs.

    -   To deploy the blockchain network with simple or a small number of configuration parameters, use the set parameter to pass in the configuration parameter values. For example:

        ```
        helm install --name blockchain-network01 --set "fabricChannel=mychannel,sharedStorage=987a6543bc-abc12.cn-hangzhou.nas.aliyuncs.com" incubator/ack-hyperledger-fabric
        ```

    -   To deploy the blockchain network with complex or a large number of configuration parameters, use the YAML file to pass in the configuration parameter values. For example:

        ```
        helm install --values network01.yaml --name blockchain-network01 incubator/ack-hyperledger-fabric
        ```

        Wherein, `--values` indicates the YAML file for customizing configuration parameters. You can set the parameter values as per your needs. See the following example:

        ```
        # sample values yaml
        sharedStorage: 987a6543bc-abc12.cn-hangzhou.nas.aliyuncs.com
        fabricNetwork: network01
        fabricChannel: tradechannel
        orgNum: 3
        ordererNum: 4
        ordererDomain: shop
        peerDomain: shop
        externalAddress: 11.22.33.44
        caExternalPortList: ["31054", "31064", "31074"]
        ordererExternalPortList: ["31050", "31060", "31070", "31080"]
        peerExternalGrpcPortList: ["31051", "31061", "31071", "31081", "31091", "31101"]
        peerExternalEventPortList: ["31053", "31063", "31073", "31083", "31093", "31103"]
        ```

        Check if the Helm release of the blockchain network is successfully deployed.  Run the following command to make sure the release status of the blockchain network is Deployed.

3.  Run the following command to check if all the node pods of the blockchain network are successfully running and make sure the status of all the pods in the blockchain network is Running.

    ```
    kubectl get pod
    ```

    -   If a namespace is specified, for example, network01, run the following command:

        ```
        kubectl get pod -n network01
        ```

    -   To listen to the pod status changes in watcher mode, run the following command:

        ```
        kubectl get pod -w
        ```

4.  Run the following command to check the blockchain network deployment status. If the status is `DEPLOYED`, the blockchain network is successfully deployed.

    ```
    helm list
    ```


Then, the blockchain network configuration and deployment are complete.

## Naming rules for node services of blockchain network {#section_efv_gbv_vdb .section}

For standard node types of Hyperledger Fabric, the service naming rule is as follows:

```
<blockchain network name>-<node type><serial number>
```

For example:

```
network01-peer1
network01-peer2
network01-orderer1
network01-ca1
```

Although blockchain networks can be differentiated by namespaces in a Kubernetes cluster, the preceding service naming rule still uses a blockchain network name as the prefix to keep consistent with the blockchain solution of swarm clusters.

A service name must be consistent with the node name in the blockchain certificate and key. This type of service name \(together with the service port\) can be used to directly access blockchain applications or CLI deployed in the same Kubernetes cluster. The external address is not required.

## Problem diagnosis {#section_hfv_gbv_vdb .section}

This section introduces some common approaches, methods, and tools for diagnosing problems and errors that may occur during blockchain configuration, deployment, and access.

First, check if all preparations described in [EN-US\_TP\_7393.md\#](intl.en-US/Block Chain/Environment preparations.md#)  have been completed correctly. 

Then, use Kubernetes-related commands, such as kubectl describe pod, kubectl logs, and kubectl get pod -o yaml, to view the deployment events and output logs.

In addition, to assist in fault diagnosis and problem troubleshooting, the blockchain solution deploys a custom fabric-utils container in the blockchain network. This container integrates with common basic tools, such as telnet, ping, nslookup, and curl. You can run the following kubectl command on the master node to enter the fabric-utils container and use suitable tools to diagnose and analyze the problems. For example:

```
kubectl exec -it fabric-utils-pod bash
```

Finally, for problems or errors related to Hyperledger Fabric, use Hyperledger Fabric official documentation, StackOverFlow, or Google/Bing/Baidu for relevant information or solutions.

