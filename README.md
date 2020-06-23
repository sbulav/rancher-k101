# rancher-k101
Rancher K101 training files and notes

## Overview

This repo contains guides and notes for Rancher K101 course

## Installation RKE

1. Install Vagrant
2. Install rke
3. Clone this repo
4. Run vagrant up
5. Run rke up

#Quiz

### 1.2 Discovering RKE

* RKE deploys Kubernetes components as:  Docker containers
* SSH is used to orchestrate RKE across servers: True
* Which is not something required on hosts to run RKE? SELinux is set to enforcing correct
* Cluster.yml contains all the information RKE needs to provision a Kubernetes cluster: True
* You can supply your own certificates that Rancher will serve for its UI/API: True
* rke start will start the provisioning of Kubernetes.: False
* Which versions of Kubernetes does RKE support by default?: The Latest patch releases from the three most recent minor releases correct

### 1.3 Day Two Operations for RKE

* What are the two files that RKE generates that need to be stored safely? cluster.rkestate and kube_config_cluster.yml
* What are the two locations RKE can write backup archives to? S3, Local Disk
* Upgrading Kubernetes involves changing the version in the system_images key and running rke upgrade.: False
* Certificates cannot be regenerated after install.: False
* You can add additional nodes to an RKE cluster after initial deployment. : True
* Which of the following is NOT something that RKE manages? Determining the correct amount of nodes for a Kubernetes cluster 

## Install Rancher as container


### Run runcher with Podman as self-signed certificate

```bash
sudo podman run -d --restart=always -p 80:80 -p 443:443 -v /opt/rancher:/var/lib/rancher rancher/rancher:v2.4.1
```
