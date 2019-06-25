# Sample workflow templates {#concept_286867 .concept}

This topic provides several sample workflow templates that can be used to create workflows.

## Steps {#section_n6b_fci_52k .section}

This sample workflow template can be used to create multi-step workflows, define more than one template in a workflow specification, and create nested workflows.

**Note:** We recommend that you read the comments to ensure qualified code.

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

The preceding workflow prints a hello-hello-hello template that contains three distinct hello steps. The first step named hello1 runs in sequence, whereas the next two steps named hello2a and hello2b run parallel with each other. By using the Ags CLI command, you can display the running records of this workflow specification through the following tree-structure diagram:

``` {#codeblock_qow_913_r4x}
STEP                                     PODNAME
 ✔ arguments-parameters-rbm92
 ├---✔ hello1                   steps-rbm92-2023062412
 └-·-✔ hello2a                  steps-rbm92-685171357
   └-✔ hello2b                  steps-rbm92-634838500
```

## Directed acyclic graph \(DAG\) {#section_kq0_qcl_8vg .section}

This sample workflow template can be also used to specify the sequence of steps in a workflow. You can define the workflow as a directed acyclic graph \(DAG\) by specifying the dependencies of each task. This method can help to simplify complex workflows by allowing a maximum number of tasks to be run in parallel.

The following workflow template shows the sequence of steps as follows:

1.  Step A runs first having no dependency.
2.  When step A is complete, steps B and C run in parallel.
3.  When both B and C are complete, step D runs.

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

A dependency graph may have multiple roots. The templates called from a DAG or Steps template can be a DAG or Steps template. This allows you to separate a complex workflow into manageable parts.

## Secrets {#section_8x3_1b0_k74 .section}

This sample workflow templates supports the same secret syntax and mechanisms as the Kubernetes pod specification. Through using this template, you can access a secret that functions as an environment variable or volume.

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

## Scripts and results {#section_29k_wes_oxv .section}

Generally, a template is required to run a script specified in the workflow specification. This following example shows how to do that:

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

The script keyword allows you to specify the script body by using the source tag. This action first creates a temporary file that contains the script body, and then it passes the name of the temporary file as the final parameter to the command. The command must be the interpreter that runs the script body.

The script feature can also be used to assign the standard output of running the script to a special output parameter named result. All of this allows you to use the result of running the script in the rest of the workflow specification. In the preceding example, the result is echoed by the print-message template.

## Output parameters {#section_q6p_eaq_p8d .section}

This sample workflow templates provides a general means by which you can use the result of a step as a parameter, rather than as an artifact. This allows you to use the results from any type of step \(rather than scripts\) for condition tests, loops, and arguments. Output parameters work similarly to script results except that the value of the output parameters is set to the content of a generated file rather than the content of stdout.

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

However, DAG templates use a task prefix to refer to another task. For example, `{{tasks.generate-parameter.outputs.parameters.hello-param}}`.

## Loops {#section_0cq_pr4_sqx .section}

-   The following template iterates over a set of inputs:

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

-   The following template iterates over sets of items:

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

-   The following template passes lists of items as parameters:

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

-   The following template dynamically generates the list of items to be iterated over:

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

A workflow template of the Conditionals type supports conditional execution. The following is a sample template named coninflip.

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

Workflow templates can recursively invoke each other. In the following template \(a variation of the preceding coinflip template\), the coin is flipped until it lands on heads.

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

The following shows the result of several times of running templates to flip the coin:

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

In the first round to run the template to flip the coin, the coin immediately lands on heads and and the coin is no longer flipped. In the second round to flip the coin, the coin lands on tails three times before landing on heads at which time the coin is no longer flipped.

## Exit handlers {#section_he0_cy4_162 .section}

An exit handler is a template run at the end of the workflow, regardless of whether the workflow succeeded or failed.

Exit handlers can be used if you want to perform any of the following actions:

-   Clean up after a workflow is run.
-   Send notifications of workflow status \(for example, emails or Slack messages\).
-   Post the pass or fail status to a WebHook result \(for example, a GitHub build result\).
-   Resubmit a workflow or submit a new workflow.

``` {#codeblock_hs6_g4x_vnz}
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

A workflow template of the timeouts type can be used to limit the timeout of a workflow. In such a template, you must set the variable activeDeadlineSecondsfor a timeout value to be specified.

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

In the following example, a volume is dynamically created, and then used in a two-step workflow.

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

Volumes help you move large amounts of data from one step in a workflow to another. Depending on the system, some volumes can be accessed from multiple steps at the same time.

If you want to access an existing volume, instead of dynamically create or destroy a volume, you can use or modify the following sample volume as needed:

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

## Daemon containers {#section_i6d_ixv_hla .section}

Workflows can start containers \(also known as daemon containers\) that run in the backend while the workflow continues to be run. The daemons are automatically destroyed when the workflow exits the template scope in which the daemon is invoked. Daemon containers can be used to start services to be tested, or can be directly used in a fixture test or other tests.

Daemon containers can also be used to run large simulations to set a database as a daemon for collecting and organizing the results. The advantage of daemons over sidecars is that daemons can be run over multiple steps or even the entire workflow.

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

DAG templates use the tasks prefix to refer to another task. For example, `{{tasks.influx.ip}}`.

## Sidecars {#section_4eb_074_q36 .section}

A sidecar is a container that is run in the same pod where the main container is executed. Sidecars help you create a pod that contains multiple containers.

The following workflow template of the sidecar type creates a sidecar container that runs Nginx as a simple web server. Containers come up in random order. Therefore, the main container polls the Nginx container until it is ready to response requests. We recommend that you use this design pattern for multi-container systems. That is, before you run the main code, you must wait for all of the required services to come up.

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

## Kubernetes resources {#section_74s_i25_jpl .section}

If you want to manage Kubernetes resources by using workflows, you can use a resource template, which allows you to create, delete, or updated any type of Kubernetes resources.

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

Resources created with this method are independent of the workflow. If you want the resource to be deleted when you delete the workflow, you can use Kubernetes garbage collection and the workflow resources as an owner reference.

**Note:** 

-   When you patch the Kubernetes resources, the resources gain the mergeStrategy attribute, which can be strategy, merge, or json. By default, strategy is used.
-   strategy cannot be used to patch custom resources. You must use one of the other two mergeStrategy values. For example, you have defined a CronTab of Custom Resource Definition as follows:

    ``` {#codeblock_nyb_bs3_mxh}
    apiVersion: "stable.example.com/v1"
    kind: CronTab
    spec:
      cronSpec: "* * * * */5"
      image: my-awesome-cron-image
    ```

    You can modify the preceding CronTab by using the following workflow:

    ``` {#codeblock_vgc_uum_xil}
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


## Reference {#section_meo_j4i_eun .section}

-   For information about more resources, see [Argo workflow templates by example](https://github.com/argoproj/argo/blob/master/examples/README.md).
-   For information about all the sample templates, see [Sample templates](https://github.com/argoproj/argo/tree/master/examples).

