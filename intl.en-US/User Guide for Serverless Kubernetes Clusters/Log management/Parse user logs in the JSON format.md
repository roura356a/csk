Parse user logs in the JSON format 
=======================================================



The standard output and error logs collected by Elastic Container Instance (ECI) are logs flushed to disks in the native format of Kubernetes. Kubernetes prefixes each line of log with information such as the timestamp and source, which corrupts the native format of user logs. For example, if the standard output is in the JSON format, Log Service fails to parse the standard output after Kubernetes adds the information to it. The following data is the sample of standard output with the information added by Kubernetes.




    2020-04-02T15:40:05.440500764+08:00 stdout F {"key1":"val1","key2":"val2"}
    2020-04-02T15:40:07.442412564+08:00 stdout F {"key1":"val1","key2":"val2"}
    2020-04-02T15:40:09.442774495+08:00 stdout F {"key1":"val1","key2":"val2"}
    2020-04-02T15:40:11.443799303+08:00 stdout F {"key1":"val1","key2":"val2"}
    2020-04-02T15:40:13.445099622+08:00 stdout F {"key1":"val1","key2":"val2"}
    2020-04-02T15:40:15.445934358+08:00 stdout F {"key1":"val1","key2":"val2"}
    2020-04-02T15:40:17.447064707+08:00 stdout F {"key1":"val1","key2":"val2"}
    2020-04-02T15:40:19.448112987+08:00 stdout F {"key1":"val1","key2":"val2"}
    2020-04-02T15:40:21.449393263+08:00 stdout F {"key1":"val1","key2":"val2"}



This topic describes how to use a Log Service processor to parse user logs in the JSON format.



User logs of ECI are collected to a Logstore under your account. Log on to the Log Service console. Find the target Logstore and modify its configurations. On the Logtail Config page, set the Mode parameter to Simple Mode and turn on the Enable Plug-in Processing switch.

Enter the following code in the Plug-in Config field. For more information, see [sls-json-processor](https://github.com/aliyuneci/Virtual-Kubelet-Example/blob/master/eci-sls/sls-json-processor.json).




    {
        "processors": [
            {
                "type": "processor_anchor",
                "detail": {
                    "SourceKey": "content",
                    "Anchors": [
                        {
                            "Start": "stdout F ",
                            "Stop": "",
                            "FieldName": "json_content",
                            "FieldType": "string",
                            "ExpondJson": false
                        }
                    ]
                }
            },
            {
                "type": "processor_json",
                "detail": {
                    "SourceKey": "json_content",
                    "KeepSource": false,
                    "ExpandConnector": ""
                }
            }
        ]
    }



Save the configurations. A few seconds later, you can view the logs parsed in the correct format.

In this way, Log Service correctly parses user logs in the JSON format.

