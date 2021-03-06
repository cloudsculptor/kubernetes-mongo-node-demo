# Kubernetes | Docker | MongoDB | Node.js demo

This repository contains a very basic demo of the technologies listed in the title.

The instructions below show you how to setup a local Kubernetes cluster with an integrated Docker Registry
from scratch on an `Ubuntu 18.04` machine.

Alternatively, if you know your way around Kubernetes and Docker registries, you should be able to deploy this sample app to any 
Kubernetes cluster with appropriate changes to the build script.

## Quick start

1. Install a local Kubernetes cluster and Docker repository by following [these](#kubernetes-infrastructure) instructions.
2. Run the `build.sh` script in this directory to install all the services in Kubernetes.
3. You can monitor the progress of the roll out with `kubectl` or the [Kubernetes Dashboard](https://microk8s.io/docs/addon-dashboard).
4. Once a successful roll out is complete you can view the results at http://127.0.0.1

If you have a play, and need to clean the Kubernetes cluster and start again the command is `microk8s.reset`.

## Kubernetes Infrastructure

You can deploy this example to any compatiable Kubernetes cluster, but the following instructions
are provided to assist in setting up a local cluster that is certain to be compatible.

## Target environment

* Ubuntu v18.04 (`lsb_release -a`)
* Docker CE v19.03.6 (`docker -v`)
* MicroK8s providing:
* Kubernetes Client v1.17.3 (`kubectl version`)
* Kubernetes Server v1.17.2 (`kubectl version`)
* Node.js v10 & NPM v6 - optional for local dev (`node -v`, `npm -v`)

## Setting up environment

Starting from a machine with a fresh copy of Ubuntu 18.04:

### Install and configure MicroK8s

1. Install `MicroK8s` following these instructions: https://microk8s.io/docs/
2. Enable the following addons: `dns`, `storage`, `registry`, `helm`, `ingress`, `dashboard`.
3. Check status of addons `microk8s.status --wait-ready`.

#### MicroK8s install commands for the impatient

```
sudo snap install microk8s --classic --channel=1.17/stable
sudo usermod -a -G microk8s $USER
su - $USER
alias kubectl='microk8s.kubectl'
microk8s.status --wait-ready
microk8s.enable dns
microk8s.enable storage
microk8s.enable registry
microk8s.enable helm
microk8s.enable ingress
microk8s.enable dashboard
microk8s.status --wait-ready
```

### Install and run Kubernetes Dashboard

1. Install and port forward the dashboard following instructions here: https://microk8s.io/docs/addon-dashboard
2. Open dashboard here: https://127.0.0.1:10443

#### Install and run Kubernetes Dasboard commands for the impatient

```
microk8s.enable dashboard
token=$(microk8s.kubectl -n kube-system get secret | grep default-token | cut -d " " -f1)
microk8s.kubectl -n kube-system describe secret $token
microk8s.kubectl port-forward -n kube-system service/kubernetes-dashboard 10443:443
# Open: https://127.0.0.1:10443
```

### Install Docker CE

1. Follow instructions here: https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-18-04
2. Ensure you setup a non root user who can run Docker.

#### Docker CE install commands for the impatient
    
```
sudo apt update
sudo apt install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic stable"
sudo apt update
sudo apt install docker-ce
sudo usermod -aG docker ${USER}
su - ${USER}
```

### Install Node.js & NPM (optional)

1. Follow instructions here: https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-ubuntu-18-04

#### Install Node.js & NPM commands for the impatient

```
cd ~
curl -sL https://deb.nodesource.com/setup_10.x -o nodesource_setup.sh
sudo bash nodesource_setup.sh
sudo apt install nodejs
nodejs -v
npm -v
```