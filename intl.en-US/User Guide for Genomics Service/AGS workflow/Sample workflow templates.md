# Sample workflow templates

This topic provides multiple sample workflow templates that can be used to create workflows.

## Steps

This type of workflow template can be used to create multi-step workflows, define more than one template in a workflow specification, and create nested workflows. We recommend that you read the comments to ensure the readability of the code.

```
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

The preceding workflow prints a hello-hello-hello template that contains three distinct steps. The first step named hello1 runs in sequence. The next two steps named hello2a and hello2b run in parallel with each other. You can run commands in the AGS command-line interface \(CLI\) to display the running records of this workflow specification. The following tree-structure diagram shows that the steps hello2a and hello2b are performed in parallel with each other.

The following output is returned:

```
STEP                                     PODNAME
 ✔ arguments-parameters-rbm92
 ├---✔ hello1                   steps-rbm92-2023062412
 └-·-✔ hello2a                  steps-rbm92-685171357
   └-✔ hello2b                  steps-rbm92-634838500
```

## DAG

This type of workflow template can also be used to specify the sequence of steps in a workflow. You can define the workflow as a directed acyclic graph \(DAG\) by specifying the dependencies of each task. This method allows you to simplify complex workflows and enable a maximum number of tasks to run in parallel with each other.

The following workflow template shows that Task A has no dependencies and runs first. After Task A is completed, Tasks B and C run in parallel with each other. Then, after Tasks B and C are completed, Task D runs.

```
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

A dependency graph may have multiple roots. The template that is called by a DAG or Steps template can be a DAG or Steps template. This allows you to separate a complex workflow into multiple parts that are easy to manage.

## Secrets

This type of workflow template supports the same Secret syntax and mechanisms as the Kubernetes pod specification. You can access a Secret that serves as an environment variable or volume by using this template.

```
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

## Scripts & Results

In most cases, a template is required to run a script specified in the workflow specification. The following example shows how to use a template to run the specified script:

```
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

The script keyword allows you to specify the script body by using the source tag. This allows you to create a temporary file that contains the script body. Then, you can specify the name of the temporary file as the final parameter in the command. The command must be the interpreter that runs the script body.

The script feature can also be used to assign the standard output of the script to a special output parameter named result. This allows you to use the result of running the script in the rest of the workflow specification. In the preceding example, the result is echoed by the print-message template.

## Output Parameters

This type of workflow template allows you to use the result of a step as a parameter, rather than as an artifact. In addition to the results of scripts, you can also use the results from a type of step. These results apply to condition tests, loops, and arguments. Output parameters are used in a similar way as script results. However, the values of output parameters are set to the content of a generated file rather than the content of stdout.

```
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

However, DAG template uses a task prefix to specify another task. For example, `{{tasks.generate-parameter.outputs.parameters.hello-param}}` can be specified.

## Loops

This type of workflow template allows you to configure a loop workflow.

-   The following template is used to iterate over a set of inputs:

    ```
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

-   The following template is used to iterate over sets of items:

    ```
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

-   The following template is used to deliver lists of items as parameters:

    ```
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

-   The following template is used to dynamically generate the list of items to be iterated over:

    ```
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


## Conditionals

This type of workflow template supports conditional execution. The following example shows a sample template named coinflip.

```
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

## Recursion

Workflow templates can recursively invoke each other. The following template is a variation of the preceding coinflip template. In this variation template, the coin is flipped until it lands on heads.

```
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

The following output shows the result of running templates several times to flip the coin:

```
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

In the first round of running the template to flip the coin, the coin lands on heads and the coin is no longer flipped. In the second round of flipping the coin, the coin lands on tails three times before it lands on heads. Then, the coin is no longer flipped.

## Exit handlers

This type of workflow template is run at the end of a workflow, regardless of whether the workflow succeeds or fails.

Exit handlers apply to the following operations:

-   Clean up data after a workflow is run.
-   Send notifications of a workflow status by emails or Slack messages.
-   Post the successful or failed state to a webhook result. For example, a GitHub build result can be posted.
-   Resubmit a workflow or submit a new workflow.

```
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
        when: "{{workflow.status}} ! = Succeeded"
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

## Timeouts

This type of workflow template can be used to limit the timeout of a workflow. In such template, you can set the variable activeDeadlineSeconds to the required timeout value.

```
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

## Volumes

This type of workflow template is used to manage volumes. In the following example, a volume is dynamically created, and then used in a two-step workflow:

```
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

You can use volumes to move large amounts of data from one step in a workflow to another. Specific volumes can be accessed from multiple steps at the same time based on your business requirements.

If you want to access an existing volume, instead of dynamically creating or destroying a volume, you can use or modify the volume in the following example:

```
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

## Daemon Containers

Workflows can start containers \(also known as daemon containers\) that run in the backend when the workflows continue to run. The daemons are automatically destroyed when the workflow exits the template scope in which the daemon is invoked. Daemon containers can be used to start the services to be tested, or in a fixture test or other tests. Daemon containers can also be used to run large simulations to set a database as a daemon. You can use the daemon to collect and organize the results. Compared with sidecars, daemons can run over multiple steps or even the entire workflow.

```
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

A DAG template uses a task prefix to specify another task. For example, `{{tasks.influx.ip}}` can be specified.

## Sidecars

A sidecar is a container that runs in the same pod where the main container is executed. Sidecars allow you to create a pod that contains multiple containers.

In the following workflow template of the sidecar type, a sidecar container is created to run NGINX as a simple web server. The sidecar container may be ready at any time in a workflow. Therefore, the main container checks the health status of the sidecar container in cycles. When the main container detects that the sidecar container is in the ready state, the main container starts to process requests for the application. We recommend that you use this mechanism for multi-container systems. Before Container Service for Kubernetes \(ACK\) can run your application, all required services must be ready.

```
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

## Kubernetes Resources

This type of workflow template allows you to manage ACK resources. You can use this type of workflow template to create, delete, or update ACK resources.

```
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

Resources that are created based on this type of workflow template are independent of the workflow. If you want to delete a resource in a workflow when you delete the workflow, you can use the Kubernetes garbage collection feature and the workflow resources as an owner reference.

**Note:**

When you patch the Kubernetes resources, the resources gain the mergeStrategy attribute. The attribute can be set to strategy, merge, or json. By default, strategy is used. The strategy value cannot be used to patch custom resources. You must use one of the other two mergeStrategy values. The following example shows the Custom Resource Definition that defines the CronTab:

```
apiVersion: "stable.example.com/v1"
kind: CronTab
spec:
  cronSpec: "* * * * */5"
  image: my-awesome-cron-image
```

You can modify the preceding CronTab by using the following workflow:

```
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

## References

-   For more information about workflow resources, see [Argo workflow templates by example](https://github.com/argoproj/argo/blob/master/examples/README.md).
-   For more information about the sample templates, see [Sample templates](https://github.com/argoproj/argo/tree/master/examples).

