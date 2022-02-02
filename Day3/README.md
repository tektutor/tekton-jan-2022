### What is a K8s Custom Resource?
 - Kubernetes allows us to extend the K8s APIs by creating Custom Resources
 - Just like Pod, ReplicaSet and Deployments exist in Kubernetes we could create our own user-defined Custom Resources
   that can be managed using kubectl or oc in openshift

### What is a K8s Custom Resource Definition?
 - In order to create a Custom Resource, we need to write Custom Resource Definitions in a YAML/JSON as declarative code

### Why we need Custom Controllers to manage Custom Resources?
 - As Custom Resources(CR) are user-defined built-in Controllers doesn't know how to manage CRs 
 - hence we need to write our very own Controllers to manage them so that we can manange them via 
   kubectl or oc client tools

### What is a Kubernetes Operator?
Kubernetes Operator = Custom Resource + Custom Controller

Stateless applications in Kubernetes are deployed, monitored and healed automatically as these applications doesn't have to deal with states i.e data.

Stateful applications are associated with a data that are read, updated, deleted, etc.,
When a cluster of such applications are deployed into Kubernetes, it isn't possible for Kubernetes to automatically
heal that when one of more replicas of the instances becomes unresponsive.  Hence, generally an expert who knows about the application better manages that manually.

For example: 
   Deploying a Cluster of mongodb databases into K8s/Openshift, it isn't as straight forward as a stateless application.  
   Updating data on one instance of mongodb instance has to automaticallly synchronize data on all other mongodb
   instances.

   If one instance of mongodb aborts, it should be replaced by another new golden instance of mongodb.

All these stuffs, requires a manual expert knowledge. May be Database Administrator who is an in MongoDB administrations knows this pretty well but Kubernetes can't automate all this by itself.  

Kubernetes Operators - automate all these operations that a System Administrator or SRE or DevOps or a DBA will do by writing Custom Resource Definitions to create Custom Resource.  Custom Controller then manages the Custom Resource.
i.e Use software to automate your software application management

### What is Operator Framework?
Developing Kubernetes Operator involves writing lot of code, typically in Go programming language.  RedHat Operator SDK makes this process easier by generating repetitive common boiler plate code, allowing us to focus on our specific requirement. 

### What is Operator Lifecycle Manager (OLM)?
 - is an Operator that installs, manages and upgrades other Operators.

### What is Operator Metering?
 - a metrics system that accounts for Operators' use of cluster resources.
 - Budgeting, Billing, Metrics aggregation

### What is an Operator Maturity Model?

Phase 1 - Basic Installation
 - Automated application provisioning and configuration Management
 - Can be done using Helm, Ansible/Puppet/Chef/Salt or Go

Phase 2 - Seamless Upgrades
 - Patch and minor version upgrades
 - Can be done using Helm, Ansible/Puppet/Chef/Salt or Go

PHase 3 - Full Lifecycle
 - App lifecycle, storage lifecycle ( backup, failure and recovery )
 - Can be done Using Configuration Management Tools (Ansible, Puppet, Chef or Salt )
 - Can also done in Go

Phase 4 - Deep Insights
 - Metrics, alerts, log processing and worload analysis
 - Can be automated Using Configuration Management Tools (Ansible, Puppet, Chef or Salt )
 - Can also be automated in Go

Phase 5 - Auto Pilot
 - Horizontal/Vertical scaling
 - auto config tuning
 - abnormal detection
 - schedule tuning
 - Can be automated Using Configuration Management Tools (Ansible, Puppet, Chef or Salt )
 - Can also be automated in Go

### Installing etcd Operator and deploy etcd cluster
Install Operator Lifecycle Manager (OLM)
```
curl -sL https://github.com/operator-framework/operator-lifecycle-manager/releases/download/v0.20.0/install.sh | bash -s v0.20.0
```

Install the Grafana operator by running the following command
```
kubectl create -f https://operatorhub.io/install/grafana-operator.yaml
```

Watch your etcd operator coming up
```
kubectl get csv -n my-grafana-operator
```
