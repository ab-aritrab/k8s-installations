Installation by "Data Science Garage": https://www.youtube.com/watch?v=SQJxYhfzznU
https://github.com/vb100/Deploy_Kubernetes_AWS_EC2/blob/main/README.md

```
==========+++++++++=============
STATIC IP:
sudo su
ll /etc/netplan/   //check the name of the file.
sudo cp /etc/netplan/00-installer-config.yaml /etc/netplan/00-installer-config.yaml.BKP  //backup once
sudo vim /etc/netplan/00-installer-config.yaml  //change from DHCP to STATIC
------------------------
# This is the network config written by 'subiquity'
network:
  ethernets:
    ens33:
      dhcp4: true
  version: 2
------------------------
    	TO
------------------------
# This is the network config written by 'subiquity'
network:
  ethernets:
    ens33:
      dhcp4: no
      addresses:
        - 10.50.2.10/16
      gateway4: 10.50.1.2
      nameservers:
          addresses: [8.8.8.8, 1.1.1.1]
  version: 2
---------------------
sudo netplan apply

==========+++++++++=============
SWAPOFF:
sudo swapoff -a; 
sudo vim /etc/fstab
EDIT -
#/swap.img      none    swap    sw      0       0
:wq

==========+++++++++=============
HOSTNAME CHANGES:
sudo hostnamectl set-hostname kmaster1  	// RUN to the server 10.50.2.10
sudo hostnamectl set-hostname knode1 		// RUN to the server 10.50.2.12
sudo hostnamectl set-hostname knode2 		// RUN to the server 10.50.2.13
sudo hostnamectl set-hostname knode3 		// RUN to the server 10.50.2.14

HOSTS ENTRIES: 
10.50.2.10	kmaster1	kmaster1.aritrabiswas.com
10.50.2.12	knode1		knode1.aritrabiswas.com
10.50.2.13	knode2		knode2.aritrabiswas.com
10.50.2.14	knode3		knode3.aritrabiswas.com

==========+++++++++=============
Add user & make the user to perform SUDO job:  //all of the servers

sudo adduser ansadmin   // password: Passw0rd12
usermod -aG sudo ansadmin

==========+++++++++=============
sudo hostnamectl set-hostname jenkins 		// RUN to the server 10.50.1.51
sudo hostnamectl set-hostname ansible		// RUN to the server 10.50.1.52

10.50.1.51      jenkins     jenkins-svr.aritrabiswas.com
10.50.1.52      ansible     ansible-svr.aritrabiswas.com

#----add above lines to ALL of the K8S (masters & nodes)

==========+++++++++=============
KUBERNATES INSTALLATION:

TO ALL NODES (IN CLUSTER INCLUDING MASTER)
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add
sudo apt-add-repository "deb http://apt.kubernetes.io/ kubernetes-xenial main"

sudo apt-get install kubeadm kubelet kubectl
sudo apt-mark hold kubeadm kubelet kubectl

kubeadm version

-------------------
cd /etc/docker/
sudo touch daemon.json 
vim daemon.json         //add below json lines

{
"exec-opts": ["native.cgroupdriver=systemd"]
}

-----------------

sudo systemctl daemon-reload
sudo systemctl restart docker
sudo kubeadm reset


*** Master node only ***
sudo kubeadm init --pod-network-cidr=10.244.0.0/16

<<<<<<<<<<---------------------------

ansadmin@kmaster1:~$ sudo kubeadm init --pod-network-cidr=10.244.0.0/16
[init] Using Kubernetes version: v1.23.6
[preflight] Running pre-flight checks
[preflight] Pulling images required for setting up a Kubernetes cluster
[preflight] This might take a minute or two, depending on the speed of your internet connection
[preflight] You can also perform this action in beforehand using 'kubeadm config images pull'

[certs] Using certificateDir folder "/etc/kubernetes/pki"
[certs] Generating "ca" certificate and key
[certs] Generating "apiserver" certificate and key
[certs] apiserver serving cert is signed for DNS names [kmaster1 kubernetes kubernetes.default kubernetes.default.svc kubernetes.default.svc.cluster.local] and IPs [10.96.0.1 10.50.2.10]
[certs] Generating "apiserver-kubelet-client" certificate and key
[certs] Generating "front-proxy-ca" certificate and key
[certs] Generating "front-proxy-client" certificate and key
[certs] Generating "etcd/ca" certificate and key
[certs] Generating "etcd/server" certificate and key
[certs] etcd/server serving cert is signed for DNS names [kmaster1 localhost] and IPs [10.50.2.10 127.0.0.1 ::1]
[certs] Generating "etcd/peer" certificate and key
[certs] etcd/peer serving cert is signed for DNS names [kmaster1 localhost] and IPs [10.50.2.10 127.0.0.1 ::1]
[certs] Generating "etcd/healthcheck-client" certificate and key
[certs] Generating "apiserver-etcd-client" certificate and key
[certs] Generating "sa" key and public key
[kubeconfig] Using kubeconfig folder "/etc/kubernetes"
[kubeconfig] Writing "admin.conf" kubeconfig file
[kubeconfig] Writing "kubelet.conf" kubeconfig file
[kubeconfig] Writing "controller-manager.conf" kubeconfig file
[kubeconfig] Writing "scheduler.conf" kubeconfig file
[kubelet-start] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[kubelet-start] Starting the kubelet
[control-plane] Using manifest folder "/etc/kubernetes/manifests"
[control-plane] Creating static Pod manifest for "kube-apiserver"
[control-plane] Creating static Pod manifest for "kube-controller-manager"
[control-plane] Creating static Pod manifest for "kube-scheduler"
[etcd] Creating static Pod manifest for local etcd in "/etc/kubernetes/manifests"
[wait-control-plane] Waiting for the kubelet to boot up the control plane as static Pods from directory "/etc/kubernetes/manifests". This can take up to 4m0s
[apiclient] All control plane components are healthy after 8.006226 seconds
[upload-config] Storing the configuration used in ConfigMap "kubeadm-config" in the "kube-system" Namespace
[kubelet] Creating a ConfigMap "kubelet-config-1.23" in namespace kube-system with the configuration for the kubelets in the cluster
NOTE: The "kubelet-config-1.23" naming of the kubelet ConfigMap is deprecated. Once the UnversionedKubeletConfigMap feature gate graduates to Beta the default name will become just "kubelet-config". Kubeadm upgrade will handle this transition transparently.
[upload-certs] Skipping phase. Please see --upload-certs
[mark-control-plane] Marking the node kmaster1 as control-plane by adding the labels: [node-role.kubernetes.io/master(deprecated) node-role.kubernetes.io/control-plane node.kubernetes.io/exclude-from-external-load-balancers]
[mark-control-plane] Marking the node kmaster1 as control-plane by adding the taints [node-role.kubernetes.io/master:NoSchedule]
[bootstrap-token] Using token: kzhvpw.9x5zc1mpizazk6do
[bootstrap-token] Configuring bootstrap tokens, cluster-info ConfigMap, RBAC Roles
[bootstrap-token] configured RBAC rules to allow Node Bootstrap tokens to get nodes
[bootstrap-token] configured RBAC rules to allow Node Bootstrap tokens to post CSRs in order for nodes to get long term certificate credentials
[bootstrap-token] configured RBAC rules to allow the csrapprover controller automatically approve CSRs from a Node Bootstrap Token
[bootstrap-token] configured RBAC rules to allow certificate rotation for all node client certificates in the cluster
[bootstrap-token] Creating the "cluster-info" ConfigMap in the "kube-public" namespace
[kubelet-finalize] Updating "/etc/kubernetes/kubelet.conf" to point to a rotatable kubelet client certificate and key
[addons] Applied essential addon: CoreDNS
[addons] Applied essential addon: kube-proxy

Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

Alternatively, if you are the root user, you can run:

  export KUBECONFIG=/etc/kubernetes/admin.conf

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 10.50.2.10:6443 --token kzhvpw.9x5zc1mpizazk6do \
        --discovery-token-ca-cert-hash sha256:56bbb9990ddecad3865da4e99046eca380ecc282db9cf2b8d1b1995127d9d244
ansadmin@kmaster1:~$
--------------->>>>>>>>>>>>>>>>>>>>>>


**** RUN from ALL Worker Nodes Only ****
Then you can join any number of worker nodes by running the following on each as root:
sudo kubeadm join 10.50.2.10:6443 --token kzhvpw.9x5zc1mpizazk6do --discovery-token-ca-cert-hash sha256:56bbb9990ddecad3865da4e99046eca380ecc282db9cf2b8d1b1995127d9d244

==========+++++++++=============



