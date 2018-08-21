# Access blockchain network {#concept_krh_xwv_vdb .concept}

After configuring and deploying the blockchain network on Alibaba Cloud, the blockchain developer or administrator can access the blockchain network and use different blockchain node services.  Common access methods are as follows:

-   The developer or administrator remotely connects to the blockchain node container, and tests or manages the blockchain network by using CLI.
-   The blockchain applications connect to the blockchain network services such as CA, Orderer, and Peer for transactions and service calls based on the blockchain.
-   The blockchain administration and monitoring tools connect to the blockchain network, managing and monitoring the blockchain network and each node in the graphical or automated way. 

These blockchain access methods, applications, and codes can be developed and deployed according to your own business and technical requirements. They can be either deployed as container applications in the container cluster, together with the blockchain network, or deployed in your own environment that you can access the blockchain network from outside the container cluster.  For the second method, create an Internet IP address or a Server Load Balancer instance for the blockchain network in advance and configure security group rules for the external port NodePort to allow external access.

The following documents provide simple examples and instructions for main blockchain access methods to help you better understand and develop related applications and tools.  At the same time, the blockchain solution provides configuration files that can be downloaded with one click \(including certificates, keys, and blockchain network configuration files required to connect to blockchain services\) to accelerate the development and testing processes of blockchain applications, administration, and monitoring tools.

-   [EN-US\_TP\_7398.md\#](intl.en-US/Block Chain/Configure Internet IP address and external port.md#)
-   [EN-US\_TP\_7399.md\#](intl.en-US/Block Chain/Access blockchain network by using CLI.md#)
-   [EN-US\_TP\_7400.md\#](intl.en-US/Block Chain/Access blockchain network by using applications.md#)
-   [EN-US\_TP\_7401.md\#](intl.en-US/Block Chain/Access blockchain network by using administration and monitoring tools.md#)

