# single-node-k8s-cluster
Single node k8s cluster based on openSUSE tumbleweed running on Vagrant virtual machine with example deployment.

This Vagrantfile is inspired by https://tuanpembual.wordpress.com/2020/10/15/run-opensuse-kubic-like-k8s-podman-cri-o-on-alibaba-cloud/ - thank you!

## How to use
### Required software
- Install Virtualbox https://www.virtualbox.org/wiki/Downloads
- Install Vagrant https://www.vagrantup.com/downloads

### Start new k8s cluster
In project directory run ```vagrant up```

### Check if cluster is up and running
A sample service should be created and running on the cluster, you can check if it is running.
- by logging to cluster node via ssh `vagrant ssh` and use `sudo su -`, `kubectl get nodes` and `kubectl get pods --all-namespaces` commands
```
single-node-k8s-cluster$ vagrant ssh
Have a lot of fun...
vagrant@localhost:~> sudo su -
localhost:~ # kubectl get nodes
NAME        STATUS   ROLES           AGE   VERSION
localhost   Ready    control-plane   16m   v1.28.2
localhost:~ # kubectl get pods --all-namespaces
NAMESPACE     NAME                                       READY   STATUS    RESTARTS   AGE
default       nginx-deployment-7c79c4bf97-42rkp          1/1     Running   0          15m
default       nginx-deployment-7c79c4bf97-jm4wv          1/1     Running   0          15m
kube-system   calico-kube-controllers-7ddc4f45bc-bzmdh   1/1     Running   0          15m
kube-system   calico-node-npwbs                          1/1     Running   0          15m
kube-system   coredns-86ccd44ff8-qrm7j                   1/1     Running   0          15m
kube-system   coredns-86ccd44ff8-zdl8q                   1/1     Running   0          15m
kube-system   etcd-localhost                             1/1     Running   0          16m
kube-system   kube-apiserver-localhost                   1/1     Running   0          16m
kube-system   kube-controller-manager-localhost          1/1     Running   0          16m
kube-system   kube-proxy-qk8w7                           1/1     Running   0          16m
kube-system   kube-scheduler-localhost                   1/1     Running   0          16m
localhost:~ #
```
- by making HTTP request to deployed service:
```
localhost:~ # curl -I localhost:30000 --silent | head -n 1
HTTP/1.1 200 OK
```
### Accessing services deployed on cluster from guest machine
In Vagrantfile there is below configuration.
```
config.vm.network "forwarded_port", guest: 30000, host: 30000
```
Example service deployed on the cluster exposes port `30000` and that port is forwarded to guest machine.
You can forward another ports.

### Accessing cluster from kubectl from guest machine
To use `kubectl` on guest machine kubeconfig is needed, after cluster creating kubeconfig (`single-node-k8s-cluster.conf`) is copied to `kubeconfig` directory
