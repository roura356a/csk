# Update expired certificates of a Kubernetes cluster

If the certificates that are used on the nodes of a Kubernetes cluster expire, you cannot communicate with the cluster API server by using kubectl or calling API operations. The expired certificates cannot be automatically updated based on template deployment. To update the certificates, you can log on to each node and run the `docker run` command.

## Update the expired certificate on each master node

1.  Log on to a master node as the root user.

2.  In the command-line interface \(CLI\), run the following command in any directory. This allows you to update the expired certificate on the master node:

    ```
    docker run -it --privileged=true  -v /:/alicoud-k8s-host --pid host --net host \
      registry.cn-hangzhou.aliyuncs.com/acs/cert-rotate:v1.0.0 /renew/upgrade-k8s.sh --role master
    ```

3.  Repeat the preceding steps on each cluster master node to update all the expired certificates.


## Update the expired certificate on a worker node

1.  Log on to a master node as the root user.

2.  In the CLI, run the following command to obtain the cluster rootCA private key:

    ```
    cat /etc/kubernetes/pki/ca.key
    ```

3.  Run either of the following commands to obtain the cluster root private key that is encoded in the Base64 format:

    -   If the cluster rootCA private key contains a blank line, run the following command:

        ```
        sed '1d' /etc/kubernetes/pki/ca.key| base64 -w 0
        ```

    -   If the cluster rootCA private key does not contain a blank line, run the following command:

        ```
        cat /etc/kubernetes/pki/ca.key | base64 -w 0
        ```

4.  Log on to a worker node as the root user.

5.  In a directory on the worker node, run the following command to update the expired certificate on the worker node.

    ```
    docker run -it --privileged=true  -v /:/alicoud-k8s-host --pid host --net host \
      registry.cn-hangzhou.aliyuncs.com/acs/cert-rotate:v1.0.0 /renew/upgrade-k8s.sh --role node --rootkey ${base64CAKey}
    ```

    **Note:** $\{base64CAKey\} specifies the cluster root private key that is encoded in the Base64 format. The value of $\{base64CAKey\} is returned in Step 3.

6.  Repeat the preceding steps on each worker node of the cluster to update all the expired certificates.


