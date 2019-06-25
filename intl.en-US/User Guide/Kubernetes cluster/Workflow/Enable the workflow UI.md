# Enable the workflow UI {#concept_270146 .concept}

This topic describes how to enable and access the workflow UI by creating an Ingress. By using the workflow UI, you can view the status of all workflows, and the container logs of each step of a workflow.

## Prerequisites {#section_z8h_j6q_zi8 .section}

-   A Kubernetes cluster is created. For more information, see [Create a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).
-   The Master node of the Kubernetes cluster is accessible. For more information, see [Connect to a Kubernetes cluster by using kubectl](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Connect to a Kubernetes cluster by using kubectl.md#).

## Procedure {#section_9is_w2g_zmd .section}

1.  Run an htpasswd command to generate the file auth.

    **Note:** In this file, you can set the password used to access the workflow UI.

    ``` {#codeblock_y3v_blp_hrs}
    $ htpasswd -c auth workflow
    New password: <workflow>
    New password:
    Re-type new password:
    Adding password for user workflow
    ```

2.  Run the following command to create a Secret that is used to store the auth file in the target Kubernetes cluster.

    ``` {#codeblock_rhs_n14_w8w}
    $ kubectl create secret generic workflow-basic-auth --from-file=auth -n argo
    ```

3.  Create the file ingress yaml, and then copy the following code to the file:

    ``` {#codeblock_fky_sh8_x65}
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

    **Note:** You must replace `<yourTestHost>` with your cluster address \(That is, the value of Testing Domain in the **Cluster Information** area. For example, cfb131.cn-zhangjiakou.alicontainer.com\).

4.  Run the `kubectl apply -f ingress.yaml` command to create an Ingress named workflow-ingress.
5.  In your browser, enter workflow.<yourTestHost\>, and then enter the password to view the page of workflow UI.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/222974/156143261947755_en-US.png)

6.  View the status of the target workflow.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/222974/156143261947756_en-US.png)


