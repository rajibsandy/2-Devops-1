# Let's Set up a HA-Kubernetes Cluster using Rancher RKE
Follow this documentation to set up a highly available Kubernetes cluster on __Ubuntu 20.04 LTS__ machines using Rancher's RKE.

## Vagrant Environment
|Role|FQDN|IP|OS|RAM|CPU|
|----|----|----|----|----|----|
|Master, etcd, worker|node1.example.com|172.16.16.101|Ubuntu 20.04|2G|2|
|Master, etcd, worker|node2.example.com|172.16.16.102|Ubuntu 20.04|2G|2|
|Master, etcd, worker|node3.example.com|172.16.16.103|Ubuntu 20.04|2G|2|

> * Password for the **root** account on all these virtual machines is **kubeadmin**
> * Perform all the commands as root user unless otherwise specified

## Pre-requisites
If you want to try this in a virtualized environment on your workstation
#### Virtualbox installed # Vagrant installed # Host machine 8 cores # Host machine 8G memory

## Bring up all the virtual machines by vagrant


```
sudo mkdir play
sudo cat > bootstrap.sh
```

```
#!/bin/bash

# Enable ssh password authentication
echo "[TASK 1] Enable ssh password authentication"
sed -i 's/^PasswordAuthentication .*/PasswordAuthentication yes/' /etc/ssh/sshd_config
echo 'PermitRootLogin yes' >> /etc/ssh/sshd_config
systemctl reload sshd
# Set Root password
echo "[TASK 2] Set root password"
echo -e "kubeadmin\nkubeadmin" | passwd root >/dev/null 2>&1

```

```
CRTL+D
```

```
sudo cat > Vagrantfile
```

```
# -*- mode: ruby -*-
# vi: set ft=ruby :

ENV['VAGRANT_NO_PARALLEL'] = 'yes'
Vagrant.configure(2) do |config|
  config.vm.provision "shell", path: "bootstrap.sh"
  NodeCount = 3

  # Kubernetes Nodes
  (1..NodeCount).each do |i|
    config.vm.define "node#{i}" do |node|
      node.vm.box = "bento/ubuntu-20.04"
      node.vm.hostname = "node#{i}.example.com"
      node.vm.network "private_network", ip: "172.16.16.10#{i}"
      node.vm.provider "virtualbox" do |v|
        v.name = "node#{i}"
        v.memory = 1024
        v.cpus = 1
      end
    end
  end

end

```
```
CRTL+D
```

```
vagrant up
```

# Enable ssh password authentication
## Set up password less SSH Logins on all nodes
We will be using SSH Keys to login to root account on all the kubernetes nodes. I am not going to set a passphrase for this ssh keypair.
##### Create an ssh keypair on the host machine
```
ssh-keygen -t rsa -b 2048
```
##### Copy SSH Keys to all the kubernetes nodes
The root password is **kubeadmin**
```
ssh-copy-id root@172.16.16.101
ssh-copy-id root@172.16.16.102
ssh-copy-id root@172.16.16.103
```

## Prepare the kubernetes nodes (node1, node2, node3)
```
sudo ssh root@172.16.16.101 
sudo ssh root@172.16.16.102
sudo ssh root@172.16.16.103
password= kubeadmin
```

##### Disable Firewall
```
ufw disable
```
##### Disable swap
```
swapoff -a; sed -i '/swap/d' /etc/fstab
```
##### Update sysctl settings for Kubernetes networking
```
cat >>/etc/sysctl.d/kubernetes.conf<<EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sysctl --system
```
##### Install docker engine
```
{
  apt install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
  curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
  add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
  apt update && apt install -y docker-ce=5:19.03.10~3-0~ubuntu-focal containerd.io
}
```
## Download RKE Binary to host diractory
##### Download the latest release from the Github releases page
[Rancher RKE Releases - Github](https://github.com/rancher/rke/releases)

## Bring up Kubernetes cluster
##### Create cluster configuration
```
sudo wget (dowenlode link)
sudo chmod +x (dowenlode file)
mv (dowenlode file) /usr/bin/....
rke config
```
Once gone through this interactive cluster configuration, you will end up with cluster.yml file in the current directory.

##### Provision the cluster
```
rke up
```
Once this command completed provisioning the cluster, you will have cluster state file (cluster.rkestate) and kube config file (kube_config_cluster.yml) in the current directory.

## Downloading kube config to your local machine
On your host machine
```
mkdir ~/.kube
cp kube_config_cluster.yml ~/.kube/config
```

## Verifying the cluster
```
kubectl cluster-info
kubectl get nodes
kubectl get cs
```

## Delete the cluster
```
rke remove
```

### Have Fun!!
