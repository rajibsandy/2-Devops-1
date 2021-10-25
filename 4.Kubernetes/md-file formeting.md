[Redirection](https://google.com/) ==>  Redirect any text

* bullet point  ==> ullet point

**Note:**  ==> 

**Note:** Minikube is a combination of `boot2docker`, `docker-machine`, and `kubeadm`.

**exmple:** 

| ![images/kubernetes-storage.png](images/kubernetes-storage.png) |
| --------------------------------------------------------------- |

**exmple:** 

**Note:** In the diagram above, it says:
> One PVC *can* be shared between multiple pods of a Deployment.
**exmple:** 

Remember, you **cannot** use a storage class to provision PVs *manually*. Storage class is only used for **dynamic provisioning** (discussed next)

**exmple:** 

```
$ kubectl get pods -o wide -w
NAME                         READY   STATUS              RESTARTS   AGE     IP           NODE                                          NOMINATED NODE   READINESS GATES
multitool-69d6b7fc59-gbghn   1/1     Running             0          9m16s   10.44.2.72   gke-dcn-cluster-2-default-pool-4955357e-txm7   <none>           <none>
nginx-7b874889c6-2zdxf       1/1     Running             0          9m34s   10.44.0.43   gke-dcn-cluster-2-default-pool-4955357e-8rnp   <none>           <none>
nginx-7b874889c6-w5lj9       0/1     ContainerCreating   0          36s     <none>       gke-dcn-cluster-2-default-pool-4955357e-txm7   <none>           <none>
```