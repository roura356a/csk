# Workflow 示例模板 {#concept_286867 .concept}

本文给您提供Workflow 示例模板，便于您创建需要的工作流。

## Steps {#section_n6b_fci_52k .section}

本示例中，我们将了解如何创建多步骤工作流，如何在工作流规范中定义多个模板，以及如何创建嵌套工作流。 请务必阅读注释，以增强代码的可读性。

``` {#codeblock_8fw_g8m_qgr}
apiVersion: argoproj.io/v1alpha1
kind: Workflow
metadata:
  generateName: steps-
spec:
  entrypoint: hello-hello-hello

  # This spec contains two templates: hello-hello-hello and whalesay
  templates:
  - name: hello-hello-hello
    # Instead of just running a container
    # This template has a sequence of steps
    steps:
    - - name: hello1            # hello1 is run before the following steps
        template: whalesay
        arguments:
          parameters:
          - name: message
            value: "hello1"
    - - name: hello2a           # double dash => run after previous step
        template: whalesay
        arguments:
          parameters:
          - name: message
            value: "hello2a"
      - name: hello2b           # single dash => run in parallel with previous step
        template: whalesay
        arguments:
          parameters:
          - name: message
            value: "hello2b"

  # This is the same template as from the previous example
  - name: whalesay
    inputs:
      parameters:
      - name: message
    container:
      image: docker/whalesay
      command: [cowsay]
      args: ["{{inputs.parameters.message}}"]
```

Steps 工作流模板打印出三种不同风格的hello。 hello-hello-hello模板由三个步骤组成。 名称为hello1的第一步将按顺序运行，而后两个名称为hello2a 和 hello2b的步骤将彼此并行运行。 使用ags CLI命令，我们可以以图形方式显示此工作流规范的执行历史记录，该规则显示名为hello2a和hello2b的步骤彼此并行运行。

**结果显示**：

``` {#codeblock_6lj_l96_45o}
STEP                                     PODNAME
 ✔ arguments-parameters-rbm92
 ├---✔ hello1                   steps-rbm92-2023062412
 └-·-✔ hello2a                  steps-rbm92-685171357
   └-✔ hello2b                  steps-rbm92-634838500
```

## DAG {#section_kq0_qcl_8vg .section}

作为指定步骤序列的替代方法，您可以通过指定每个任务的依赖关系将工作流定义为有向非循环图（DAG）。 对于复杂的工作流程，这可以更简单地维护，并且在运行任务时允许最大的并行性。

在以下工作流程中，步骤A首先运行，因为它没有依赖项。 A完成后，步骤B和C并行运行。 最后，一旦B和C完成，步骤D就可以运行了。

``` {#codeblock_6un_jj9_7q0}
apiVersion: argoproj.io/v1alpha1
kind: Workflow
metadata:
  generateName: dag-diamond-
spec:
  entrypoint: diamond
  templates:
  - name: echo
    inputs:
      parameters:
      - name: message
    container:
      image: alpine:3.7
      command: [echo, "{{inputs.parameters.message}}"]
  - name: diamond
    dag:
      tasks:
      - name: A
        template: echo
        arguments:
          parameters: [{name: message, value: A}]
      - name: B
        dependencies: [A]
        template: echo
        arguments:
          parameters: [{name: message, value: B}]
      - name: C
        dependencies: [A]
        template: echo
        arguments:
          parameters: [{name: message, value: C}]
      - name: D
        dependencies: [B, C]
        template: echo
        arguments:
          parameters: [{name: message, value: D}]
```

依赖图可以有多个根。 从DAG或步骤模板调用的模板本身可以是DAG或步骤模板。 这可以允许将复杂的工作流程拆分为可管理的部分。

## Secrets {#section_8x3_1b0_k74 .section}

Template 支持与Kubernetes Pod规范相同的 Secret 语法和机制，允许访问 Secret 作为环境变量或 Volume mounts。

``` {#codeblock_vlp_ajo_lxs}
# To run this example, first create the secret by running:
# kubectl create secret generic my-secret --from-literal=mypassword=S00perS3cretPa55word
apiVersion: argoproj.io/v1alpha1
kind: Workflow
metadata:
  generateName: secret-example-
spec:
  entrypoint: whalesay
  # To access secrets as files, add a volume entry in spec.volumes[] and
  # then in the container template spec, add a mount using volumeMounts.
  volumes:
  - name: my-secret-vol
    secret:
      secretName: my-secret     # name of an existing k8s secret
  templates:
  - name: whalesay
    container:
      image: alpine:3.7
      command: [sh, -c]
      args: ['
        echo "secret from env: $MYSECRETPASSWORD";
        echo "secret from file: `cat /secret/mountpath/mypassword`"
      ']
      # To access secrets as environment variables, use the k8s valueFrom and
      # secretKeyRef constructs.
      env:
      - name: MYSECRETPASSWORD  # name of env var
        valueFrom:
          secretKeyRef:
            name: my-secret     # name of an existing k8s secret
            key: mypassword     # 'key' subcomponent of the secret
      volumeMounts:
      - name: my-secret-vol     # mount file containing secret at /secret/mountpath
        mountPath: "/secret/mountpath"
```

## Scripts & Results {#section_29k_wes_oxv .section}

通常，我们只需要一个模板来执行工作流规范中指定的脚本。 此示例显示了如何执行此操作：

``` {#codeblock_uv3_cvy_684}
apiVersion: argoproj.io/v1alpha1
kind: Workflow
metadata:
  generateName: scripts-bash-
spec:
  entrypoint: bash-script-example
  templates:
  - name: bash-script-example
    steps:
    - - name: generate
        template: gen-random-int-bash
    - - name: print
        template: print-message
        arguments:
          parameters:
          - name: message
            value: "{{steps.generate.outputs.result}}"  # The result of the here-script

  - name: gen-random-int-bash
    script:
      image: debian:9.4
      command: [bash]
      source: |                                         # Contents of the here-script
        cat /dev/urandom | od -N2 -An -i | awk -v f=1 -v r=100 '{printf "%i\n", f + r * $1 / 65536}'

  - name: gen-random-int-python
    script:
      image: python:alpine3.6
      command: [python]
      source: |
        import random
        i = random.randint(1, 100)
        print(i)

  - name: gen-random-int-javascript
    script:
      image: node:9.1-alpine
      command: [node]
      source: |
        var rand = Math.floor(Math.random() * 100);
        console.log(rand);

  - name: print-message
    inputs:
      parameters:
      - name: message
    container:
      image: alpine:latest
      command: [sh, -c]
      args: ["echo result was: {{inputs.parameters.message}}"]
```

Script 关键字允许使用 source 标记指定脚本体。这将创建一个包含脚本主体的临时文件，然后将临时文件的名称作为命令的最终参数传递，该命令应该是执行脚本主体的解释器。

脚本功能的使用还将运行脚本的标准输出分配给名为 Result 的特殊输出参数。 这允许您在其余的工作流规范中使用运行脚本本身的结果。 在此示例中，结果仅由打印消息模板回显。

## Output Parameters {#section_q6p_eaq_p8d .section}

Output Parameters 提供了将步骤的结果用作参数而不是使用外部存储的一般机制。 这允许您使用任何类型的步骤的结果，而不仅仅是脚本，用于条件测试，循环和参数。 Output Parameters 与脚本结果的工作方式类似，只是Output Parameters 的值设置为生成文件的内容而不是 stdout 的内容。

``` {#codeblock_vdd_cf6_izz}
apiVersion: argoproj.io/v1alpha1
kind: Workflow
metadata:
  generateName: output-parameter-
spec:
  entrypoint: output-parameter
  templates:
  - name: output-parameter
    steps:
    - - name: generate-parameter
        template: whalesay
    - - name: consume-parameter
        template: print-message
        arguments:
          parameters:
          # Pass the hello-param output from the generate-parameter step as the message input to print-message
          - name: message
            value: "{{steps.generate-parameter.outputs.parameters.hello-param}}"

  - name: whalesay
    container:
      image: docker/whalesay:latest
      command: [sh, -c]
      args: ["echo -n hello world > /tmp/hello_world.txt"]  # generate the content of hello_world.txt
    outputs:
      parameters:
      - name: hello-param   # name of output parameter
        valueFrom:
          path: /tmp/hello_world.txt  # set the value of hello-param to the contents of this hello-world.txt

  - name: print-message
    inputs:
      parameters:
      - name: message
    container:
      image: docker/whalesay:latest
      command: [cowsay]
      args: ["{{inputs.parameters.message}}"]
```

DAG 模板使用任务前缀来引用另一个任务，例如，`{{tasks.generate-parameter.outputs.parameters.hello-param}}`。

## Loops {#section_0cq_pr4_sqx .section}

在编写Loops工作流时：

-   迭代一组输入（最为常用），如下例所示。

    ``` {#codeblock_q75_ash_ria}
    apiVersion: argoproj.io/v1alpha1
    kind: Workflow
    metadata:
      generateName: loops-
    spec:
      entrypoint: loop-example
      templates:
      - name: loop-example
        steps:
        - - name: print-message
            template: whalesay
            arguments:
              parameters:
              - name: message
                value: "{{item}}"
            withItems:              # invoke whalesay once for each item in parallel
            - hello world           # item 1
            - goodbye world         # item 2
    
      - name: whalesay
        inputs:
          parameters:
          - name: message
        container:
          image: docker/whalesay:latest
          command: [cowsay]
          args: ["{{inputs.parameters.message}}"]
    ```

-   迭代多组项目，如下例所示。

    ``` {#codeblock_2rd_zc1_asn}
    apiVersion: argoproj.io/v1alpha1
    kind: Workflow
    metadata:
      generateName: loops-maps-
    spec:
      entrypoint: loop-map-example
      templates:
      - name: loop-map-example
        steps:
        - - name: test-linux
            template: cat-os-release
            arguments:
              parameters:
              - name: image
                value: "{{item.image}}"
              - name: tag
                value: "{{item.tag}}"
            withItems:
            - { image: 'debian', tag: '9.1' }       #item set 1
            - { image: 'debian', tag: '8.9' }       #item set 2
            - { image: 'alpine', tag: '3.6' }       #item set 3
            - { image: 'ubuntu', tag: '17.10' }     #item set 4
    
      - name: cat-os-release
        inputs:
          parameters:
          - name: image
          - name: tag
        container:
          image: "{{inputs.parameters.image}}:{{inputs.parameters.tag}}"
          command: [cat]
          args: [/etc/os-release]
    ```

-   将项目列表作为参数传递，如下例所示。

    ``` {#codeblock_qdu_12o_ov1}
    apiVersion: argoproj.io/v1alpha1
    kind: Workflow
    metadata:
      generateName: loops-param-arg-
    spec:
      entrypoint: loop-param-arg-example
      arguments:
        parameters:
        - name: os-list                                     # a list of items
          value: |
            [
              { "image": "debian", "tag": "9.1" },
              { "image": "debian", "tag": "8.9" },
              { "image": "alpine", "tag": "3.6" },
              { "image": "ubuntu", "tag": "17.10" }
            ]
    
      templates:
      - name: loop-param-arg-example
        inputs:
          parameters:
          - name: os-list
        steps:
        - - name: test-linux
            template: cat-os-release
            arguments:
              parameters:
              - name: image
                value: "{{item.image}}"
              - name: tag
                value: "{{item.tag}}"
            withParam: "{{inputs.parameters.os-list}}"      # parameter specifies the list to iterate over
    
      # This template is the same as in the previous example
      - name: cat-os-release
        inputs:
          parameters:
          - name: image
          - name: tag
        container:
          image: "{{inputs.parameters.image}}:{{inputs.parameters.tag}}"
          command: [cat]
          args: [/etc/os-release]
    ```

-   动态生成要迭代的项目列表，如下例所示。

    ``` {#codeblock_ui9_hql_mrm}
    apiVersion: argoproj.io/v1alpha1
    kind: Workflow
    metadata:
      generateName: loops-param-result-
    spec:
      entrypoint: loop-param-result-example
      templates:
      - name: loop-param-result-example
        steps:
        - - name: generate
            template: gen-number-list
        # Iterate over the list of numbers generated by the generate step above
        - - name: sleep
            template: sleep-n-sec
            arguments:
              parameters:
              - name: seconds
                value: "{{item}}"
            withParam: "{{steps.generate.outputs.result}}"
    
      # Generate a list of numbers in JSON format
      - name: gen-number-list
        script:
          image: python:alpine3.6
          command: [python]
          source: |
            import json
            import sys
            json.dump([i for i in range(20, 31)], sys.stdout)
    
      - name: sleep-n-sec
        inputs:
          parameters:
          - name: seconds
        container:
          image: alpine:latest
          command: [sh, -c]
          args: ["echo sleeping for {{inputs.parameters.seconds}} seconds; sleep {{inputs.parameters.seconds}}; echo done"]
    ```


## Conditionals {#section_oep_1b5_bmg .section}

我们还支持条件执行，如下例所示。

``` {#codeblock_pzp_28q_joh}
apiVersion: argoproj.io/v1alpha1
kind: Workflow
metadata:
  generateName: coinflip-
spec:
  entrypoint: coinflip
  templates:
  - name: coinflip
    steps:
    # flip a coin
    - - name: flip-coin
        template: flip-coin
    # evaluate the result in parallel
    - - name: heads
        template: heads                 # call heads template if "heads"
        when: "{{steps.flip-coin.outputs.result}} == heads"
      - name: tails
        template: tails                 # call tails template if "tails"
        when: "{{steps.flip-coin.outputs.result}} == tails"

  # Return heads or tails based on a random number
  - name: flip-coin
    script:
      image: python:alpine3.6
      command: [python]
      source: |
        import random
        result = "heads" if random.randint(0,1) == 0 else "tails"
        print(result)

  - name: heads
    container:
      image: alpine:3.6
      command: [sh, -c]
      args: ["echo \"it was heads\""]

  - name: tails
    container:
      image: alpine:3.6
      command: [sh, -c]
      args: ["echo \"it was tails\""]
```

## Recursion {#section_owy_gz7_0kc .section}

模板可以递归地相互调用。 在上述硬币翻转模板的这种变体中，我们继续翻转硬币直到它出现在头部。

``` {#codeblock_xwq_fy5_9mc}
apiVersion: argoproj.io/v1alpha1
kind: Workflow
metadata:
  generateName: coinflip-recursive-
spec:
  entrypoint: coinflip
  templates:
  - name: coinflip
    steps:
    # flip a coin
    - - name: flip-coin
        template: flip-coin
    # evaluate the result in parallel
    - - name: heads
        template: heads                 # call heads template if "heads"
        when: "{{steps.flip-coin.outputs.result}} == heads"
      - name: tails                     # keep flipping coins if "tails"
        template: coinflip
        when: "{{steps.flip-coin.outputs.result}} == tails"

  - name: flip-coin
    script:
      image: python:alpine3.6
      command: [python]
      source: |
        import random
        result = "heads" if random.randint(0,1) == 0 else "tails"
        print(result)

  - name: heads
    container:
      image: alpine:3.6
      command: [sh, -c]
      args: ["echo \"it was heads\""]
```

这是几次硬币翻转的结果，用于比较。

``` {#codeblock_nfl_21d_xrg}
ags get coinflip-recursive-tzcb5

STEP                         PODNAME                              MESSAGE
 ✔ coinflip-recursive-vhph5
 ├---✔ flip-coin             coinflip-recursive-vhph5-2123890397
 └-·-✔ heads                 coinflip-recursive-vhph5-128690560
   └-○ tails

STEP                          PODNAME                              MESSAGE
 ✔ coinflip-recursive-tzcb5
 ├---✔ flip-coin              coinflip-recursive-tzcb5-322836820
 └-·-○ heads
   └-✔ tails
     ├---✔ flip-coin          coinflip-recursive-tzcb5-1863890320
     └-·-○ heads
       └-✔ tails
         ├---✔ flip-coin      coinflip-recursive-tzcb5-1768147140
         └-·-○ heads
           └-✔ tails
             ├---✔ flip-coin  coinflip-recursive-tzcb5-4080411136
             └-·-✔ heads      coinflip-recursive-tzcb5-4080323273
               └-○ tails
```

在第一次运行中，硬币立即出现在头部，我们停下来； 在第二次运行中，硬币上升了三次，然后它终于出现了，我们就停了下来。

## Exit handlers {#section_he0_cy4_162 .section}

Exit handlers 是在工作流结束时始终执行的模板，无论成功或失败。

Exit handlers 的一些常见用例。

-   工作流程运行后清理
-   发送工作流程状态通知（例如，电子邮件/ Slack）
-   将通过/失败状态发布到webhook结果（例如GitHub构建结果）
-   重新提交或提交其他工作流程

``` {#codeblock_8wb_b55_mi9}
apiVersion: argoproj.io/v1alpha1
kind: Workflow
metadata:
  generateName: exit-handlers-
spec:
  entrypoint: intentional-fail
  onExit: exit-handler                  # invoke exit-hander template at end of the workflow
  templates:
  # primary workflow template
  - name: intentional-fail
    container:
      image: alpine:latest
      command: [sh, -c]
      args: ["echo intentional failure; exit 1"]

  # Exit handler templates
  # After the completion of the entrypoint template, the status of the
  # workflow is made available in the global variable {{workflow.status}}.
  # {{workflow.status}} will be one of: Succeeded, Failed, Error
  - name: exit-handler
    steps:
    - - name: notify
        template: send-email
      - name: celebrate
        template: celebrate
        when: "{{workflow.status}} == Succeeded"
      - name: cry
        template: cry
        when: "{{workflow.status}} != Succeeded"
  - name: send-email
    container:
      image: alpine:latest
      command: [sh, -c]
      args: ["echo send e-mail: {{workflow.name}} {{workflow.status}}"]
  - name: celebrate
    container:
      image: alpine:latest
      command: [sh, -c]
      args: ["echo hooray!"]
  - name: cry
    container:
      image: alpine:latest
      command: [sh, -c]
      args: ["echo boohoo!"]
```

## Timeouts {#section_a33_qpt_erd .section}

要限制工作流的已用时间，可以设置变量activeDeadlineSeconds。

``` {#codeblock_pq4_ydd_f5p}
# To enforce a timeout for a container template, specify a value for activeDeadlineSeconds.
apiVersion: argoproj.io/v1alpha1
kind: Workflow
metadata:
  generateName: timeouts-
spec:
  entrypoint: sleep
  templates:
  - name: sleep
    container:
      image: alpine:latest
      command: [sh, -c]
      args: ["echo sleeping for 1m; sleep 60; echo done"]
    activeDeadlineSeconds: 10           # terminate container template after 10 seconds
```

## Volumes {#section_4zl_ehn_n8z .section}

以下示例动态创建卷，然后在两步工作流中使用该卷。

``` {#codeblock_do5_bah_okj}
apiVersion: argoproj.io/v1alpha1
kind: Workflow
metadata:
  generateName: volumes-pvc-
spec:
  entrypoint: volumes-pvc-example
  volumeClaimTemplates:                 # define volume, same syntax as k8s Pod spec
  - metadata:
      name: workdir                     # name of volume claim
    spec:
      accessModes: [ "ReadWriteOnce" ]
      resources:
        requests:
          storage: 1Gi                  # Gi => 1024 * 1024 * 1024

  templates:
  - name: volumes-pvc-example
    steps:
    - - name: generate
        template: whalesay
    - - name: print
        template: print-message

  - name: whalesay
    container:
      image: docker/whalesay:latest
      command: [sh, -c]
      args: ["echo generating message in volume; cowsay hello world | tee /mnt/vol/hello_world.txt"]
      # Mount workdir volume at /mnt/vol before invoking docker/whalesay
      volumeMounts:                     # same syntax as k8s Pod spec
      - name: workdir
        mountPath: /mnt/vol

  - name: print-message
    container:
      image: alpine:latest
      command: [sh, -c]
      args: ["echo getting message from volume; find /mnt/vol; cat /mnt/vol/hello_world.txt"]
      # Mount workdir volume at /mnt/vol before invoking docker/whalesay
      volumeMounts:                     # same syntax as k8s Pod spec
      - name: workdir
        mountPath: /mnt/vol
```

卷是将大量数据从工作流中的一个步骤移动到另一个步骤的非常有用的方法。 根据系统的不同，可以从多个步骤同时访问某些卷。

在某些情况下，您希望访问现有卷，而不是动态创建或销毁一个卷。

``` {#codeblock_axf_4im_hci}
# Define Kubernetes PVC
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: my-existing-volume
spec:
  accessModes: [ "ReadWriteOnce" ]
  resources:
    requests:
      storage: 1Gi

---
apiVersion: argoproj.io/v1alpha1
kind: Workflow
metadata:
  generateName: volumes-existing-
spec:
  entrypoint: volumes-existing-example
  volumes:
  # Pass my-existing-volume as an argument to the volumes-existing-example template
  # Same syntax as k8s Pod spec
  - name: workdir
    persistentVolumeClaim:
      claimName: my-existing-volume

  templates:
  - name: volumes-existing-example
    steps:
    - - name: generate
        template: whalesay
    - - name: print
        template: print-message

  - name: whalesay
    container:
      image: docker/whalesay:latest
      command: [sh, -c]
      args: ["echo generating message in volume; cowsay hello world | tee /mnt/vol/hello_world.txt"]
      volumeMounts:
      - name: workdir
        mountPath: /mnt/vol

  - name: print-message
    container:
      image: alpine:latest
      command: [sh, -c]
      args: ["echo getting message from volume; find /mnt/vol; cat /mnt/vol/hello_world.txt"]
      volumeMounts:
      - name: workdir
        mountPath: /mnt/vol
```

## Daemon Containers {#section_i6d_ixv_hla .section}

工作流可以启动在后台运行的容器（也称为守护程序容器），同时工作流本身继续执行。 请注意，当工作流退出调用守护程序的模板范围时，将自动销毁守护程序。 守护进程容器可用于启动要测试的服务或用于测试（例如，固定装置）。 我们还发现，在运行大型模拟以将数据库作为用于收集和组织结果的守护进程时，它非常实用。 守护进程与 sidecars 相比的最大优势在于它们的存在可以持续跨越多个步骤甚至整个工作流程。

``` {#codeblock_mzt_v2e_rxv}
apiVersion: argoproj.io/v1alpha1
kind: Workflow
metadata:
  generateName: daemon-step-
spec:
  entrypoint: daemon-example
  templates:
  - name: daemon-example
    steps:
    - - name: influx
        template: influxdb              # start an influxdb as a daemon (see the influxdb template spec below)

    - - name: init-database             # initialize influxdb
        template: influxdb-client
        arguments:
          parameters:
          - name: cmd
            value: curl -XPOST 'http://{{steps.influx.ip}}:8086/query' --data-urlencode "q=CREATE DATABASE mydb"

    - - name: producer-1                # add entries to influxdb
        template: influxdb-client
        arguments:
          parameters:
          - name: cmd
            value: for i in $(seq 1 20); do curl -XPOST 'http://{{steps.influx.ip}}:8086/write?db=mydb' -d "cpu,host=server01,region=uswest load=$i" ; sleep .5 ; done
      - name: producer-2                # add entries to influxdb
        template: influxdb-client
        arguments:
          parameters:
          - name: cmd
            value: for i in $(seq 1 20); do curl -XPOST 'http://{{steps.influx.ip}}:8086/write?db=mydb' -d "cpu,host=server02,region=uswest load=$((RANDOM % 100))" ; sleep .5 ; done
      - name: producer-3                # add entries to influxdb
        template: influxdb-client
        arguments:
          parameters:
          - name: cmd
            value: curl -XPOST 'http://{{steps.influx.ip}}:8086/write?db=mydb' -d 'cpu,host=server03,region=useast load=15.4'

    - - name: consumer                  # consume intries from influxdb
        template: influxdb-client
        arguments:
          parameters:
          - name: cmd
            value: curl --silent -G http://{{steps.influx.ip}}:8086/query?pretty=true --data-urlencode "db=mydb" --data-urlencode "q=SELECT * FROM cpu"

  - name: influxdb
    daemon: true                        # start influxdb as a daemon
    container:
      image: influxdb:1.2
      restartPolicy: Always             # restart container if it fails
      readinessProbe:                   # wait for readinessProbe to succeed
        httpGet:
          path: /ping
          port: 8086

  - name: influxdb-client
    inputs:
      parameters:
      - name: cmd
    container:
      image: appropriate/curl:latest
      command: ["/bin/sh", "-c"]
      args: ["{{inputs.parameters.cmd}}"]
      resources:
        requests:
          memory: 32Mi
          cpu: 100m
```

DAG模板使用任务前缀来引用另一个任务，例如`{{tasks.influx.ip}}`。

## Sidecars {#section_4eb_074_q36 .section}

Sidecar是另一个容器，它与主容器在同一个容器中同时执行，在创建多容器容器时很实用。

在本示例中，我们创建了一个 Sidecar 容器，它将 nginx 作为简单的Web服务器运行。 容器出现的顺序是随机的，因此在此示例中，主容器轮询 nginx 容器，直到它准备好为请求提供服务。 在设计多容器系统时，这是一个很好的设计模式：在运行主代码之前，请始终等待所需的任何服务。

``` {#codeblock_zuu_qqg_0uy}
apiVersion: argoproj.io/v1alpha1
kind: Workflow
metadata:
  generateName: sidecar-nginx-
spec:
  entrypoint: sidecar-nginx-example
  templates:
  - name: sidecar-nginx-example
    container:
      image: appropriate/curl
      command: [sh, -c]
      # Try to read from nginx web server until it comes up
      args: ["until `curl -G 'http://127.0.0.1/' >& /tmp/out`; do echo sleep && sleep 1; done && cat /tmp/out"]
    # Create a simple nginx web server
    sidecars:
    - name: nginx
      image: nginx:1.13
```

## Kubernetes Resources {#section_74s_i25_jpl .section}

在多数情况下，您需要从工作流程管理 Kubernetes 资源。 资源模板允许您创建，删除或更新任何类型的Kubernetes 资源。

``` {#codeblock_bls_lq1_drl}
# in a workflow. The resource template type accepts any k8s manifest
# (including CRDs) and can perform any kubectl action against it (e.g. create,
# apply, delete, patch).
apiVersion: argoproj.io/v1alpha1
kind: Workflow
metadata:
  generateName: k8s-jobs-
spec:
  entrypoint: pi-tmpl
  templates:
  - name: pi-tmpl
    resource:                   # indicates that this is a resource template
      action: create            # can be any kubectl action (e.g. create, delete, apply, patch)
      # The successCondition and failureCondition are optional expressions.
      # If failureCondition is true, the step is considered failed.
      # If successCondition is true, the step is considered successful.
      # They use kubernetes label selection syntax and can be applied against any field
      # of the resource (not just labels). Multiple AND conditions can be represented by comma
      # delimited expressions.
      # For more details: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
      successCondition: status.succeeded > 0
      failureCondition: status.failed > 3
      manifest: |               #put your kubernetes spec here
        apiVersion: batch/v1
        kind: Job
        metadata:
          generateName: pi-job-
        spec:
          template:
            metadata:
              name: pi
            spec:
              containers:
              - name: pi
                image: perl
                command: ["perl",  "-Mbignum=bpi", "-wle", "print bpi(2000)"]
              restartPolicy: Never
          backoffLimit: 4
```

以这种方式创建的资源独立于工作流程。 如果希望在删除工作流时删除资源，则可以将 Kubernetes 垃圾回收与工作流资源一起用作所有者引用。

**说明：** 

更新Kubernetes资源时，资源将接受mergeStrategy属性，该属性的取值为strategy、merge或json。 如果未提供此属性，则默认为strategy属性。 需要注意的是，不能使用策略修补自定义资源，因此必须选择不同的策略。 例如，假设您已定义 CronTab CustomResourceDefinition，并且以下是 CronTab 的实例：

``` {#codeblock_1zv_buf_1ns}
apiVersion: "stable.example.com/v1"
kind: CronTab
spec:
  cronSpec: "* * * * */5"
  image: my-awesome-cron-image
```

可以使用以下工作流修改此 Crontab。

``` {#codeblock_jug_eii_idc}
apiVersion: argoproj.io/v1alpha1
kind: Workflow
metadata:
  generateName: k8s-patch-
spec:
  entrypoint: cront-tmpl
  templates:
  - name: cront-tmpl
    resource:
      action: patch
      mergeStrategy: merge                 # Must be one of [strategic merge json]
      manifest: |
        apiVersion: "stable.example.com/v1"
        kind: CronTab
        spec:
          cronSpec: "* * * * */10"
          image: my-awesome-cron-image
```

## 更多资源 {#section_meo_j4i_eun .section}

-   更多资源展示，请参见[https://github.com/argoproj/argo/blob/master/examples/README.md](https://github.com/argoproj/argo/blob/master/examples/README.md)。
-   所有资料示例模板，请参见[https://github.com/argoproj/argo/tree/master/examples](https://github.com/argoproj/argo/tree/master/examples)。

