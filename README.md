# rancher-k101
Rancher K101 training files and notes

## Overview

This repo contains guides and notes for [Rancher K101 course](https://academy.rancher.com)

### Installation of RKE

1. Install Vagrant
2. Install rke
3. Clone this repo
4. Create kubernetes master and worker nodes:
```
vagrant up
```
5. Install RKE distribution:
```bash
rke up
```
6. Set up kube config file for current sesstion
```bash
export KUBECONFIG=$(pwd)/kube_config_cluster.yml
```
7. Verify that you can connect to K8S cluster
```
kubectl get nodes
```

## Quiz

## 1. Intro to Rancher and RKE

### 1.2 Discovering RKE

* RKE deploys Kubernetes components as:  Docker containers
* SSH is used to orchestrate RKE across servers: True
* Which is not something required on hosts to run RKE? SELinux is set to
  enforcing
* Cluster.yml contains all the information RKE needs to provision a Kubernetes
  cluster: True
* You can supply your own certificates that Rancher will serve for its UI/API:
  True
* rke start will start the provisioning of Kubernetes.: False
* Which versions of Kubernetes does RKE support by default?: The Latest patch
  releases from the three most recent minor releases

### 1.3 Day Two Operations for RKE

* What are the two files that RKE generates that need to be stored safely?
  cluster.rkestate and kube_config_cluster.yml
* What are the two locations RKE can write backup archives to? S3, Local Disk
* Upgrading Kubernetes involves changing the version in the system_images key
  and running rke upgrade.: False
* Certificates cannot be regenerated after install.: False
* You can add additional nodes to an RKE cluster after initial deployment. :
  True
* Which of the following is NOT something that RKE manages? Determining the
  amount of nodes for a Kubernetes cluster

## 2. Installing and Managing Rancher

### 2.1 Installing Rancher with Docker

* When might you want to use the Docker method for installing Rancher? For
  sandbox or demonstration purposes
* The first step in backing up a single container Docker install is stopping
  the container.: True
* Upgrading Rancher is similar to making a backup except we start a container
  with a new image tag: True
* Why do you want to delete the old Rancher container after an upgrade?:  So it
  doesnt startup accidentally and overwrite the data volume

### 2.2 Installing Rancher with Kubernetes

* Which of the following is a valid type of load balancer when fronting Rancher
  server?: A TCP-based load balancer
* Its possible to migrate from alpha to latest, but not to stable.: False
* Which type of TLS certificate can you use with Rancher?: All of options
* Lets Encrypt doesnt actually use the email address you give them.: False
* How do you make a backup of Rancher in RKE?: Make a normal RKE backup
* You can restore a Rancher/RKE backup with zero downtime.: False
* You can roll back a failed Rancher upgrade with helm rollback.: False

## 3. Deploying Kubernetes With Rancher

### 3.1 Designing and Provisioning Clusters

* Which features are unavailable with Hosted Kubernetes Clusters?: etcd
  backup/restore and some monitoring of etcd
* What is the difference between a Node Template and an RKE Template?:  Node
  templates describe how to provision nodes and RKE templates how to deploy
  Kubernetes
* Where can you find detailed information on the port requirements for each
  role?: Rancher Documentation
* When you create a cluster in Rancher, what RBAC role are you assigned?  Owner

### 3.2 Deploying Kubernetes with Rancher


* Which is true about RKE Templates?: They specify the Kubernetes configuration
  options that should be used
* Node Templates allow you to deploy new nodes without having to re-enter the
  node parameters. True
* What is the purpose of a cloud provider?: To allow Kubernetes to leverage
  cloud-specific features such as load balancers and network storage
* To use the Custom provider only requires that a supported version of Docker
  to be installed on the node.: True
* The command provided by the custom provider can be used repeatedly on each
  node you want to provision for that role.: True
* If you already deployed a Kubernetes cluster, you cant ever import that
  cluster into Rancher.: False
* Which is the best provider if you are provisioning with Terraform?: Custom
  Provider
* Imported clusters have a special import flag that enables you to manage the
  nodes from within Rancher.: False
* The best practice is to colocate etcd, control plane, and worker roles on a
  single node in downstream Kubernetes clusters.: False
* Which network provider works with Windows clusters? Flannel

### 3.3 Performing basic troubleshooting

* How do you check the logs for the Rancher api-server?: Using Kubectl with the
  logs subcommand for one of the pods in the cattle-system namespace 
* Where can you find details on node sensors?: kubectl describe node < node
  name >
* Infrastructure issues on a particular node are often revealed by the kubelet
  logs: True
* Docker daemon logs are useful for understanding if an image cannot be pulled
  for some reason. : True

### 3.4 Performing advanced troubleshooting

* How do you find the logs for etcd in an RKE cluster?:  Using Docker logs
* Control plane nodes only have one active controller manager so log data may
  not be present equally across nodes.: True
* What is the purpose of nginx-proxy?:  So non-control plane nodes can reach
  control plane components without knowing node addresses
* Which log is the best place to start the troubleshooting process?: Rancher
  API server logs
* What is a common cause of CNI failure?:  Network port restrictions between
  nodes

## 4. Managing Kubernetes with Rancher

### 4.1 Editing Clusters

* After provisioning a cluster, you can change the network provider in the
  cluster options on the Edit screen.: False
* In what circumstances should you take a backup?:  Before starting a
  Kubernetes upgrade
* When you update the version of a Kubernetes cluster, it immediately starts
  updating the worker, control plane, and etcd nodes with new components.: True

### 4.2 Using CLI Tools

* Where does the kubectl CLI tool make API calls?:  The Kubernetes API of the
  cluster you are working with
* Where is the link to download the Rancher CLI?:  In the bottom right of the
  UI
* How can you see what API keys are active for your account?:  Click on your
  user profile icon in the top right and select "API & Keys"  

### 4.3 Interacting With Monitoring and Logging

* Enabling Advanced Monitoring deploys Prometheus and Grafana.: True
* How do you see the Grafana dashboard for a given component? By clicking the
  Grafana icon next to a component in the UI
* What is the role of a notifier?:  Handles delivery of alerts from Prometheus
  to a destination such as Email or Pagerduty
* What types of clusters permit alerts for etcd?:  Clusters that are built
  using RKE
* How do you enable project vs. cluster-wide monitoring?:  Selecting the
  Monitoring section under tools at either the Cluster or Project level
  contexts and enabling first at the desired context
* Which of the following is NOT one of the log destinations?: InfluxDB

## Install Rancher as container

### Run runcher with Podman as self-signed certificate

```bash
sudo podman run -d --restart=always -p 80:80 -p 443:443 -v /opt/rancher:/var/lib/rancher rancher/rancher:v2.4.1
```

## Installing Rancher with Kubernetes

### Deploying Into RKE

1. Install nodes and RKE
2. Install helm3
```bash
wget https://get.helm.sh/helm-v3.2.4-linux-amd64.tar.gz -O /tmp/helm-v3.2.4-linux-amd64.tar.gz
tar xvzf /tmp/helm-v3.2.4-linux-amd64.tar.gz -C /tmp
mv /tmp/linux-amd64/helm ~/bin/helm3
chmod +x ~/bin/helm3
```
3.  Add Helm3 Rancher repo
```bash
helm3 repo add rancher-latest https://releases.rancher.com/server-charts/latest
```
4. Follow other steps from [guide](https://rancher.com/docs/rancher/v2.x/en/installation/k8s-install/helm-rancher/#3-create-a-namespace-for-rancher)




# Tips

## Reset admin password

```bash
kubectl --kubeconfig $KUBECONFIG -n cattle-system exec $(kubectl --kubeconfig $KUBECONFIG -n cattle-system get pods -l app=rancher | grep '1/1' | head -1 | awk '{ print $1 }') -- reset-password
```
