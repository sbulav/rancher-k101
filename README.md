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
* Which is not something required on hosts to run RKE? SELinux is set to enforcing
* Cluster.yml contains all the information RKE needs to provision a Kubernetes cluster: True
* You can supply your own certificates that Rancher will serve for its UI/API: True
* rke start will start the provisioning of Kubernetes.: False
* Which versions of Kubernetes does RKE support by default?: The Latest patch releases from the three most recent minor releases

### 1.3 Day Two Operations for RKE

* What are the two files that RKE generates that need to be stored safely? cluster.rkestate and kube_config_cluster.yml
* What are the two locations RKE can write backup archives to? S3, Local Disk
* Upgrading Kubernetes involves changing the version in the system_images key and running rke upgrade.: False
* Certificates cannot be regenerated after install.: False
* You can add additional nodes to an RKE cluster after initial deployment. : True
* Which of the following is NOT something that RKE manages? Determining the  amount of nodes for a Kubernetes cluster

## 2. Installing and Managing Rancher

### 2.1 Installing Rancher with Docker

* When might you want to use the Docker method for installing Rancher? For sandbox or demonstration purposes
* The first step in backing up a single container Docker install is stopping the container.: True
* Upgrading Rancher is similar to making a backup except we start a container with a new image tag: True
* Why do you want to delete the old Rancher container after an upgrade?:  So it doesnt startup accidentally and overwrite the data volume

### 2.2 Installing Rancher with Kubernetes

* Which of the following is a valid type of load balancer when fronting Rancher server?: A TCP-based load balancer
* Its possible to migrate from alpha to latest, but not to stable.: False
* Which type of TLS certificate can you use with Rancher?: All of options
* Lets Encrypt doesnt actually use the email address you give them.: False
* How do you make a backup of Rancher in RKE?: Make a normal RKE backup
* You can restore a Rancher/RKE backup with zero downtime.: False
* You can roll back a failed Rancher upgrade with helm rollback.: False

## 3. Deploying Kubernetes With Rancher

### 2.1 Designing and Provisioning Clusters

* Which features are unavailable with Hosted Kubernetes Clusters?: etcd backup/restore and some monitoring of etcd
* What is the difference between a Node Template and an RKE Template?:  Node templates describe how to provision nodes and RKE templates how to deploy Kubernetes
* Where can you find detailed information on the port requirements for each role?: Rancher Documentation
* When you create a cluster in Rancher, what RBAC role are you assigned?  Owner

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
