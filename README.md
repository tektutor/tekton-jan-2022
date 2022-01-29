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

### Installing Openshift(Code Ready Containers-CRC)
```
cd ~/Downloads
tar xvf crc-linux-amd64.tar.xz
cd crc-linux-1.38.0-amd64/
./crc setup
./crc start
```

The expected output is
<pre>
[jegan@tektutor crc-linux-1.38.0-amd64]$ ./crc start
INFO Checking if running as non-root              
INFO Checking if running inside WSL2              
INFO Checking if crc-admin-helper executable is cached 
INFO Checking for obsolete admin-helper executable 
INFO Checking if running on a supported CPU architecture 
INFO Checking minimum RAM requirements            
INFO Checking if crc executable symlink exists    
INFO Checking if Virtualization is enabled        
INFO Checking if KVM is enabled                   
INFO Checking if libvirt is installed             
INFO Checking if user is part of libvirt group    
INFO Checking if active user/process is currently part of the libvirt group 
INFO Checking if libvirt daemon is running        
INFO Checking if a supported libvirt version is installed 
INFO Checking if crc-driver-libvirt is installed  
INFO Checking crc daemon systemd socket units     
INFO Checking if systemd-networkd is running      
INFO Checking if NetworkManager is installed      
INFO Checking if NetworkManager service is running 
INFO Checking if /etc/NetworkManager/conf.d/crc-nm-dnsmasq.conf exists 
INFO Checking if /etc/NetworkManager/dnsmasq.d/crc.conf exists 
INFO Checking if libvirt 'crc' network is available 
INFO Checking if libvirt 'crc' network is active  
INFO Starting CodeReady Containers VM for OpenShift 4.9.12... 
INFO CodeReady Containers instance is running with IP 192.168.130.11 
INFO CodeReady Containers VM is running           
INFO Check internal and public DNS query...       
INFO Check DNS query from host...                 
INFO Verifying validity of the kubelet certificates... 
INFO Starting OpenShift kubelet service           
INFO Waiting for kube-apiserver availability... [takes around 2min] 
INFO Waiting for user's pull secret part of instance disk... 
INFO Starting OpenShift cluster... [waiting for the cluster to stabilize] 
INFO 2 operators are progressing: openshift-controller-manager, operator-lifecycle-manager-packageserver 
INFO 2 operators are progressing: openshift-controller-manager, operator-lifecycle-manager-packageserver 
INFO 3 operators are progressing: kube-apiserver, openshift-controller-manager, operator-lifecycle-manager-packageserver 
INFO 2 operators are progressing: kube-apiserver, openshift-controller-manager 
INFO Operator authentication is not yet available 
INFO Operator authentication is not yet available 
INFO All operators are available. Ensuring stability... 
INFO Operators are stable (2/3)...                
INFO Operators are stable (3/3)...                
INFO Adding crc-admin and crc-developer contexts to kubeconfig... 
Started the OpenShift cluster.

The server is accessible via web console at:
  https://console-openshift-console.apps-crc.testing

Log in as administrator:
  Username: kubeadmin
  Password: B8XxM-aY9yz-zhwJY-5HU7d

Log in as user:
  Username: developer
  Password: developer

Use the 'oc' command line interface:
  $ eval $(crc oc-env)
  $ oc login -u developer https://api.crc.testing:6443
</pre>

You may login now as shown below

<pre>
[jegan@tektutor crc-linux-1.38.0-amd64]$ eval $(./crc oc-env)
[jegan@tektutor crc-linux-1.38.0-amd64]$ oc login -u developer https://api.crc.testing:6443
Logged into "https://api.crc.testing:6443" as "developer" using existing credentials.

You don't have any projects. You can try to create a new project, by running

    oc new-project <projectname>
</pre>

### Troubleshooting CRC start
OpenShift creates a Virtual Machine
OpenShift CRC requires 
- Virtualization enabled on the BIOS
- 8 GB RAM
- 4 Virtual CPU cores
- 35 GB storage, you may need more if you wish to install other operators
Make sure your base machine where CRC is installed has more than 4 vCPUs, atleast 8 vCPUs, 16 GB RAM and 250~300 GB HDD(storage)

At times, crc start get's struck due to memory/disk pressure,etc.  In such cases, you just need to
```
./crc stop
./crc start
```

### Installing Tekton in Openshift
```
oc new-project tekton-pipelines --display-name='Tekton Pipelines'
oc adm policy add-scc-to-user anyuid -z tekton-pipelines-controller
oc apply --filename https://storage.googleapis.com/tekton-releases/latest/release.yaml
```
You may now check any pods are running in the namespace tekton-pipelines
```
oc get pods --namespace tekton-pipelines --watch
```


### Installing Tekton in Kubernetes Cluster
```
kubectl apply -f https://storage.googleapis.com/tekton-releases/pipeline/latest/release.yaml
```


### Installing Tekton Dashboard
```
kubectl apply -f https://github.com/tektoncd/dashboard/releases/latest/download/tekton-dashboard-release.yaml
```
Expected output is
<pre>
[root@master ~]# kubectl apply -f https://github.com/tektoncd/dashboard/releases/latest/download/tekton-dashboard-release.yaml
customresourcedefinition.apiextensions.k8s.io/extensions.dashboard.tekton.dev created
serviceaccount/tekton-dashboard created
role.rbac.authorization.k8s.io/tekton-dashboard-info created
clusterrole.rbac.authorization.k8s.io/tekton-dashboard-backend created
clusterrole.rbac.authorization.k8s.io/tekton-dashboard-tenant created
rolebinding.rbac.authorization.k8s.io/tekton-dashboard-info created
clusterrolebinding.rbac.authorization.k8s.io/tekton-dashboard-backend created
configmap/dashboard-info created
service/tekton-dashboard created
deployment.apps/tekton-dashboard created
clusterrolebinding.rbac.authorization.k8s.io/tekton-dashboard-tenant created
</pre>

Accessing the Tekton dashboard
```
kubectl --namespace tekton-pipelines port-forward svc/tekton-dashboard 9097:9097
```
From your favourite web browser you can access the Tekton Dashboard @ http://localhost:9097
