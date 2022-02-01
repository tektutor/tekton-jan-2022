### Installing Code Ready Containers in Linux
```
cd /home/alchemy/Downloads
tar xvf crc-linux-amd64.tar.xz
cd crc-linux-1.38.0-amd64
./crc setup
./crc start
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

### Login to CRC Cluster via CLI
```
eval $(./crc oc-env)
oc login -u developer https://api.crc.testing:6443
```
