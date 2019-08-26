# Failed to start a container on the GPU node {#concept_943909 .concept}

## Symptom {#section_a52_jt7_yj4 .section}

When a container fails to start after you restart your Kubelet and Docker on the GPU node, the following codes are displayed:

``` {#codeblock_6y4_cmu_c9c}
# service kubelet stop
Redirecting to /bin/systemctl stop kubelet.service
# service docker stop
Redirecting to /bin/systemctl stop docker.service
# service docker start
Redirecting to /bin/systemctl start docker.service
# service kubelet start
Redirecting to /bin/systemctl start kubelet.service

# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```

## Cause {#section_yrl_7r8_l3p .section}

The type of `Cgroup Driver` of Docker is `cgroupfs` \(which is incorrect\).

**Note:** You can run the following command to check the type of `Cgroup Driver`:

``` {#codeblock_xre_ze9_piq}
docker info | grep -i cgroup
Cgroup Driver: cgroupfs
```

## Solution {#section_83g_qmh_cis .section}

1.  Back up the /etc/docker/daemon.json file and run the following command to update the file:

    ``` {#codeblock_qst_ua7_1mx}
    cat >/etc/docker/daemon.json <<-EOF
    {
        "default-runtime": "nvidia",
        "runtimes": {
            "nvidia": {
                "path": "/usr/bin/nvidia-container-runtime",
                "runtimeArgs": []
            }
        },
        "exec-opts": ["native.cgroupdriver=systemd"],
        "log-driver": "json-file",
        "log-opts": {
            "max-size": "100m",
            "max-file": "10"
        },
        "oom-score-adjust": -1000,
        "storage-driver": "overlay2",
        "storage-opts":["overlay2.override_kernel_check=true"],
        "live-restore": true
    }
    EOF
    ```

2.  Run the following command to restart Docker and Kubelet:

    ``` {#codeblock_sn4_33t_4o9}
    # service kubelet stop
    Redirecting to /bin/systemctl stop kubelet.service
    # service docker restart
    Redirecting to /bin/systemctl restart docker.service
    # service kubelet start
    Redirecting to /bin/systemctl start kubelet.service
    ```

3.  Run the following command to verify that the type of `Cgroup Driver` is `systemd`.

    ``` {#codeblock_rxx_lq7_auy}
    # docker info | grep -i cgroup
    Cgroup Driver: systemd
    ```


