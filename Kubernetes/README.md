# How To Install Kubernetes

## Install and Deploy Kubernetes on Ubuntu
### Set up Docker 
#### Step 1: Install Docker
  
``` 
sudo apt-get update  
```
If you want to upgrade the packages installed everything, execute the following commands.
```
sudo apt-get dist-upgrade -y
sudo reboot
```
Keep going
```
sudo apt-get install docker.io  
```
Check the installation and version by this the following command.  
  
``` docker --version  ```
  
#### Step 2: Start&Enable Docker

``` 
sudo systemctl enable docker  
sudo systemctl status docker  
sudo systemctl start docker  
```

### Install Kubernetes
#### Step 3: Add K8s signing key
```
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add
```

#### Step 4: Add the repository
```
sudo apt-add-repository "deb http://apt.kubernetes.io/ kubernetes-xenial main"

```

#### Step 5: Kubernetes Installtion Tools
```
sudo apt-get install kubeadm kubelet kubectl
sudo apt-mark hold kubeadm kubelet kubectl
```
Verify the installation with:
```
kubeadm version
```

### Kubernetes deployment
#### Step 6: Begin K8s deployment - Following until here for worker nodes set up
```
sudo swapoff -a

sudo tee /etc/rc.local <<-'EOF'
#!/bin/sh -e
#
# rc.local
#
# This script is executed at the end of each multiuser runlevel.
# Make sure that the script will "exit 0" on success or any other
# value on error.
#
# In order to enable or disable this script just change the execution
# bits.
#
# By default this script does nothing.
sudo swapoff -a

exit 0
EOF

sudo chmod +x /etc/rc.local

sudo sysctl net.bridge.bridge-nf-call-iptables=1


```
#### Step 7: Initialize K8s on Master node
```
sudo kubeadm init --pod-network-cidr=10.200.0.0/16
```
> once this command finishes, it will show a "kubeadm join" message at the end. Make a note of the whole entry. It will be used to join the worker nodes to the cluster.
```
mkdir -p ~/.kube
sudo cp -i /etc/kubernetes/admin.conf ~/.kube/config
sudo chown $(id -u):$(id -g) ~/.kube/config
```
#### Step 8: Deploy Pod Network(calico) to the Cluster
```
kubectl apply -f https://docs.projectcalico.org/v3.3/getting-started/kubernetes/installation/hosted/rbac-kdd.yaml
```
> Before applying below calico network, you should change the IPV4POOL first if you use 192.168.0.0/16 network for the host machines. The default CIDR is "192.168.0.0/16".
```
kubectl apply -f https://docs.projectcalico.org/v3.3/getting-started/kubernetes/installation/hosted/kubernetes-datastore/calico-networking/1.7/calico.yaml
```
Verify that everything is running and communication.
```
kubectl get pods --all-namespaces
```

#### Step 9: Join worker node to the Cluster
execute the command which gat from Step 7, "kubeadm join ...."
```
sudo kubeadm join 192.168.100.199:6443 --token fdh7yr.vgtedvkzf18l0i2a \
    --discovery-token-ca-cert-hash sha256:53..............................
```
Verify that the worker has joined successfully
```
kubectl get nodes
```
