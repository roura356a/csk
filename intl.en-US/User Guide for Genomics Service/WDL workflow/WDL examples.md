# WDL examples

This topic provides examples about how to use Workflow Description Language \(WDL\).

## Query job list

Run the following command to query a list of jobs for the WDL workflow:

```
ags wdl list
```

Expected output:

```
+----------+--------------------------+--------------------------------------+------------+
| JOB NAME |       CREATE TIME        |                JOB ID                | JOB STATUS |
+----------+--------------------------+--------------------------------------+------------+
| wf       | 2020-10-21T06:34:10.859Z | 87546541-f41d-4745-9d1e-4b9b0f27b033 | Running    |
| wf       | 2020-10-21T05:52:28.360Z | cc195aee-41c7-44b0-89e5-4e94ba47f56b | Succeeded  |
| wf       | 2020-10-21T05:52:08.340Z | 7209675e-4277-4d68-8848-725abca6b143 | Succeeded  |
| wf       | 2020-10-21T03:50:01.010Z | 05ed1979-39d4-41db-8535-446919088a2b | Succeeded  |
| wf       | 2020-10-21T03:45:40.783Z | b2394c34-9086-441f-af49-02735b5710ed | Succeeded  |
+----------+--------------------------+--------------------------------------+------------+
```

## Query details about a job

Run the following command to query the detailed information about a job:

**Note:** The information includes the ID of the job and the path where error log is stored.

```
ags wdl query cc195aee-41c7-44b0-89e5-4e94ba47f56b
```

Expected output:

```
{
    "workflowName": "wf",
    "workflowProcessingEvents": [
        {
            "description": "Finished",
            "timestamp": "2020-10-21T05:53:05.109Z",
            "cromwellId": "cromid-89e8e7d",
            "cromwellVersion": "54-36be57c-SNAP"
        },
        {
            "timestamp": "2020-10-21T05:52:28.359Z",
            "cromwellVersion": "54-36be57c-SNAP",
            "cromwellId": "cromid-89e8e7d",
            "description": "PickedUp"
        }
    ],
    "metadataSource": "Unarchived",
    "actualWorkflowLanguageVersion": "draft-2",
    "submittedFiles": {
        "workflow": "task bwa_mem_tool {\n    String outputdir\n    String fastqFolder\n    String cpunum\n    String bamfilename\n    command {\n        cd ${fastqFolder}\n        cp gene.bam.bai gene.bam.bai.test\n    }\n    runtime {\n    docker: \"registry.cn-beijing.aliyuncs.com/shuangkun/genetool:v1.1\"\n    memory: \"2GB\"\n    cpu: 1\n    }\n  }\ntask agstask {\n    String config\n    command {\n        mkdir /root/.ags\n        cp ${config} /root/.ags/\n        ags remote list > /ags-wdl-nas/remote.txt\n    }\n    runtime {\n    docker: \"registry.cn-beijing.aliyuncs.com/shuangkun/genetool:v1.1\"\n    memory: \"2GB\"\n    cpu: 1\n    }\n  }\nworkflow wf {\n      call agstask\n      call bwa_mem_tool\n} \n",
        "root": "",
        "options": "{\n\n}",
        "inputs": "{\"wf.agstask.config\":\"/ags-wdl-nas/bwatest/ags/config\",\"wf.bwa_mem_tool.bamfilename\":\"gene.bam\",\"wf.bwa_mem_tool.cpunum\":\"6\",\"wf.bwa_mem_tool.fastqFolder\":\"/ags-wdl-nas/sample/MGISEQ2000_PCR-free_NA12878_1_V100003043_L01_1.fq.gz\",\"wf.bwa_mem_tool.outputdir\":\"/ags-wdl-nas/bwatest/output\"}",
        "workflowUrl": "",
        "labels": "{}"
    },
    "calls": {
        "wf.bwa_mem_tool": [
            {
                "executionStatus": "Done",
                "stdout": "/ags-wdl-nas/wf/cc195aee-41c7-44b0-89e5-4e94ba47f56b/call-bwa_mem_tool/execution/stdout",
                "backendStatus": "Complete",
                "commandLine": "cd /ags-wdl-nas/sample/MGISEQ2000_PCR-free_NA12878_1_V100003043_L01_1.fq.gz\ncp gene.bam.bai gene.bam.bai.test",
                "shardIndex": -1,
                "outputs": {},
                "runtimeAttributes": {
                    "preemptible": "false",
                    "failOnStderr": "false",
                    "continueOnReturnCode": "0",
                    "docker": "registry.cn-beijing.aliyuncs.com/shuangkun/genetool:v1.1",
                    "maxRetries": "0",
                    "cpu": "1",
                    "memory": "2 GB"
                },
                "callCaching": {
                    "allowResultReuse": false,
                    "effectiveCallCachingMode": "CallCachingOff"
                },
                "inputs": {
                    "fastqFolder": "/ags-wdl-nas/sample/MGISEQ2000_PCR-free_NA12878_1_V100003043_L01_1.fq.gz",
                    "outputdir": "/ags-wdl-nas/bwatest/output",
                    "cpunum": "6",
                    "bamfilename": "gene.bam"
                },
                "returnCode": 0,
                "jobId": "task-6dbeff7e",
                "backend": "TESK",
                "end": "2020-10-21T05:53:04.317Z",
                "dockerImageUsed": "registry.cn-beijing.aliyuncs.com/shuangkun/genetool:v1.1",
                "stderr": "/ags-wdl-nas/wf/cc195aee-41c7-44b0-89e5-4e94ba47f56b/call-bwa_mem_tool/execution/stderr",
                "callRoot": "/ags-wdl-nas/wf/cc195aee-41c7-44b0-89e5-4e94ba47f56b/call-bwa_mem_tool",
                "attempt": 1,
                "executionEvents": [
                    {
                        "startTime": "2020-10-21T05:53:03.351Z",
                        "description": "UpdatingJobStore",
                        "endTime": "2020-10-21T05:53:04.317Z"
                    },
                    {
                        "startTime": "2020-10-21T05:52:29.408Z",
                        "endTime": "2020-10-21T05:52:29.408Z",
                        "description": "Pending"
                    },
                    {
                        "startTime": "2020-10-21T05:52:29.970Z",
                        "endTime": "2020-10-21T05:53:03.351Z",
                        "description": "RunningJob"
                    },
                    {
                        "endTime": "2020-10-21T05:52:29.957Z",
                        "startTime": "2020-10-21T05:52:29.408Z",
                        "description": "RequestingExecutionToken"
                    },
                    {
                        "description": "WaitingForValueStore",
                        "startTime": "2020-10-21T05:52:29.957Z",
                        "endTime": "2020-10-21T05:52:29.957Z"
                    },
                    {
                        "startTime": "2020-10-21T05:52:29.957Z",
                        "endTime": "2020-10-21T05:52:29.970Z",
                        "description": "PreparingJob"
                    }
                ],
                "start": "2020-10-21T05:52:29.408Z"
            }
        ],
        "wf.agstask": [
            {
                "executionStatus": "Done",
                "stdout": "/ags-wdl-nas/wf/cc195aee-41c7-44b0-89e5-4e94ba47f56b/call-agstask/execution/stdout",
                "backendStatus": "Complete",
                "commandLine": "mkdir /root/.ags\ncp /ags-wdl-nas/bwatest/ags/config /root/.ags/\nags remote list > /ags-wdl-nas/remote.txt",
                "shardIndex": -1,
                "outputs": {},
                "runtimeAttributes": {
                    "preemptible": "false",
                    "failOnStderr": "false",
                    "continueOnReturnCode": "0",
                    "docker": "registry.cn-beijing.aliyuncs.com/shuangkun/genetool:v1.1",
                    "maxRetries": "0",
                    "cpu": "1",
                    "memory": "2 GB"
                },
                "callCaching": {
                    "allowResultReuse": false,
                    "effectiveCallCachingMode": "CallCachingOff"
                },
                "inputs": {
                    "config": "/ags-wdl-nas/bwatest/ags/config"
                },
                "returnCode": 0,
                "jobId": "task-1aa59269",
                "backend": "TESK",
                "end": "2020-10-21T05:53:03.297Z",
                "dockerImageUsed": "registry.cn-beijing.aliyuncs.com/shuangkun/genetool:v1.1",
                "stderr": "/ags-wdl-nas/wf/cc195aee-41c7-44b0-89e5-4e94ba47f56b/call-agstask/execution/stderr",
                "callRoot": "/ags-wdl-nas/wf/cc195aee-41c7-44b0-89e5-4e94ba47f56b/call-agstask",
                "attempt": 1,
                "executionEvents": [
                    {
                        "startTime": "2020-10-21T05:52:29.965Z",
                        "description": "RunningJob",
                        "endTime": "2020-10-21T05:53:02.773Z"
                    },
                    {
                        "description": "PreparingJob",
                        "startTime": "2020-10-21T05:52:29.957Z",
                        "endTime": "2020-10-21T05:52:29.965Z"
                    },
                    {
                        "description": "WaitingForValueStore",
                        "startTime": "2020-10-21T05:52:29.957Z",
                        "endTime": "2020-10-21T05:52:29.957Z"
                    },
                    {
                        "startTime": "2020-10-21T05:52:29.408Z",
                        "endTime": "2020-10-21T05:52:29.957Z",
                        "description": "RequestingExecutionToken"
                    },
                    {
                        "startTime": "2020-10-21T05:52:29.408Z",
                        "description": "Pending",
                        "endTime": "2020-10-21T05:52:29.408Z"
                    },
                    {
                        "endTime": "2020-10-21T05:53:03.297Z",
                        "startTime": "2020-10-21T05:53:02.773Z",
                        "description": "UpdatingJobStore"
                    }
                ],
                "start": "2020-10-21T05:52:29.408Z"
            }
        ]
    },
    "outputs": {},
    "workflowRoot": "/ags-wdl-nas/wf/cc195aee-41c7-44b0-89e5-4e94ba47f56b",
    "actualWorkflowLanguage": "WDL",
    "id": "cc195aee-41c7-44b0-89e5-4e94ba47f56b",
    "inputs": {
        "wf.agstask.config": "/ags-wdl-nas/bwatest/ags/config",
        "wf.bwa_mem_tool.fastqFolder": "/ags-wdl-nas/sample/MGISEQ2000_PCR-free_NA12878_1_V100003043_L01_1.fq.gz",
        "wf.bwa_mem_tool.outputdir": "/ags-wdl-nas/bwatest/output",
        "wf.bwa_mem_tool.cpunum": "6",
        "wf.bwa_mem_tool.bamfilename": "gene.bam"
    },
    "labels": {
        "cromwell-workflow-id": "cromwell-cc195aee-41c7-44b0-89e5-4e94ba47f56b"
    },
    "submission": "2020-10-21T05:52:13.812Z",
    "status": "Succeeded",
    "end": "2020-10-21T05:53:05.109Z",
    "start": "2020-10-21T05:52:28.360Z"
}
```

## Mount a PV

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the Container Service for Kubernetes \(ACK\) console, click **Releases**.

5.  On the **Releases** page, click the **Helm** tab, find the release name ack-ags-wdl, and then click **Update** in the **Actions** column.

6.  In the **Update Release** dialog box, mount a persistent volume \(PV\) to the cluster. The naspvc3 PV is used as an example.

    1.  Specify the name of the PV.

        In the **Update Release** dialog box, find the `naspvcs` field in the code editor and add the following content to the `naspvcs` field:

        ```
        naspvcs:
        - naspvc1
        - naspvc2
        - naspvc3
        ```

    2.  Add configuration to the PV.

        In the **Update Release** dialog box, add the following configuration to the code editor and click **Update**.

        ```
        naspvc3:
          driver: csi
          mountOptions: nolock,tcp,noresvport
          mountVers: "3"
          nasbasepath: /ags-wdl-nas3
          path: /tarTest/sample
          server: xxxxxxxx.cn-beijing.nas.aliyuncs.com
        ```

        After the PV is mounted, new jobs of the workflow can read data from or write data to the PV.


