# 以CLI方式访问区块链 {#task_zg2_cpz_vdb .task}

区块链解决方案在部署区块链网络过程中创建了 CLI 容器，主要用于以命令行方式连接到区块链网络的 Peer 和 Orderer 节点，执行 Hyperledger Fabric 支持的 CLI 命令，可以满足对区块链网络的测试、管理等需求。

登录 CLI 容器的方式主要是在支持 kubectl 命令的环境中，运行 kubectl exec -it <fabricNetwork\>-fabric-cli -n <namespace名称\> bash 命令进入 CLI 容器。在该 CLI 容器中，我们提供了基于 Hyperledger Fabric 的标准端到端 CLI 测试脚本，如果用户需要修改该测试脚本，可从任意一个 ECS 节点上的文件路径 /data/fabric/<区块链网络名称\>/cli/cli-test.sh 找到该文件。

1.  用 root 账户以 SSH 方式登录 Kubernetes 集群的 master 节点 （获取地址方式请参见[环境准备](intl.zh-CN/解决方案/区块链解决方案/环境准备.md#)\)，或者在一个支持 kubectl 远程管理 Kubernetes 集群的环境。 
2.  运行命令进入CLI 容器：kubectl exec -it -n <namespace名称\> <fabricNetwork\>-fabric-cli bash，例如： kubectl exec -it -n network01 <fabricNetwork\>-fabric-cli bash。 
3.   运行以下测试脚本：./cli-test.sh ，然后 CLI 测试便开始运行。 
4.   测试过程中在每一步完成后会暂停，以方便用户查看执行过程和结果，然后在用户按下任意键之后继续测试步骤。当测试脚本成功执行完毕后，可以见到类似以下的输出信息： 

    ```
    Query Result: 90
     2017-11-12 09:22:31.452 UTC [main] main -> INFO 007 Exiting.....
     ===================== Query on PEER4 on channel 'bankchannel' is successful ===================== 
     Press any key to continue...
     ===================== All GOOD, End-2-End execution completed =====================
    ```

    **说明：** 对同一套区块链网络，CLI 示例和应用程序示例无法同时运行，请为每一套区块链网络仅选择一种类型的示例运行。


