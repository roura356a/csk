# Access blockchain network by using CLI {#task_zg2_cpz_vdb .task}

The blockchain solution creates CLI containers during the blockchain network deployment. CLI containers connect to the Peer and Orderer nodes of the blockchain network in command line mode, run CLI commands supported by Hyperledger Fabric, and satisfy the requirements for testing and managing the blockchain network.

To log on to a CLI container, run the kubectl exec -it <fabricNetwork\>-fabric-cli -n <namespace name\> bash command in the environment that supports kubectl commands. The CLI container provides a standard end-to-end CLI test script based on Hyperledger Fabric. To modify the test script, find the file in the path /data/fabric/<blockchain network name\>/cli/cli-test.sh on any Elastic Compute Service \(ECS\) instance.

1.  Use SSH to log on to a master node of the Kubernetes cluster as the root account. For how to obtain the address, see [Environment preparations](intl.en-US/solution/Blockchain solution/Environment preparations.md#). You can also access an environment that supports managing Kubernetes clusters remotely by using kubectl. 
2.  Run the kubectl exec -it -n <namespace name\> <fabricNetwork\>-fabric-cli bash command to enter the CLI container, for example, kubectl exec -it -n network01 <fabricNetwork\>-fabric-cli bash. 
3.   Run the test script ./cli-test.sh to start the CLI test. 
4.   After each step is complete, the test pauses so that you can view the execution process and result. The test continues after you press any key. After the test script is executed successfully, information similar to the following one is displayed: 

    ```
    Query Result: 90
     2017-11-12 09:22:31.452 UTC [main] main -> INFO 007 Exiting.....
     ===================== Query on PEER4 on channel 'bankchannel' is successful ===================== 
     Press any key to continue...
     ===================== All GOOD, End-2-End execution completed =====================
    ```

    **Note:** For the same blockchain network, CLI sample and application sample cannot run at the same time. Choose to run only one type of sample for each blockchain network.


