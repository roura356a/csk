# Access blockchain network by using administration and monitoring tools {#task_ul5_4rz_vdb .task}

-   Create the Alibaba Cloud blockchain network by using the Container Service blockchain solution.   For more information, see [EN-US\_TP\_7395.md\#](intl.en-US/Block Chain/Configure and deploy blockchain network.md#).
-   Access the blockchain network by using applications or CLI and complete the end-to-end test.  For more information, see [EN-US\_TP\_7400.md\#](intl.en-US/Block Chain/Access blockchain network by using applications.md#) or [EN-US\_TP\_7399.md\#](intl.en-US/Block Chain/Access blockchain network by using CLI.md#).

After creating the blockchain network by using the Alibaba Cloud Container Service blockchain solution,  you can access services on the blockchain network by using blockchain administration and monitoring tools based on Hyperledger Fabric SDKs.

-   The administration and monitoring tool can be deployed on an Alibaba Cloud container cluster together with the blockchain network. In this mode, the administration and monitoring tool directly accesses services by using the name and port of each blockchain service.
-   The administration and monitoring tool can also be deployed outside Alibaba Cloud container clusters. In this mode, the administration and monitoring tool accesses services by using the external address of the blockchain network and the external port of each service.

In this example, deploy the administration and monitoring tool outside Alibaba Cloud container clusters. The provided administration and monitoring tool is developed and adapted based on [Hyperledger Explorer](https://github.com/hyperledger/blockchain-explorer) . When the blockchain network is deployed,  the Hyperledger Explorer is deployed to the Kubernetes cluster by default.

You can use the administration and monitoring tool provided in this example, the official version of Hyperledger Explorer, self-developed administration and monitoring tool, or third-party administration and monitoring tool, and adapt the tool by referring to the source codes of the administration and monitoring tool provided in this example to access the Alibaba Cloud Container Service blockchain network.

Adapt the existing blockchain administration and monitoring tool as follows:

-   Directly use the script download-from-fabric-network.sh that is provided in the sample code to automatically download blockchain network configurations from the newly deployed blockchain network with one click, including certificates, keys, and blockchain network configuration files \(basically the config.json and network-config.yaml\).
-   Make sure that the blockchain administration and monitoring tool can be correctly loaded to the preceding blockchain network configurations.
-   If the blockchain administration and monitoring tool directly uses the channel name, external address, node name, or domain name of the blockchain, replace them with parameters in the configuration files \(config.json and network-config.yaml\) to keep the configurations of the administration and monitoring tool consistent with those of the target blockchain network.

1.   Run the kubectl get svc command on a master node of the Kubernetes cluster. You can also log on to the [Container Service console](https://cs.console.aliyun.com/), enter the Kubernetes cluster dashboard, click  **Services** in the left-side navigation pane, and check the external endpoint of the service <network name\>-explorer. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7401/15348401192737_en-US.png)

2.   Access the external endpoint in the browser. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7401/15348401192739_en-US.png)

    **Note:** Hyperledger Explorer is still in the project incubation phase, and the functionality remains to be improved. You can follow its [official project](https://github.com/hyperledger/blockchain-explorer) progress to get further functions and version updates.


