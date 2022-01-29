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
