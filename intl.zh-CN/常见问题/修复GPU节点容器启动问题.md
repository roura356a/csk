# 修复GPU节点容器启动问题 {#concept_943909 .concept}

## 问题描述 {#section_a52_jt7_yj4 .section}

在某些特定 Kubernetes 版本中的GPU节点上，重启Kubelet和Docker时，发现没有容器被启动。

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

## 问题定位 {#section_yrl_7r8_l3p .section}

执行如下命令，查看Docker 的 Cgroup Driver。

``` {#codeblock_tol_6ic_iw4}
docker info | grep -i cgroup
Cgroup Driver: cgroupfs
```

此时发现的Cgroup Driver类型是cgroupfs。

## 解决方案 {#section_83g_qmh_cis .section}

1.  备份/etc/docker/daemon.json，完成后，执行如下命令更新/etc/docker/daemon.json。

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

2.  执行如下命令，重启 Docker 和 Kubelet。

    ``` {#codeblock_sn4_33t_4o9}
    # service kubelet stop
    Redirecting to /bin/systemctl stop kubelet.service
    # service docker restart
    Redirecting to /bin/systemctl restart docker.service
    # service kubelet start
    Redirecting to /bin/systemctl start kubelet.service
    ```

3.  执行如下命令，确认Docker 的 Cgroup Driver 的类型为 systemd。

    ``` {#codeblock_rxx_lq7_auy}
    # docker info | grep -i cgroup
    Cgroup Driver: systemd
    ```


