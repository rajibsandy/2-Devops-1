### Vagrant automation

## Pre-requisites
If you want to try this in a virtualized environment on your workstation
* Virtualbox installed
* Vagrant installed
* Host machine has atleast 8 cores
* Host machine has atleast 8G memory

## Bring up all the virtual machines
```
vagrant up
```

##### Copy SSH Keys to all the kubernetes nodes
The root password is **kubeadmin**

```
root@172.16.16.100
root@172.16.16.101
root@172.16.16.102

```

## Downloading kube config to your local machine
On your host machine
```
mkdir ~/.kube
```

## Verifying the cluster

```
kubectl cluster-info
kubectl get nodes
kubectl get cs

```

Have Fun!!
