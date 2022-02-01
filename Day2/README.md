### Installing kubectl
```
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x ./kubectl
sudo mv ./kubectl /usr/bin
```
When prompted for password, type welcome as the administrator password in your Ubuntu Lab machine.

### Installing Code Ready Containers in Linux
```
cd /home/alchemy/Downloads
tar xvf crc-linux-amd64.tar.xz
cd crc-linux-1.38.0-amd64
./crc setup
```

The expected output is
<pre>
jegan@ubuntu:~/Downloads/crc-linux-1.38.0-amd64$ ./crc setup
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
INFO Installing crc-driver-libvirt                
INFO Checking crc daemon systemd service          
INFO Setting up crc daemon systemd service        
INFO Checking crc daemon systemd socket units     
INFO Setting up crc daemon systemd socket units   
INFO Checking if AppArmor is configured           
INFO Updating AppArmor configuration              
INFO Using root access: Updating AppArmor configuration 
[sudo] password for jegan: 
INFO Using root access: Changing permissions for /etc/apparmor.d/libvirt/TEMPLATE.qemu to 644  
INFO Checking if systemd-networkd is running      
INFO Checking if NetworkManager is installed      
INFO Checking if NetworkManager service is running 
INFO Checking if dnsmasq configurations file exist for NetworkManager 
INFO Checking if the systemd-resolved service is running 
INFO Checking if /etc/NetworkManager/dispatcher.d/99-crc.sh exists 
INFO Writing NetworkManager dispatcher file for crc 
INFO Using root access: Writing NetworkManager configuration to /etc/NetworkManager/dispatcher.d/99-crc.sh 
INFO Using root access: Changing permissions for /etc/NetworkManager/dispatcher.d/99-crc.sh to 755  
INFO Using root access: Executing systemctl daemon-reload command 
INFO Using root access: Executing systemctl reload NetworkManager 
INFO Checking if libvirt 'crc' network is available 
INFO Setting up libvirt 'crc' network             
INFO Checking if libvirt 'crc' network is active  
INFO Starting libvirt 'crc' network               
INFO Checking if CRC bundle is extracted in '$HOME/.crc' 
INFO Checking if /home/jegan/.crc/cache/crc_libvirt_4.9.12.crcbundle exists 
INFO Extracting bundle from the CRC executable    
INFO Ensuring directory /home/jegan/.crc/cache exists 
INFO Extracting embedded bundle crc_libvirt_4.9.12.crcbundle to /home/jegan/.crc/cache 
INFO Uncompressing crc_libvirt_4.9.12.crcbundle   
crc.qcow2: 11.69 GiB / 11.69 GiB [------------------------------------------------------] 100.00%
oc: 117.16 MiB / 117.16 MiB [-----------------------------------------------------------] 100.00%
Your system is correctly setup for using CodeReady Containers, you can now run 'crc start' to start the OpenShift cluster
</pre>

### Starting your local CRC OpenShift Cluster
```
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
[jegan@tektutor crc-linux-1.38.0-amd64]$ 
</pre>
when the crc prompts for pull secret, you need to paste the content of pull-secret.txt and hit enter.

### Troubleshooting CRC start
It is commonly noticed that ./crc start command fails many times. 

Make sure

1. Virtualization is enabled (VT-X/AMD-V)
2. You have sufficient RAM in the system atleast 16GB or more
3. You have alteast 8 vCPU in your system

Try to stop and start
```
./crc stop
./crc start
```

### Login to CRC Cluster as a developer via CLI
```
eval $(./crc oc-env)
oc login -u developer https://api.crc.testing:6443
```

### Login to CRC Cluster as an administrator via CLI
```
eval $(./crc oc-env)
oc login -u kubeadmin https://api.crc.testing:6443
```

### Listing projects in Openshift
```
oc get projects
```

### Checking your currently active project
```
oc project
```

### Switching to an existing project
```
oc project <project-name>
```

### Creating a new project
In order to deploy any applications, you should first create a project in OpenShift.
```
oc new-project tektutor
```
Name of the project is user-defined, you may replace 'tektutor' with something you wish.

### Delete a project
This will delete all applications deployed within the project also. So think twice, before your do it.
```
oc delete project tektutor
```

### Deploying a ruby-on-rails sample application within your project
The assumption is you already created a project and you switched to that project already.
```
oc new-app rails-postgresql-example
```
The expected output is
<pre>
jegan@ubuntu:~/Downloads/crc-linux-1.38.0-amd64$ oc new-app rails-postgresql-example
--> Deploying template "openshift/rails-postgresql-example" to project tektutor

     Rails + PostgreSQL (Ephemeral)
     ---------
     An example Rails application with a PostgreSQL database. For more information about using this template, including OpenShift considerations, see https://github.com/sclorg/rails-ex/blob/master/README.md.
     
     WARNING: Any data stored will be lost upon pod destruction. Only use this template for testing.

     The following service(s) have been created in your project: rails-postgresql-example, postgresql.
     
     For more information about using this template, including OpenShift considerations, see https://github.com/sclorg/rails-ex/blob/master/README.md.

     * With parameters:
        * Name=rails-postgresql-example
        * Namespace=openshift
        * Memory Limit=512Mi
        * Memory Limit (PostgreSQL)=512Mi
        * Git Repository URL=https://github.com/sclorg/rails-ex.git
        * Git Reference=
        * Context Directory=
        * Application Hostname=
        * GitHub Webhook Secret=Kme84jEsgb26ttBexijED3FeV3qXr2YnJG4TUrnx # generated
        * Secret Key=ngr720y6pmlrtqkv5adua5ya8p6tmlegctv7ricv8m5kkcmlepv27b5kw6d21yxkk8v4oey5nuv7fv7qlvgc2it3k30efd7q66mdrgwva8e5rtyjq2seyrpsqhuvlp3 # generated
        * Application Username=openshift
        * Application Password=secret
        * Rails Environment=production
        * Database Service Name=postgresql
        * Database Username=user0D3 # generated
        * Database Password=beBlQecG # generated
        * Database Name=root
        * Maximum Database Connections=100
        * Shared Buffer Amount=12MB
        * Custom RubyGems Mirror URL=

--> Creating resources ...
    secret "rails-postgresql-example" created
    service "rails-postgresql-example" created
    route.route.openshift.io "rails-postgresql-example" created
    imagestream.image.openshift.io "rails-postgresql-example" created
    buildconfig.build.openshift.io "rails-postgresql-example" created
    deploymentconfig.apps.openshift.io "rails-postgresql-example" created
    service "postgresql" created
    deploymentconfig.apps.openshift.io "postgresql" created
--> Success
    Access your application via route 'rails-postgresql-example-tektutor.apps-crc.testing' 
    Build scheduled, use 'oc logs -f buildconfig/rails-postgresql-example' to track its progress.
    Run 'oc status' to view your app.
</pre>

You can check the application status
```
oc status
```
The expected output is
<pre>
jegan@ubuntu:~/Downloads/crc-linux-1.38.0-amd64$ oc status
In project tektutor on server https://api.crc.testing:6443

svc/postgresql - 10.217.5.60:5432
  dc/postgresql deploys openshift/postgresql:12-el8 
    deployment #1 running for 20 seconds - 0/1 pods

http://rails-postgresql-example-tektutor.apps-crc.testing (svc/rails-postgresql-example)
  dc/rails-postgresql-example deploys istag/rails-postgresql-example:latest <-
    bc/rails-postgresql-example source builds https://github.com/sclorg/rails-ex.git on openshift/ruby:2.6-ubi8 
      build #1 pending for 21 seconds
    deployment #1 waiting on image or update

View details with 'oc describe <resource>/<name>' or list resources with 'oc get all'.
</pre>

### Login to the Web console and view your application as a developer
From your Ubuntu VM Firefox Web browser, launch this url  https://console-openshift-console.apps-crc.testing
Login credentials are
<pre>
Username - developer
Password - developer
</pre>

Click on Topology to view the Ruby on Rails web application.  This is 2 tier application which has a frontend component and postgress db backend component. If you click on the arrow that shows up in the Topology (Ruby apps), you can 

### Deploying an user-defined application into OpenShift via Dockerfile from GitHub
Assuming you have already created a project.
From the OpenShift Webconsole, select Add and then Select From Git.
Type this URL https://github.com/tektutor/spring-ms.git

### Deploying an user-defined application into OpenShift S2I given a GitHub URL
Assuming you have already created a project.
From the OpenShift Webconsole, select Add and then Select From Git.
Type this URL https://github.com/tektutor/hello-spring-boot.git
