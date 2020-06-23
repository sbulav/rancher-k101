# -*- mode: ruby -*-
# # vi: set ft=ruby :
# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure("2") do |config|
  config.vm.provider :virtualbox do |v|
    v.memory = 2048
    v.cpus = 2
  end

  config.vm.provision :shell, privileged: true, inline: $install_common_tools

  config.vm.define :master do |master|
    master.vm.box = "ubuntu/xenial64"
    master.vm.hostname = "master"
    master.vm.network :private_network, ip: "10.0.0.10"
    # master.vm.provision :shell, privileged: false, run: "once", inline: $provision_master_node
  end

  %w{worker1 worker2}.each_with_index do |name, i|
    config.vm.define name do |worker|
      worker.vm.box = "ubuntu/xenial64"
      worker.vm.hostname = name
      worker.vm.network :private_network, ip: "10.0.0.#{i + 11}"
      worker.vm.provision :shell, privileged: false, run: "once", inline: <<-SHELL
SHELL
    end

  end
  config.vm.provision "shell", inline: $install_multicast
end


# --------------------------------------------------------------------------------------------
# ------------------------------  COMMON TOOLS  ----------------------------------------------
# --------------------------------------------------------------------------------------------
$install_common_tools = <<-SCRIPT
echo ssh-rsa 'insert pub key here' >> /home/vagrant/.ssh/authorized_keys
echo 'export TERM=xterm' >> /home/vagrant/.bashrc
# bridged traffic to iptables is enabled for kube-router.
cat >> /etc/ufw/sysctl.conf <<EOF
net/bridge/bridge-nf-call-ip6tables = 1
net/bridge/bridge-nf-call-iptables = 1
net/bridge/bridge-nf-call-arptables = 1
EOF
sysctl net.bridge.bridge-nf-call-iptables=1

# disable swap
swapoff -a
sed -i '/swap/d' /etc/fstab

# Install kubeadm, kubectl and kubelet
export DEBIAN_FRONTEND=noninteractive
apt-get -qq install ebtables ethtool
apt-get -qq update
apt-get -qq install -y docker.io apt-transport-https curl
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
deb http://apt.kubernetes.io/ kubernetes-xenial main
EOF
apt-get -qq update
apt-get -qq install -y kubelet=1.16.2-00 kubectl=1.16.2-00 kubeadm=1.16.2-00
cat > /etc/docker/daemon.json <<EOF
 {
   "exec-opts": ["native.cgroupdriver=systemd"],
   "log-driver": "json-file",
   "log-opts": {
     "max-size": "100m"
   },
   "storage-driver": "overlay2"
 }
EOF
systemctl daemon-reload
systemctl restart docker

sudo echo "10.0.0.10 master" >> /etc/hosts
sudo echo "10.0.0.11 worker1" >> /etc/hosts
sudo echo "10.0.0.12 worker2" >> /etc/hosts
sudo usermod -aG docker vagrant
SCRIPT

# --------------------------------------------------------------------------------------------
# ------------------------------  MASTER PROVISION  ------------------------------------------
# --------------------------------------------------------------------------------------------
$provision_master_node = <<-SHELL
OUTPUT_FILE=/vagrant/Tools/join.sh
rm -rf $OUTPUT_FILE

sudo cp /vagrant/Tools/etcd-v3.4.0-linux-amd64/etcd* /usr/local/bin/

# Start cluster
sudo kubeadm init --apiserver-advertise-address=10.0.0.10 --pod-network-cidr=10.244.0.0/16 | grep "kubeadm join" > ${OUTPUT_FILE}
echo "--discovery-token-unsafe-skip-ca-verification" >> ${OUTPUT_FILE}
chmod +x $OUTPUT_FILE

# Configure kubectl
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
echo "source <(kubectl completion bash)" >> ~/.bashrc
echo "alias kk=kubectl" >> ~/.bashrc
echo "complete -F __start_kubectl kk" >> ~/.bashrc

# Fix kubelet IP
echo 'Environment="KUBELET_EXTRA_ARGS=--node-ip=10.0.0.10"' | sudo tee -a /etc/systemd/system/kubelet.service.d/10-kubeadm.conf

# # Configure flannel
# curl -o kube-flannel.yml https://raw.githubusercontent.com/coreos/flannel/2140ac876ef134e0ed5af15c65e414cf26827915/Documentation/kube-flannel.yml
# sed -i.bak 's|"/opt/bin/flanneld",|"/opt/bin/flanneld", "--iface=enp0s8",|' kube-flannel.yml
# kubectl create -f kube-flannel.yml

# Install Weave
kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"

sudo systemctl daemon-reload
sudo systemctl restart kubelet

SHELL

# --------------------------------------------------------------------------------------------
# ------------------------------  MULTICAST --------------------------------------------------
# --------------------------------------------------------------------------------------------
$install_multicast = <<-SHELL
apt-get -qq install -y avahi-daemon libnss-mdns
SHELL

# --------------------------------------------------------------------------------------------
# ------------------------------  RUN start.sh to start exam ---------------------------------
# --------------------------------------------------------------------------------------------
