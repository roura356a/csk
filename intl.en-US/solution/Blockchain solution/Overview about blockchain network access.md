# Overview about blockchain network access {#concept_krh_xwv_vdb .concept}

After configuring and deploying the blockchain network on Alibaba Cloud, the blockchain developer or administrator can access the blockchain network and use different blockchain node services. Common access methods are as follows:

-   The developer or administrator remotely connects to the blockchain node container, and tests or manages the blockchain network by using CLI.
-   The blockchain applications connect to the blockchain network services such as CA, Orderer, and Peer for transactions and service calls based on the blockchain.
-   The blockchain administration and monitoring tools connect to the blockchain network, managing and monitoring the blockchain network and each node in the graphical or automated way. 

These blockchain access methods, applications, and codes can be developed and deployed according to your own business and technical requirements. They can be either deployed as container applications in the container cluster, together with the blockchain network, or deployed in your own environment that you can access the blockchain network from outside the container cluster.  For the second method, create an Internet IP address or a Server Load Balancer instance for the blockchain network in advance and configure security group rules for NodePort to allow external access.

The following documents provide simple examples and instructions for main blockchain access methods to help you better understand and develop related applications and tools.  At the same time, the Blockchain Solution provides configuration files that can be downloaded in one click \(including certificates, keys and blockchain network configuration files necessary for access to blockchain services\) to accelerate the development and testing processes of blockchain applications and management and monitoring tools.

-   [Configure Internet IP address and external port](intl.en-US/solution/Blockchain solution/Configure Internet IP address and external port.md#)
-   [Access blockchain network by using CLI](intl.en-US/solution/Blockchain solution/Access blockchain network by using CLI.md#)
-   [EN-US\_TP\_16624.md\#](intl.en-US/solution/Blockchain solution/Access blockchain network by using applications.md#)
-   [Access blockchain network by using administration and monitoring tools](intl.en-US/solution/Blockchain solution/Access blockchain network by using administration and monitoring tools.md#)

