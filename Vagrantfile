# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
    config.vm.box = "opensuse/Tumbleweed.x86_64"
    config.vm.network "forwarded_port", guest: 6443, host: 6443
    config.vm.network "forwarded_port", guest: 30000, host: 30000
    config.vm.network "private_network", type: "dhcp"
    config.vm.provider "virtualbox" do |v|
        v.memory = 4092
        v.cpus = 2
      end
    config.vm.provision "shell", inline: <<-SHELL
        zypper --non-interactive dup --no-recommends
        zypper --non-interactive install --no-recommends cri-o cri-tools kubernetes-kubeadm kubernetes-client podman wget htop net-tools-deprecated

        modprobe overlay
        modprobe br_netfilter
        echo "net.ipv4.ip_forward = 1" >> /etc/sysctl.conf 
        echo "net.ipv4.conf.all.forwarding = 1" >> /etc/sysctl.conf
        echo "net.bridge.bridge-nf-call-iptables = 1" >> /etc/sysctl.conf
        sysctl -p
    
        systemctl enable kubelet
        systemctl start kubelet

        kubeadm config images pull
        kubeadm init

        mkdir -p $HOME/.kube
        cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
        chown $(id -u):$(id -g) $HOME/.kube/config

        sed 's#https://.*#https://localhost:6443#g' /etc/kubernetes/admin.conf > /vagrant/kubeconfig/single-node-k8s-cluster.conf

        wget https://docs.projectcalico.org/manifests/calico.yaml
        kubectl apply -f calico.yaml

        kubectl taint nodes --all node-role.kubernetes.io/control-plane-

        kubectl apply -f /vagrant/nginx-example-deployment.yaml

        kubectl get nodes
        kubectl get pods --all-namespaces
        
    SHELL
  end
