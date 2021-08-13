---
keyword: [Inclavare Containers, remote attestation, confidential containers]
---

# Use confidential containers to implement remote attestation in TEE-based ACK clusters

Inclavare Containers implements Enclave Attestation Architecture \(EAA\), which is a universal and cross-platform infrastructure that is used for remote attestation. EAA can prove that sensitive workloads run in a hardware-based Trusted Execution Environment \(TEE\). This topic describes how to implement remote attestation in a TEE-based Container Service for Kubernetes \(ACK\) cluster that runs Inclavare Containers.

-   [Create a managed Kubernetes cluster for confidential computing](/intl.en-US/User Guide for Kubernetes Clusters/TEE-based confidential computing/Create a managed Kubernetes cluster for confidential computing.md)

    **Note:** Only Elastic Compute Service \(ECS\) instances of the **c7t security-enhanced compute optimized** **instance family** support TEE-based ACK clusters. Make sure that these instance types are available in the selected zone. For more information, see [Instance families](/intl.en-US/Instance/Instance families.md).

-   An ECS instance that runs CentOS 8.2 or Ubuntu 18.04 is created. This instance is used to run the remote attestation tool Shelter. For more information, see [Create an ECS instance](/intl.en-US/Instance/Create an instance/Create an instance by using the wizard.md).
-   [Connect to an ACK cluster by using kubectl](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to ACK clusters by using kubectl.md).
-   A runtime environment for Inclavare Containers is deployed in all nodes of the TEE-based ACK cluster. For more information, see [Deploy confidential containers in TEE-based ACK clusters](/intl.en-US/User Guide for Kubernetes Clusters/TEE-based confidential computing/Deploy confidential containers in managed Kubernetes cluster for confidential computing.md).

EAA uses a Transport Layer Security \(TLS\) certificate in which a quote of a hardware-based TEE is embedded as the root of trust. This ensures that the server and the client communicate in a hardware-based TEE. The following figure shows the workflow and architecture of EAA.

![AKK](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/5300254261/p280617.png)

To check whether your workloads run on a trusted platform, you can start Shelter and send the request to Inclavared for verification. The following procedure shows how to check whether your workloads run on a trusted platform:

1.  After Inclavared receives the request from Shelter, Inclavared sends the request to a confidential container. Both Inclavared and the confidential container generate a TLS certificate in which a quote of a hardware-based TEE is embedded.
2.  An attested and secure channel is established between Inclavared and Shelter based on enclave-TLS.
3.  A mutually attested and secure channel is established between Inclavared and the confidential container based on enclave-TLS.
4.  Inclavared forwards the information about the hardware-based TEE and the sensitive information from the confidential container to Shelter.
5.  Shelter verifies the measurements of the enclave runtime and returns the result.

The following table describes the components of EAA.

|Component|Role|Description|
|---------|----|-----------|
|Confidential container|Workload|Runs the server program enclave-tls-server in Occlum. The confidential container also responds to the request from Inclavared based on enclave-TLS and returns the attestation evidence of the confidential container. The `attestation evidence` contains the values of `mrenclave` and `mrsigner`. For more information, see [Occlum](https://github.com/occlum/occlum) and [enclave-tls](https://github.com/alibaba/inclavare-containers/tree/master/enclave-tls).|
|Inclavared|Attester|Forwards the traffic between the downstream confidential container and the upstream Shelter client. The communication is protected by the attested enclave-TLS channel.|
|Shelter|On-premises verifier|1.  Records the launch measurements of the enclave runtime.
2.  Establishes the attested enclave-TLS channel to communicate with Inclavared.
3.  Shelter verifies the launch measurements of the enclave runtime. This way, you can verify that your workloads run in a genuine hardware-based TEE. |
|Alibaba Cloud Provisioning Certificate Caching Service \(PCCS\)|Remote attestation service|The Alibaba Cloud Software Guard Extensions \(SGX\) remote attestation service is fully compatible with the remote attestation service for Intel® SGX Elliptic Curve Digital Signature Algorithm \(ECDSA\) and Intel® SGX SDK. Therefore, the vSGX instances of Alibaba Cloud, which are instances that consist of the g7t, c7t, and r7t instance families, can gain trust from remote providers and producers by using remote attestation. For more information, see [Attestation Services for Intel® SGX ECDSA and Intel® SGX SDK](https://software.intel.com/content/www/us/en/develop/topics/software-guard-extensions/attestation-services.html).|

## Limits

-   Remote attestation is supported only for DaemonSets. This requires that only one DaemonSet runs on each node of the cluster.
-   To enable direct communication between Shelter and the workload, each node in the cluster must be associated with an elastic IP address \(EIP\). For more information, see [Step 4: Associate an EIP with an ECS instance](#section_aei_f51_uop).

## Step 1: Deploy Inclavared

1.  Run the following command to deploy an `inclavared` DaemonSet:

    ```
    cat <<-EOF | kubectl apply -f -
    apiVersion: apps/v1
    kind: DaemonSet
    metadata:
      name: inclavared
    spec:
      selector:
        matchLabels:
          k8s-app: inclavared
      template:
        metadata:
          labels:
            k8s-app: inclavared
        spec:
          affinity:
            nodeAffinity:
              requiredDuringSchedulingIgnoredDuringExecution:
                nodeSelectorTerms:
                - matchExpressions:
                  - key: alibabacloud.com/tee-hardware-category
                    operator: In
                    values:
                    - intel-sgx1
                    - intel-sgx2
          containers:
          - image: docker.io/inclavarecontainers/inclavared:latest
            imagePullPolicy: IfNotPresent
            securityContext:
              privileged: true
            name: inclavared
            command:
              - /usr/local/bin/inclavared
            args:
              - --listen
              - 0.0.0.0:1236
              - --xfer
              - 127.0.0.1:1234
              - --attester
              - sgx_ecdsa
              - --verifier 
              - sgx_ecdsa_qve
              - --mutual
            volumeMounts:
            - mountPath: /dev/sgx/enclave
              name: dev-enclave
            - mountPath: /dev/sgx/provision
              name: dev-provision
            - mountPath: /var/run/aesmd
              name: run-dir
            resources:
              requests:
                cpu: 100m
                memory: 100Mi
              limits:
                cpu: 1
                memory: 1000Mi
          tolerations:
          - effect: NoSchedule
            key: alibabacloud.com/sgx_epc_MiB
            operator: Exists
          volumes:
          - hostPath:
              path: /var/run/aesmd
              type: DirectoryOrCreate
            name: run-dir
          - hostPath:
              path: /dev/sgx_enclave
            name: dev-enclave
          - hostPath:
              path: /dev/sgx_provision
            name: dev-provision
          hostNetwork: true
    EOF
    ```

2.  Run the following command to check whether the inclavared DaemonSet is deployed:

    ```
    kubectl get daemonset inclavared
    ```

    Expected output:

    ```
    NAME         DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
    inclavared   2         2         2       2            2           <none>          7d22h
    ```

    **Note:** After Inclavared is started, Inclavared listens on `0.0.0.0:1236` for the requests from Shelter and forwards the requests to the attestation workloads on the node.


## Step 2: Deploy the occlum-attestation-app workload

1.  Run the following command to deploy an `occlum-attestation-app` DaemonSet:

    ```
    cat <<-EOF | kubectl apply -f -
    apiVersion: apps/v1
    kind: DaemonSet
    metadata:
      name: occlum-attestation-app
    spec:
      selector:
        matchLabels:
          k8s-app: occlum-attestation-app
      template:
        metadata:
          labels:
            k8s-app: occlum-attestation-app
        spec:
          affinity:
            nodeAffinity:
              requiredDuringSchedulingIgnoredDuringExecution:
                nodeSelectorTerms:
                - matchExpressions:
                  - key: alibabacloud.com/tee-hardware-category
                    operator: In
                    values:
                    - intel-sgx1
                    - intel-sgx2
          containers:
          - image: docker.io/inclavarecontainers/occlum-ecdsa-server:0.21.0
            imagePullPolicy: IfNotPresent
            securityContext:
              privileged: true
            name: occlum-attestation-app
            command:
              - /bin/enclave-tls-server
            args:
              - --ip
              - "127.0.0.1"    # The IP address of your on-premises machine. 
              - --port
              - "1234"
              - --mutual
            env:
              - name: ENCLAVE_TYPE
                value: intelSgx
              - name: RUNE_CARRIER
                value: occlum
              - name: ENCLAVE_RUNTIME_LOGLEVEL
                value: info
              - name: ENCLAVE_RUNTIME_PATH
                value: /opt/occlum/build/lib/libocclum-pal.so.0.21.0
              - name: ENCLAVE_RUNTIME_ARGS
                value: occlum_workspace_server
              - name: OCCLUM_RELEASE_ENCLAVE
                value: "1"
            workingDir: /run/rune
            resources:
              requests:
                cpu: 100m
                memory: 100Mi
              limits:
                cpu: 1
                memory: 1000Mi
          tolerations:
          - effect: NoSchedule
            key: alibabacloud.com/sgx_epc_MiB
            operator: Exists
          hostNetwork: true
    EOF
    ```

2.  Run the following command to check whether the `occlum-attestation-app` workload is deployed:

    ```
    kubectl get daemonset occlum-attestation-app
    ```

    Expected output:

    ```
    NAME                     DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
    occlum-attestation-app   2         2         2       2            2           <none>          7d22h
    ```

    The preceding output indicates that the `occlum-attestation-app` workload is deployed. The `enclave-tls-server` program in the workload listens on `127.0.0.1:1234` for the requests from Inclavared.


## Step 3: Modify the security group settings of the cluster

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  On the details page of the cluster, click the **Cluster Resources** tab and click the hyperlink on the right side of Security Group.

5.  Modify the default security group of the cluster and allow inbound traffic through port `1236`. For more information, see [Modify security group rules](/intl.en-US/Security/Security groups/Manage security group rules/Modify security group rules.md).


## Step 4: Associate an EIP with an ECS instance

**Note:** If the ECS instance is associated with an EIP, skip this step.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Nodes** \> **Nodes**.

5.  On the **Nodes** page, find the node that you want to manage and click the **Instance ID** of the node.

6.  In the ECS console, choose **Instances & Images** \> **Instances**. On the page that appears, click the **Instance Details** tab.

7.  Click **Bind EIP** and associate an EIP with the ECS instance.

    ![akk1](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/5519344261/p280664.png)

    -   If you have created EIPs, select an EIP from the **EIP** drop-down list in the **Bind EIP** dialog box.
    -   If no EIPs are available, click **Create EIP** in the **Bind EIP** dialog box. For more information, see [Apply for EIPs](/intl.en-US/User Guide/Apply for EIPs.md).

        ![jm11](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/5519344261/p283199.png)

8.  Click **OK**.


## Step 5: Install Shelter for remote attestation

1.  Install the SGX Platform Software \(PSW\).

    **Note:** Shelter must rely on the dynamic library provided by the SGX PSW to verify the TLS certificate in which information about the SGX is embedded. For more information, see [SGX PSW](https://download.01.org/intel-sgx/sgx-linux/2.13/docs/Intel_SGX_Installation_Guide_Linux_2.13_Open_Source.pdf).

    -   If you use CentOS 8.2, run the following command to install the SGX PSW:

        ```
        yum install -y yum-utils && \
            wget -c https://download.01.org/intel-sgx/sgx-linux/2.13/distro/centos8.2-server/sgx_rpm_local_repo.tgz && \
            tar xzf sgx_rpm_local_repo.tgz && \
            yum-config-manager --add-repo sgx_rpm_local_repo && \
            yum makecache && rm -f sgx_rpm_local_repo.tgz && \
            yum install --nogpgcheck -y libsgx-dcap-quote-verify \
            libsgx-dcap-default-qpl libsgx-dcap-ql \
            libsgx-uae-service
        ```

    -   If you use Ubuntu 18.04, run the following command to install the SGX PSW:

        ```
        apt-get update -y && apt-get install -y wget gnupg && \
            echo "deb [arch=amd64] https://download.01.org/intel-sgx/sgx_repo/ubuntu bionic main" | tee /etc/apt/sources.list.d/intel-sgx.list && \
            wget -qO - https://download.01.org/intel-sgx/sgx_repo/ubuntu/intel-sgx-deb.key | apt-key add - && \
            apt-get update -y && \
            apt-get install -y libsgx-dcap-quote-verify=1.10.100.4-bionic1 \
            libsgx-dcap-default-qpl=1.10.100.4-bionic1 \
            libsgx-dcap-ql=1.10.103.1-bionic1 \
            libsgx-uae-service=2.13.100.4-bionic1
        ```

2.  Configure the public endpoint of Alibaba Cloud PCCS.

    The Alibaba Cloud SGX remote attestation service is regionally deployed. For optimal stability, you can access this service in the region where the vSGX instance is deployed. You must manually modify the /etc/sgx\_default\_qcnl.conf file to adapt to the Alibaba Cloud SGX remote attestation service that is deployed in the region where the vSGX instance is deployed.

    **Note:** Only regions in mainland China support the Alibaba Cloud SGX remote attestation service. For more information, see [Regions and zones]().

    -   If a public IP address is assigned to the vSGX instance, you must modify /etc/sgx\_default\_qcnl.conf based on the following content:

        ```
        # PCCS server address
        PCCS_URL=https://sgx-dcap-server.<Region-ID>.aliyuncs.com/sgx/certification/v3/
        # To accept insecure HTTPS cert, set this option to FALSE
        USE_SECURE_CERT=TRUE
        ```

    -   If the vSGX instance is in a virtual private cloud \(VPC\) and has only an internal IP address, you must modify /etc/sgx\_default\_qcnl.conf based on the following content:

        ```
        # PCCS server address
        PCCS_URL=https://sgx-dcap-server-vpc.<Region-ID>.aliyuncs.com/sgx/certification/v3/
        # To accept insecure HTTPS cert, set this option to FALSE
        USE_SECURE_CERT=TRUE
        ```

        **Note:** You must replace <Region-ID\> with the ID of the region where the vSGX instance is deployed.

3.  Install Shelter for remote attestation.

    -   If you use CentOS 8.2, run the following command to install Shelter:

        ```
        yum-config-manager --add-repo https://mirrors.openanolis.org/inclavare-containers/rpm-repo/ && \
            rpm --import https://mirrors.openanolis.org/inclavare-containers/rpm-repo/RPM-GPG-KEY-rpm-sign && \
            yum install -y shelter
        ```

    -   If you use Ubuntu 18.04, run the following command to install Shelter:

        ```
        echo 'deb [arch=amd64] https://mirrors.openanolis.org/inclavare-containers/deb-repo bionic main' | tee /etc/apt/sources.list.d/inclavare-containers.list && \
            wget -qO - https://mirrors.openanolis.org/inclavare-containers/deb-repo/DEB-GPG-KEY.key  |  apt-key add - && \
            apt-get update -y && apt-get install -y shelter
        ```

    Verify the result

    Run the following command to check whether Shelter is installed:

    ```
    which shelter
    ```

    Expected output:

    ```
    /usr/local/bin/shelter
    ```


## Verify that remote attestation is implemented in the TEE-based ACK cluster that runs Inclavare Containers.

Run the following command on each node in the cluster to launch Shelter:

**Note:** Before you run the command, replace <$IP\> with the EIP associated to the worker node.

```
shelter remoteattestation --verifier sgx_ecdsa --tls openssl --crypto openssl --addr=tcp://<$IP>:1236
```

-   If Shelter runs in an SGX environment, the following output is returned:

    ![jm67](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/9411465261/p282846.png)

    If `Remote attestation is successful` appears in the output, it indicates that remote attestation is implemented.

-   If Shelter runs in a non-SGX environment, the following errors are returned together with the `Remote attestation is successful` message:

    ```
    [load_qve ../sgx_dcap_quoteverify.cpp:209] Error, call sgx_create_enclave for QvE fail [load_qve], SGXError:2006.
    [sgx_qv_get_quote_supplemental_data_size ../sgx_dcap_quoteverify.cpp:527] Error, failed to load QvE.
    ```

    The preceding errors have no impact on the remote attestation result. You can ignore the errors.

    The errors are returned because Shelter attempts to load Quote Verification Enclave \(QVE\) when Shelter calls [sgx\_qv\_get\_quote\_supplemental\_data\_size\(\)](https://download.01.org/intel-sgx/sgx-dcap/1.3/linux/docs/Intel_SGX_ECDSA_QuoteLibReference_DCAP_API.pdf). The preceding errors are returned when QVE fails to be loaded. QVE cannot be loaded in non-SGX environments. In this case, Shelter uses Quote Verification Library \(QVL\) to continue the attestation. In non-SGX environments, the attestation is handled by QVL. For more information, see [Attestation services for Intel® SGX ECDSA](https://api.portal.trustedservices.intel.com/provisioning-certification).


