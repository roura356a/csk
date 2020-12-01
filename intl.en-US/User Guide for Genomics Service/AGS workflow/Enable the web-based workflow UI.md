# Enable the web-based workflow UI

This topic describes how to enable and access the web-based workflow UI by creating an Ingress. You can view the status of all workflows and the container logs for each step of a workflow by using the web-based workflow UI.

-   A Container Service for Kubernetes \(ACK\) cluster is created. For more information, see [Create a managed kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed kubernetes cluster.md).
-   A master node is connected. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md).

1.  Run the htpasswd command to generate an auth file. You can store the username and password in the file.

    Run the following command:

    ```
    htpasswd -c auth workflow
    ```

    The following output is returned:

    ```
    New password: <workflow>
    New password:
    Re-type new password:
    Adding password for user workflow
    ```

2.  Run the following command to create a Secret and store the encrypted file in the ACK cluster:

    ```
    kubectl create secret generic workflow-basic-auth --from-file=auth -n argo
    ```

3.  Create an ingress yaml file, copy the following content to the file, and then run the `kubectl apply -f ingress.yaml` command to create the Ingress named workflow-ingress.

    ```
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: workflow-ingress
      namespace: argo
      annotations:
        # type of authentication
        nginx.ingress.kubernetes.io/auth-type: basic
        # name of the secret that contains the user/password definitions
        nginx.ingress.kubernetes.io/auth-secret: workflow-basic-auth
        # message to display with an appropriate context why the authentication is required
        nginx.ingress.kubernetes.io/auth-realm: 'Authentication Required - workflow'
    spec:
      rules:
      - host: workflow.<yourTestHost>
        http:
          paths:
          - path: /
            backend:
              serviceName: argo-ui
              servicePort: 80
    ```

    **Note:** In the preceding code block, the value of `host` must be replaced with the value of Testing Domain in the **Cluster Information** section of the ACK cluster. For example, the value can be set to workflow.cfb131.cn-zhangjiakou.alicontainer.com.

4.  Open your browser, enter workflow.<yourTestHost\> into the address bar, and then provide the required password to view the web-based workflow UI.

    ![Enable the web-based workflow UI](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7386858951/p47755.png)

    You can view the status of the workflow based on your business requirements.

    ![View the workload status](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8386858951/p47756.png)


