# 收集 Kubernetes 诊断信息 {#concept_vtq_bvb_wdb .concept}

1.  在 master 节点下载诊断脚本，并增加运行权限。

    ```
    curl -o /usr/local/bin/diagnose_k8s.sh http://aliacs-k8s-cn-hangzhou.oss-cn-hangzhou.aliyuncs.com/public/diagnose/diagnose_k8s.sh
    chmod u+x /usr/local/bin/diagnose_k8s.sh
    ```

2.  执行诊断脚本。

    ```
    diagnose_k8s.sh
     ......
     + echo 'please get diagnose_1514939155.tar.gz for diagnostics'   ##每次执行诊断脚本，产生的日志文件的名称不同
     please get diagnose_1514939155.tar.gz for diagnostics
     + echo '请上传 diagnose_1514939155.tar.gz'
     请上传 diagnose_1514939155.tar.gz
    ```

3.  列出产生的日志文件。

    ```
    cd /usr/local/bin
    ls -ltr|grep diagnose_1514939155.tar.gz           ##注意替换为生成的日志文件名
    ```


