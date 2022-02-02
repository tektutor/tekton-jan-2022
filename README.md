### CentOS 8.x has reached End of Life by Dec 2021, hence software installations won't work without the below
```
su -
sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-Linux-*
sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-Linux-*
```


### Create kubernetes secrets for Docker registry
```
kubectl create secret docker-registry regcred --docker-server=https://index.docker.io/v1 --docker-username=<your-docker-login> --docker-password=<your-docker-password> --docker-email=<your-registered-docker-email>
```

List the secrets
```
kubectl get secret
```

### Applying docker registry credentials to deploy
pod.yml
```
apiVersion: v1
kind: Pod
metadata:
  name: docker-registry
spec:
  containers:
    - name: docker-registry
      image: nginx:1.20
  imagePullSecrets:
   - name: regcred
```

```
kubectl apply -f pod.yml
```
List the pod
```
kubectl get po -w
```
