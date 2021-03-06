### Installing OpenShift CLI Tool
```
https://access.redhat.com/downloads/content/290/ver=4.9/rhel---8/4.9.18/x86_64/product-software
```

### Installing Tekton CLI in CentOS 8.x
For detailed instructions, you can head over to the official web page https://github.com/tektoncd/cli
```
curl -LO https://github.com/tektoncd/cli/releases/download/v0.21.0/tkn_0.21.0_Linux_x86_64.tar.gz
sudo tar xvzf tkn_0.21.0_Linux_x86_64.tar.gz -C /usr/local/bin/ tkn
```

You may now check if tkn command works
```
tkn version
```
The expected output is
<pre>
[root@master ~]# tkn version
Client version: 0.21.0
Pipeline version: v0.32.1
</pre>


### Installing Tekton in Openshift
Tekton can be installed from OpenShift webconsole "OpenShift Pipelines Operator" as an Administrator.  Pipeline can be created from any namespace.

### Installing Tekton in Kubernetes Cluster
```
kubectl apply -f https://storage.googleapis.com/tekton-releases/pipeline/latest/release.yaml
```

### What is Tekton?
- is an opensource project that helps creating and running CI/CD pipelines within Kubernetes and OpenShift.
- is a cloud native CI/CD tool.
- is a kubernetes native application that extends Kubernetes/OpenShift by adding Custom Resources like Task, TaskRun, Pipeline, PipelineRun etc.,
- cloud-native CI/CD is based on 3 principles
  1. Containers
       - each CI/CD operation like compilation, testing, packaging, etc happens in an isolated container.
  2. Serverless
       - running and scaling CI/CD on demand without the need for a central CI engine
  3. DevOps
       - cloud-native CI/CD needs to be build with DevOps practices in mind
       - teams are allowed to own their delivery pipelines

### What is Tekton CLI?
- command line tool that is used to manage Tekton pipeline
- this tool makes it easier to interact with Tekton components better than oc or kubectl

### What is Tekton Triggers?
- child project of Tekton
- allows users to add a way to launch pipelines based on webhooks 
- using Triggers one can launch piepline each time a git push is done in your version control repository.

### What is Tekton Catalog?
- Tekton Tasks are reusable 
- Tekton Tasks can be re-used in different Pipelines
- Resusable tasks are available in https://github.com/tektoncd/catalog

### Tekton building blocks
- Steps, Tasks and Pipleline
- Steps
   - represents a single operation in CI/CD process
   - each step runs in its own container
   - once the step is completed, the container is dicarded 
   - as the container will be disposed once the step completes, required data needs to be stored in a shared volume
- Tasks
    - is a collection of steps that run in a sequence
    - Steps inside a single Task should be related to each other
    - Task will be executed in a single Pod that enables Steps to share common Volume and shared resources
    - Pre-written Tasks can be found in Tekton Catalog
- Pipeline
    - is a collection of Tasks that performs a series of operations on an input and potentially produce some output
    - describes CI/CD workflow
    - creates several Pods and runs them in a sequence or simultaneously as directed by the Pipeline code   
- Workspaces
   - is a shared volume that can be used across all Tasks running in a Pipeline
- TasksRuns
   - is an instance of Task
   - used to execute a Task in a cluster
- PipelineRuns
   - is an instance of Pipeline
   - represents how Pipeline is executed
   - Once PipelineRun is statted, it automatically creates a TaskRun for each Task in the Pipeline
   - The respective status of Tasks are stored in the TaskRun
   - The respective status of Pipeline is stored in the PipelineRun

### Creating your first Tekton Task in OpenShift

hello.yml

```
apiVersion: tekton.dev/v1beta1
kind: Task
metadata:
  name: hello
spec:
  steps:
    - image: registry.access.redhat.com/ubi8/ubi-minimal
      command:
        - /bin/bash
        - -c
        - echo "Hello World"
```
You may now create the hello Task in OpenShift.
```
oc apply -f ./hello.yml
```
You may list the tasks as
```
tkn task ls
```
You may run the Task as
```
tkn task start hello --showlog
```

### Writing Task with multiple Steps

task-with-multiple-steps.yml

```
apiVersion: tekton.dev/v1beta1
kind: Task
metadata:
  name: multiple-steps
spec
  steps:
    - name: First Step
      image: registry.access.redhat.com/ubi8/ubi-minimal
      command:
        - /bin/bash
        - -c
        - echo "Running First Step ..."
    - name: Second Step
      image: alpine
      command:
        - /bin/sh
        - c
        - echo "Running Second Step ..."
    - name: Third Step
      image: alpine
      command:
        - /bin/sh
        - c
        - echo "Running Third Step ..."
```  

Now let's create the Task 
```
oc apply -f ./task-with-multiple-steps.yml
```

List the task
```
tkn task ls
```

Run the task
```
tkn task start multiple-steps --showlog
```

### Parametering your Task with a single param
task-with-single-param.yml

```
apiVersion: tekton.dev/v1beta1
kind: Task
metadata:
  name: task-with-single-param
spec
  params:
    - name: who
      type: string
  steps:
    - name: Hello
      image: registry.access.redhat.com/ubi8/ubi-minimal
      command:
        - /bin/bash
        - -c
        - echo "Hello $(params.who)"
```

Now let's create the Task 
```
oc apply -f ./task-with-single-param.yml
```

List the task
```
tkn task ls
```

Run the task
```
tkn task start task-with-single-param --showlog
```

You can also pass values as shown below
```
tkn task start task-with-single-param --showlog -p who="Tekton !"
```

### Parametering your Task with a multiple params
task-with-multiple-params.yml

```
apiVersion: tekton.dev/v1beta1
kind: Task
metadata:
  name: task-with-multiple-params
spec
  params:
    - name: greeting
      type: string
  
    - name: who
      type: string
  steps:
    - name: Greet
      image: registry.access.redhat.com/ubi8/ubi-minimal
      command:
        - /bin/bash
        - -c
        - echo "$(params.greeting) $(params.who)"
```

Now let's create the Task 
```
oc apply -f ./task-with-multiple-params.yml
```

List the task
```
tkn task ls
```

Run the task
```
tkn task start task-with-multiple-params --showlog
```

You can also pass values as shown below
```
tkn task start task-with-multiple-params --showlog -p greeting="Hello " -p who="Tekton !"
```

### Adding a default value to Task parameter
task-with-default-value-to-params.yml

```
apiVersion: tekton.dev/v1beta1
kind: Task
metadata:
  name: task-with-default-values-to-params
spec
  params:
    - name: greeting
      type: string
      default: Hello
  
    - name: who
      type: string
      default: Tekton !
  steps:
    - name: Greet
      image: registry.access.redhat.com/ubi8/ubi-minimal
      command:
        - /bin/bash
        - -c
        - echo "$(params.greeting) $(params.who)"
```

Now let's create the Task 
```
oc apply -f ./task-with-default-values-to-params.yml
```

List the task
```
tkn task ls
```

Run the task
```
tkn task start task-with-default-values-to-params --showlog
```

You can also pass values as shown below
```
tkn task start task-with-default-values-to-params --showlog
```

### Troubleshooting TaskRun
failing-task.yml

```
apiVersion: tekton.dev/v1beta1
kind: Task
metadata:
  name: failing-task
spec:
  steps:
    - image: invalid-container-image
      command:
         - echo "Forget it, this won't show up"
```

Let's run the task
```
oc apply -f ./failing-task.yml
tkn task start failing-task
```

You can then troubleshoot this way
```
oc get tr failing-run-xxyyzz -o yaml
```
You need to find the TaskRun pod name and replace "failing-run-xxyyzz" with "your-taskrun-podname"

Look for the reason section to understand the reason for the failure.

