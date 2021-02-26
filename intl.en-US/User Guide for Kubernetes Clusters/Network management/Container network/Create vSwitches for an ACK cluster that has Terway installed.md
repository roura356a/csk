---
keyword: [vSwitch, Terway, create vSwitches]
---

# Create vSwitches for an ACK cluster that has Terway installed

When the IP addresses provided by a vSwitch in a Container Service for Kubernetes \(ACK\) cluster are exhausted, you must create vSwitches in the virtual private cloud \(VPC\) where the cluster is deployed. This topic describes how to expand the IP address space of an ACK cluster by creating vSwitches.

## Symptoms of insufficient IP addresses

In a cluster that has Terway installed, the following symptoms indicate that the IP addresses provided by the vSwitch in the cluster are exhausted:

-   You fail to create pods. The pod that you want to create stays in the ContainerCreating state. In this case, run the following command to query the Terway log on the node where the pod is deployed:

    ```
    kubectl logs --tail=100 -f terway-eniip-zwjwx -n kube-system -c terway
    ```

    If an error message similar to the following content is returned, it indicates that the IP addresses of the vSwitch are exhausted. The pod cannot be created and stays in the ContainerCreating state because no IP address is available.

    ```
    time="2020-03-17T07:03:40Z" level=warning msg="Assign private ip address failed: Aliyun API Error: RequestId: 2095E971-E473-4BA0-853F-0C41CF52651D Status Code: 403 Code: InvalidVSwitchId.IpNotEnough Message: The specified VSwitch \"vsw-AAA\" has not enough IpAddress., retrying"
    ```

-   Log on to the [VPC console](https://vpc.console.aliyun.com/). In the left-side navigation pane, click **VSwitches**. On the VSwitches page, find the vSwitch and verify that 0 is displayed in the **Number of Available Private IPs** column.

## Create a vSwitch

To create a vSwitch, perform the following steps:

1.  Log on to the [VPC console](https://vpc.console.aliyun.com/) and create a vSwitch. You must create the vSwitch in the same region as the preceding one whose IP addresses are exhausted. For more information about how to create a vSwitch, see [Create a vSwitch](/intl.en-US/VPCs and vSwitchs/Create a VSwitch.md).

    **Note:** To provide sufficient IP addresses for an increasing number of pods in the ACK cluster, we recommend that the CIDR block of the vSwitch contain at least 8,192 IP addresses. This means that the network bits of the CIDR block must be no greater than 19 in length.

2.  Run the following command to add the newly created vSwitch to the ConfigMap of Terway:

    ```
    kubectl edit cm eni-config -n kube-system
    ```

    The following code provides an example on how to configure the ConfigMap:

    ```
    eni_conf: |
    {
    "version": "1",
    "max_pool_size": 25,
    "min_pool_size": 10,
    "vswitches": {"cn-shanghai-f":["vsw-AAA", "vsw-BBB"]},
    "service_cidr": "172.21.0.0/20",
    "security_group": "sg-CCC"
    }
    ```

    In this example, `vsw-BBB` is added to the value of `vswitches`. `vsw-AAA` represents the existing vSwitch that has insufficient IP addresses.

3.  Upgrade Terway.

    Upgrade Terway to the latest version. If Terway is already upgraded to the latest version, skip this step.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane of the ACK console, click **Clusters**.

    3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster, or click **Details** in the **Actions** column. The details page of the cluster appears.

    4.  In the left-side navigation pane, choose **Operations** \> **Add-ons**. On the Add-ons page, click the **Networking** tab.

    5.  On the Networking tab, find the Terway plug-in and click **Upgrade**.

4.  Run the following commands to delete all pods that use Terway. ACK automatically recreates the pods.

    -   If an elastic network interface \(ENI\) is shared among multiple pods, run the following command:

        ```
        kubectl delete -n kube-system pod -l app=terway-eniip
        ```

    -   If an ENI is exclusive to one pod, run the following command:

        ```
        kubectl delete -n kube-system pod -l app=terway-eni
        ```

    Run the following command to check whether all pods are recreated:

    ```
    kubectl get pod -n kube-system  | grep terway
    ```

5.  After the preceding steps are completed, you can create a pod to check whether the pod is assigned an IP address from the newly created vSwitch.


If exceptions occur in the preceding steps, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

**Related topics**  


[Use Terway](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Container network/Use Terway.md)

[Use network policies for access control](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Container network/Use network policies for access control.md)

