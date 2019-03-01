# How do I collect Kubernetes diagnosis information when a Kubernetes cluster exception or a cluster node exception occurs? {#task_ncj_ryg_ygb .task}

If exceptions occur to the Kubernetes cluster, you need to collect diagnosis information on the Master node.

If exceptions occur to Worker nodes, you need to collect diagnosis information on the Master node and the abnormal Worker nodes.

1.   Download the diagnosis script on the Master and Worker nodes, and add the permission to run the script. 

    ```
    curl -o /usr/local/bin/diagnose_k8s.sh http://aliacs-k8s-cn-hangzhou.oss-cn-hangzhou.aliyuncs.com/public/diagnose/diagnose_k8s.sh
    chmod u+x /usr/local/bin/diagnose_k8s.sh
    ```

2.   Run the diagnosis script. 

    ```
    diagnose_k8s.sh
     ......
     + echo 'please get diagnose_1514939155.tar.gz for diagnostics'   ##The name of a generated log file varies each time when you run the diagnosis script.
     please get diagnose_1514939155.tar.gz for diagnostics
     + echo 'upload diagnose_1514939155.tar.gz'
     Upload the diagnose_1514939155.tar.gz file.
    ```

3.   List and upload the generated log file. 

    ```
    cd /usr/local/bin
    ls -ltr|grep diagnose_1514939155.tar.gz           ##Replace this example file name with the name of your generated log file.
    ```


