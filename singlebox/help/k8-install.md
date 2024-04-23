# Install Containerd Runtime (all nodes)

## install dependencies

sudo apt install -y curl gnupg2 software-properties-common apt-transport-https ca-certificates

## Enable the Docker repository

sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmour -o /etc/apt/trusted.gpg.d/docker.gpg
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"

## Update the package list and install containerd:

sudo apt update
sudo apt install -y containerd.io

## Configure containerd to start using systemd as cgroup:
containerd config default | sudo tee /etc/containerd/config.toml >/dev/null 2>&1
sudo sed -i 's/SystemdCgroup \= false/SystemdCgroup \= true/g' /etc/containerd/config.toml


# Load the required kernel modules on all nodes

sudo tee /etc/modules-load.d/containerd.conf <<EOF
overlay
br_netfilter
EOF
sudo modprobe overlay
sudo modprobe br_netfilter

## Restart and enable the containerd service:

sudo systemctl restart containerd
sudo systemctl enable containerd

That completes the conatinerd runtime install, that is used by k8.

Now you can go ahead and install the k8 as follows:

# Do some prerequisiste

## disable swap
sudo swapoff -a
sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab

# Configure the critical kernel parameters for Kubernetes using the following:

sudo tee /etc/sysctl.d/kubernetes.conf <<EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
EOF

# Then, reload the changes

sudo sysctl --system

## Add Apt Repository for Kubernetes (all nodes)
//the following 2 is not working-
//papu - option 3 is preferred way to do , it works best

option1 
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo gpg --dearmour -o /etc/apt/trusted.gpg.d/kubernetes-xenial.gpg
sudo apt-add-repository "deb http://apt.kubernetes.io/ kubernetes-xenial main"

option2
curl -fsSL https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes.gpg

echo "deb [arch=amd64 signed-by=/etc/apt/keyrings/kubernetes.gpg] http://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list

Option 3
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.29/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.29/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

## Install Kubectl, Kubeadm, and Kubelet (all nodes)

sudo apt update
sudo apt install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl

## Initialize Kubernetes Cluster with Kubeadm (master node)

sudo kubeadm  init --upload-certs

## Run the following commands on the master node
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

## Next, use kubectl commands to check the cluster and node status

kubectl get nodes

## Add Worker Nodes to the Cluster (worker nodes)
kubeadm join 146.190.135.86:6443 --token f1h95l.u4nkex9cw8d0g63w         --discovery-token-ca-cert-hash sha256:6d15f2a79bdb38d1666af50c85f060b9fadc73f13c932e0e2a9eeef08f51f91a

## Install Kubernetes Network Plugin (master node)
//papu - unless this is done , the control plane never gets ready

kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.25.0/manifests/calico.yaml

## Verify the cluster and test (master node)

kubectl get pods -n kube-system
kubectl get nodes

## Deploy test application on cluster (master node)

kubectl run nginx --image=nginx

## How to run pods in master node 
You can taint the master node
(myproject) papu@papu:~$ kubectl get no
NAME   STATUS   ROLES           AGE   VERSION
papu   Ready    control-plane   19h   v1.28.2

kubectl taint nodes --all node-role.kubernetes.io/control-plane-

## To run as root
export KUBECONFIG=/etc/kubernetes/admin.conf

## How to check all stuffs are properly installed 
//papu - when you see all the core services running perfectly ok

![alt text](image.png)



# Some extra tips -

## How to export docker images to crictl discovered image

crictl --image-endpoint unix:///run/containerd/containerd.sock image ls
crictl --image-endpoint  unix:///run/containerd/containerd.sock rmi docker.io/library/hostpath-provisioner
docker save image -o image.tar
ctr -n=k8s.io images import image.tar

## How to debug the service

Run a busybox for debug
kubectl run -i --tty --rm debug --image=busybox --restart=Never -- sh
use netcat tools etc for connecting service





