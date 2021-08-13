---
keyword: [Inclavare Containers, confidential containers, ACK-TEE]
---

# Deploy confidential containers in TEE-based ACK clusters

Inclavare Containers is the first open source container runtime intended for confidential computing in the industry. Inclavare Containers allows you to launch protected containers in a hardware-based Trusted Execution Environment \(TEE\) to prevent untrusted entities, such as cloud service providers \(CSPs\), from accessing sensitive data. In a TEE-based Container Service for Kubernetes \(ACK\) cluster, you can deploy confidential containers based on Inclavare Containers. This topic describes how to deploy and run confidential containers based on Inclavare Containers in a TEE-based ACK cluster.

-   [Create a managed Kubernetes cluster for confidential computing](/intl.en-US/User Guide for Kubernetes Clusters/TEE-based confidential computing/Create a managed Kubernetes cluster for confidential computing.md).

    **Note:** Only Elastic Compute Service \(ECS\) instances of the **c7t security-enhanced compute optimized** **instance family** support TEE-based ACK clusters. Make sure that these instance types are available in the selected zone.

-   [Connect to an ACK cluster by using kubectl](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to ACK clusters by using kubectl.md).

You can use conventional methods to deploy confidential containers in TEE-based ACK clusters. However, technology expertise in confidential computing is required and you must use Intel Software Guard Extensions \(SGX\) SDK to develop and build images. Inclavare Containers helps you streamline the processes and provides you with easy access to confidential computing. Inclavare Containers is compatible with different types enclave runtime and provides a consistent user experience across standard and confidential containers. For more information, see [inclavare-containers](https://github.com/alibaba/inclavare-containers).

## Step 1: Build a runtime environment to run a confidential container

**Note:** In Kubernetes, pods are scheduled to nodes at random. When you deploy confidential containers in a pod, if you do not use labels to specify the node to which you want to schedule the pod, you must build a runtime environment for confidential containers on all nodes.

1.  Run the following command to install `rune` and `shim-rune`: For more information, see [rune](https://github.com/alibaba/inclavare-containers/tree/master/rune) and [shim-rune](https://github.com/alibaba/inclavare-containers/tree/master/shim).

    **Note:**

    -   `rune` is a command-line tool that conforms to the Open Container Initiative \(OCI\) runtime specification. rune is used to create and run confidential containers. For more information, see [runtime-container](https://github.com/opencontainers/runtime-spec/blob/master/implementations.md#runtime-container).
    -   `shim-rune` is a `shim` for `rune`. shim-rune also provides enclave signing and signature management in addition to the basic features of `shim`.
    ```
    yum-config-manager --add-repo https://mirrors.openanolis.org/inclavare-containers/alinux2-repo && \
      rpm --import https://mirrors.openanolis.org/inclavare-containers/alinux2-repo/RPM-GPG-KEY-rpm-sign && \
      yum install -y rune shim-rune
    ```

2.  Configure the container engine containerd.

    **Note:** In TEE-based ACK clusters, the default OCI runtime supported by containerd is runC. However, you must use a new container runtime runE to run confidential containers.

    1.  Run the following command to configure the OCI runtime runE:

        ```
        cd /etc/containerd/ && \
          sed -i 's/\(default_runtime_name = \)"runc"/\1"rune"/' config.toml && \
          sed -i '/\[plugins."io.containerd.grpc.v1.cri".containerd.runtimes\]/a\\t[plugins."io.containerd.grpc.v1.cri".containerd.runtimes.rune]' config.toml && \
          sed -i '/\[plugins."io.containerd.grpc.v1.cri".containerd.runtimes.rune\]/a\\t  runtime_type = "io.containerd.rune.v2"' config.toml
        ```

    2.  Run the following command to restart containerd:

        ```
        systemctl restart containerd.service
        ```

    3.  Run the following command to check whether containerd is started:

        ```
        systemctl status containerd.service
        ```

        Expected output:

        ```
        containerd.service - containerd container runtime
           Loaded: loaded (/usr/lib/systemd/system/containerd.service; enabled; vendor preset: disabled)
           Active: active (running) since Tuesday 2021-06-15 19:16:19 CST; 5s ago
             Docs: https://containerd.io
          Process: 212462 ExecStartPre=/sbin/modprobe overlay (code=exited, status=0/SUCCESS)
         Main PID: 212464 (containerd)
            Tasks: 288
           Memory: 6.3G
           ...
        ```

3.  Run the following command to install the Occlum software stack:

    **Note:** Occlum is an enclave runtime that is supported by Inclavare Containers. Inclavare Containers must work with an enclave runtime to run confidential containers.

    ```
    yum install -y occlum-pal occlum-rdfsbase-dkms
    ```

4.  Run the following command to check whether `occlum-pal` is installed:

    ```
    ls /opt/occlum/build/lib/
    ```

    Expected output:

    ```
    libocclum-pal.so.0.21.0
    ```

5.  Run the following command to check whether the `rdfsbase` driver is installed:

    ```
    lsmod | grep enable_rdfsbase
    ```

    Expected output:

    ```
    enable_rdfsbase        16384  0
    ```


## Step 2: Build an image for the confidential container

**Note:**

-   Substeps 1 to 5: Build and package the trusted application Hello World by using Occlum.
-   Substep 6: Build a container image that contains the trusted application Hello World.

1.  Run the following command to launch the Occlum development environment `occlum-app-builder`:

    **Note:** The image version that is specified in `docker.io/occlum/occlum` must be the same as the Occlum version that is installed.

    ```
    cat << EOF | kubectl apply -f -
    apiVersion: v1
    kind: Pod
    metadata:
      labels:
        run: occlum-app-builder
      name: occlum-app-builder
      namespace: default
    spec:
      hostNetwork: true
      containers:
      - command:
        - sleep
        - infinity
        image: docker.io/occlum/occlum:0.21.0-centos8.2
        imagePullPolicy: IfNotPresent
        securityContext:
          privileged: true
        name: occlum-app-builder
    EOF
    ```

2.  Run the following command to log on to the `occlum-app-builder` container in the Occlum development environment:

    ```
    kubectl exec -it occlum-app-builder -c occlum-app-builder -- /bin/bash
    ```

3.  Install Docker in the Occlum development environment `occlum-app-builder`.

    1.  Install Docker. For more information, see [Install Docker](https://docs.docker.com/engine/install/centos/).

    2.  After Docker is installed, run the following command to run Docker:

        ```
        nohup dockerd -b docker0 --storage-driver=vfs &
        ```

        **Note:** By default, Systemd is not installed in the container. Therefore, you cannot manage Docker by using Systemd. To start Docker, you must run `nohup dockerd -b docker0 --storage-driver=vfs &`.

    3.  Run the following command to verify that Docker runs as expected:

        ```
        docker ps
        ```

        Expected output:

        ```
        CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
        ```

4.  The following code block is an example of the Hello World application written in the C programming language:

    ```
    cat << EOF > hello_world.c
    #include <stdio.h>
    #include <unistd.h>
    
    void main(void)
    {
        while (1) {
            printf("Hello World!\n");
            fflush(stdout);
            sleep(5);
        }
    }
    EOF
    ```

5.  Build and package the trusted application Hello World.

    1.  Run the following command to compile the program with the Occlum toolchain:

        ```
        occlum-gcc -o hello_world hello_world.c
        ```

    2.  Run the following command to initialize `occlum_instance`:

        ```
        occlum new occlum_instance
        ```

    3.  Run the following command to generate a file system image of Occlum and Occlum SGX enclave:

        ```
        cd occlum_instance && \
            cp ../hello_world image/bin/ && \
            openssl genrsa -aes128 -out occlum_key.pem -3 3072 && \
            occlum build --sign-key occlum_key.pem
        ```

    4.  Run the following command to package the trusted application Hello World:

        ```
        occlum package occlum_instance.tar.gz
        ```

6.  Build a confidential container image that contains the trusted application Hello World.

    1.  Use the following Dockerfile template to create a file named Dockerfile:

        ```
        cat << EOF >Dockerfile
        FROM scratch
        ADD occlum_instance.tar.gz /
        ENTRYPOINT ["/bin/hello_world"]
        EOF
        ```

    2.  Run the following command to build and push the image:

        ```
        docker build -f Dockerfile -t "<$TARGET_IMAGE>" .
        docker login -p <$password> -u <$username>
        docker push "<$TARGET_IMAGE>"
        ```

        **Note:**

        -   The variables <$password\> and <$username\> separately specify the password and username that are used to log on to `Docker Hub`.
        -   The variable <$TARGET\_IMAGE\> specifies the image address.

## Step 3: Deploy the confidential container

1.  Exit from the Occlum development environment `occlum-app-builder`. Run the following command to create the following RuntimeClass objects: runC and runE. Then, you can deploy the confidential container in the TEE-based ACK cluster.

    ```
    cat << EOF | kubectl apply -f -
    apiVersion: node.k8s.io/v1beta1
    handler: runc
    kind: RuntimeClass
    metadata:
      name: runc
    ---
    apiVersion: node.k8s.io/v1beta1
    handler: rune
    kind: RuntimeClass
    metadata:
      name: rune
    EOF
    ```

2.  Run the following command to deploy the confidential container:

    ```
    cat << EOF | kubectl apply -f -
    apiVersion: v1
    kind: Pod
    metadata:
      labels:
        run: occlum-helloworld
      name:  occlum-helloworld
      namespace: default
    spec:
      restartPolicy: Always
      runtimeClassName: rune
      containers:
        - command:
          - /bin/hello_world
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
              value: occlum_instance
          image: <$TARGET_IMAGE>     
          imagePullPolicy: IfNotPresent
          name: hello-world-client
      dnsPolicy: ClusterFirst
    EOF
    ```

    The following table describes the parameters in the YAML file.

    |Parameter|Description|
    |---------|-----------|
    |**ENCLAVE\_TYPE**|Specifies the type of hardware used for confidential computing|
    |**RUNE\_CARRIER**|`shim-rune` creates and runs Occlum applications.|
    |**ENCLAVE\_RUNTIME\_LOGLEVEL**|Specifies the log level of the runtime. Valid values: trace, debug, info, warning, error, fatal, panic, and off.|
    |**ENCLAVE\_RUNTIME\_PATH**|Specifies the path to start the enclave runtime|
    |**ENCLAVE\_RUNTIME\_ARGS**|Specifies the parameters to start the enclave runtime|
    |**TARGET\_IMAGE**|The container image that is specified in Substep 6 of Step 2|


## Verify that the confidential container runs as normal in the TEE-based ACK cluster

Run the following command to check the operations log of the confidential container:

```
kubectl logs -f occlum-helloworld
```

Expected output:

```
Hello World!
Hello World!
Hello World!
```

If `Hello World!` is printed in the container log every 5 seconds, it indicates that the Inclavare Containers environment is installed and the confidential container runs as normal.

**Note:** If you have any questions about confidential containers, go to [issues](https://github.com/alibaba/inclavare-containers/issues) and leave a message.

