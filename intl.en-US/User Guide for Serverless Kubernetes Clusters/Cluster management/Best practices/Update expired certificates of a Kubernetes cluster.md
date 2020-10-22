# Update expired certificates of a Kubernetes cluster

When cluster certificates expire, communication with the cluster API server by using kubectl or calling APIs is disabled, and the expired certificates on cluster nodes cannot be updated automatically through template deployment. To update the certificates, you can log on to each cluster node and run the container stating commands, `docker run`.

## Update the expired certificates on a Master node

1.  Log on to a Master node with the root permission.
2.  Run the following command in any directory to update the expired certificates on the Master node:

    ```
    $ docker run -it --privileged=true  -v /:/alicoud-k8s-host --pid host --net host \
      registry.cn-hangzhou.aliyuncs.com/acs/cert-rotate:v1.0.0 /renew/upgrade-k8s.sh --role master
    ```

3.  Repeat the preceding steps on each cluster Master node to update all the expired certificates.

## Update the expired certificates on a Worker node

1.  Log on to a Master node with the root permission.
2.  Run the following command to obtain the cluster rootCA private key:

    ```
    $ cat /etc/kubernetes/pki/ca.key
    ```

3.  Run either of the following commands to obtain the cluster root private key encoded through base64:
    -   If the cluster rootCA private key has a blank line, run the following command:

        ```
        $ sed '1d' /etc/kubernetes/pki/ca.key| base64 -w 0
        ```

    -   If the cluster rootCA private key does not have any blank line, run the following command:

        ```
        $ cat /etc/kubernetes/pki/ca.key | base64 -w 0
        ```

4.  Log on to a Worker node with the root permission.
5.  Run the following command in any directory to update the expired certificates on the Worker node.

    ```
    $ docker run -it --privileged=true  -v /:/alicoud-k8s-host --pid host --net host \
      registry.cn-hangzhou.aliyuncs.com/acs/cert-rotate:v1.0.0 /renew/upgrade-k8s.sh --role node --rootkey ${base64CAKey}
    ```

    **Note:** In step 3, you have obtained $\{base64CAKey\}, which is the cluster root private key encoded through base64.

6.  Repeat the preceding steps on each cluster Worker node to update all the expired certificates.

