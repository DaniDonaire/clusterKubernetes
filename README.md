# Kubernetes Tutorial

Create a 3 Virtual machines - Ubuntu Server:
- Master
- Node 1
- Node 2

Configure the networks adapters in bridge adapter.

Configure the Ip's of the servers in the same LAN.

I chose these IP's:

- 192.168.0.80
- 192.168.0.81
- 192.168.0.82

Before the configure anything, my recommendation is to install the dependencies in the first machine and clon it.

#Configure the template machine

These are the commands to install de dependencies:

Unable SWAP:
- sudo swapoff -a
Comment the line of SWAP in /etc/fstab
- sudo nano /etc/fstab

Add Google's apt repository gpg key
- curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -

Add the Kubernetes apt repository
(copy and paste with multiline):
- sudo bash -c 'cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF'

Now update the repositories and install docker.io kubelet kubeadm kubectl
- sudo apt-get update
- sudo apt-get install -y docker.io kubelet kubeadm kubectl

Configure the packages are not updated
- sudo apt-mark hold docker.io kubelet kubeadm kubectl

Now enable the kubelet and docker service run where start the SO.
- sudo systemctl enable kubelet.service
- sudo systemctl enable docker.service

# Now clone the machine to save time

#Configure the master

Set the lan of the cluster:
- sudo kubeadm init --pod-network-cidr=192.168.0.0/24

Create the directory of configuration:
- mkdir -p $HOME/.kube
- sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
- sudo chown $(id -u):$(id -g) $HOME/.kube/config

Get the config files (yaml):

- wget https://docs.projectcalico.org/v3.3/getting-started/kubernetes/installation/hosted/rbac-kdd.yaml
- wget https://docs.projectcalico.org/v3.3/getting-started/kubernetes/installation/hosted/kubernetes-datastore/calico-networking/1.7/calico.yaml
 
Apply the files to config the cluster:

- kubectl apply -f rbac-kdd.yaml
- kubectl apply -f calico.yaml

In the master confirm message appears a join command for join the nodes to master, copy it!

#Configure the nodes (repite for any node):

Run the command of confirmation master in first node:
- kubeadm join 192.168.0.80:6443 --token rkcjfb.by1hgolbji6sul2f --discovery-token-ca-cert-hash sha256:11af1f40487203fcf12c07b3726ef23457783f2140a1d6c21a5cafdf08cc10dd

(If is correct, the node is correctly join)

#Confirm the nodes are into the cluster:

I master you can get info with these commands:

- kubectl cluster-info

- kubectl get nodes

- kubectl get pods





