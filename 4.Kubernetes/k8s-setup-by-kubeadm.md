### Step 1 - Start your vagrant box

`
vim /Vagrantfile
`


```
Vagrant.configure("2") do |config|
  config.vm.define "kmaster" do |kmaster|
    kmaster.vm.box_download_insecure = true
    kmaster.vm.box = "hashicorp/bionic64"
    kmaster.vm.network "private_network", ip: "100.0.0.1"
    kmaster.vm.hostname = "kmaster"
    kmaster.vm.provider "virtualbox" do |v|
      v.name = "kmaster"
      v.memory = 2048
      v.cpus = 1
    end
  end

  config.vm.define "kworker" do |kworker|
    kworker.vm.box_download_insecure = true
    kworker.vm.box = "hashicorp/bionic64"
    kworker.vm.network "private_network", ip: "100.0.0.2"
    kworker.vm.hostname = "kworker"
    kworker.vm.provider "virtualbox" do |v|
      v.name = "kworker"
      v.memory = 1024
      v.cpus = 1
    end
  end


end

```

```
vagrant up 
```
### Step 2 - Update host files on both master and worker node
```
vagrant ssh master
```
***For all vms
```
sudo vi /etc/hosts
100.0.0.1 master.jhooq.com master
100.0.0.2 worker.jhooq.com worker
```
***For all vms
```
ping worker or ip
ping master
```
### Step 3 - Install Docker on both master and worker node
 **install Docker all vms
```
sudo apt-get update
sudo apt install docker.io
sudo systemctl enable docker
Created symlink /etc/systemd/system/multi-user.target.wants/docker.service → /lib/systemd/system/docker.service.
sudo systemctl start  docker
sudo systemctl status docker
```
### Step 4 - Disable the firewall and turnoff the “swapping”
```
sudo systemctl status docker
sudo ufw disable
sudo swapoff -a
```
### Step 5 - Install “apt-transport-https” package
``` 
sudo apt-get update && sudo apt-get install -y apt-transport-https 
```
### Step 6 - Download the public keys
``` 
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add - 
```
### Step 7 - Add kubernetes repo
``` 
sudo bash -c 'echo "deb http://apt.kubernetes.io/ kubernetes-xenial main" > /etc/apt/sources.list.d/kubernetes.list' 
```
### Step 8 - Install kubernetes
``` 
sudo apt-get update && sudo apt-get install -y kubelet kubeadm kubectl 
```

### Step 9 - Enable and Start kubelet
``` 
sudo systemctl enable kubelet
sudo systemctl start kubelet 
````
### Step 10 - Initialize the kubernetes cluster
```
sudo kubeadm init --apiserver-advertise-address=100.0.0.1
(Note : - Followig command will be different for you, do not try copy the following command)
sudo kubeadm join 100.0.0.1:6443 --token g2bsw7.5xr3bqc21eqyc6r7 --discovery-token-ca-cert-hash sha256:39b2b0608b9300b3342a8d0a0e9204c8fc74d45b008043a810f94e4f1fb8861f
```

### Step 11 - Move kube config file to current user (only run on master)
```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

### Step 12 - Apply CNI from kube-flannel.yml(only run on master)
```
wget https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml 
```

### Step 13 - Join worker nodes to master(only run on worker)
```
sudo kubeadm join 100.0.0.1:6443 --token g2bsw7.5xr3bqc21eqyc6r7     --discovery-token-ca-cert-hash sha256:39b2b0608b9300b3342a8d0a0e9204c8fc74d45b008043a810f94e4f1fb8861f
```

### Step 14 - Check the nodes status(only run on master)
``` 
kubectl get nodes 
```

### Troubleshooting kube-flannel.yml
```ip a s
vi kube-flannel.yml
Searche for - “flanneld”

In the args section add : - -iface=eth1

- --iface=eth1
        args:
        - --ip-masq
        - --kube-subnet-mgr
        - --iface=eth1



kubectl apply -f kube-flannel.yml


```
