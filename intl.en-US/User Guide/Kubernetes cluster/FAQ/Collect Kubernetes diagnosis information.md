# Collect Kubernetes diagnosis information {#concept_vtq_bvb_wdb .concept}

1.  Download diagnosis script on the master node and add the operation permission.

    ```
    curl -o /usr/local/bin/diagnose_k8s.sh http://aliacs-k8s-cn-hangzhou.oss-cn-hangzhou.aliyuncs.com/public/diagnose/diagnose_k8s.sh
     chmod u+x /usr/local/bin/diagnose_k8s.sh
    ```

2.  Run the diagnosis script.

    ```
    diagnose_k8s.sh
     
     + echo 'please get diagnose_1514939155.tar.gz for diagnostics' ##The generated log file name is different every time you run the diagnosis script.
     please get diagnose_1514939155.tar.gz for diagnostics
     + echo 'Upload diagnose_1514939155.tar.gz'
     Upload diagnose_1514939155.tar.gz
    ```

3.  Upload the generated logs.

    ```
    cd /usr/local/bin
     ls -ltr|grep diagnose_1514939155.tar.gz ##Replace with the generated log file name.
    ```


