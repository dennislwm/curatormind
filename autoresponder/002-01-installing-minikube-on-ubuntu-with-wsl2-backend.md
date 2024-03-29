## TL;DR

minikube is local Kubernetes, focusing on making it easy to learn and develop for Kubernetes.

### Preparing your Windows 10 machine

What you'll need:
* 2 CPUs or more
* 2Gb of free memory
* 20Gb of free disk space
* Internet connection
* Windows Subsystem Linux ["WSL"] 2
  
> You can install WSL 2 by following this article [Install WSL on Windows 10](https://docs.microsoft.com/en-us/windows/wsl/install-win10)

* Ubuntu 18.04

> You can install Ubuntu on WSL 2 by following this article [Ubuntu on WSL 2 Is Generally Available](https://ubuntu.com/blog/ubuntu-on-wsl-2-is-generally-available)

* Container or virtual machine manager

> All you need is Docker (or similarly compatible) container or a Virtual Machine environment, and Kubernetes is a single command away: `minikube start`. You can install Docker Desktop for Windows by following this article [Docker Desktop WSL 2 backend](https://docs.docker.com/docker-for-windows/wsl)

### Installing minikube on Ubuntu 18.04 with Windows Subsystem Linux 2 backend

Download and install the latest minikube package for Ubuntu.

```
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube_latest_amd64.deb
sudo dpkg -i minikube_latest_amd64.deb
```
### Configuring VM driver

As we're on a virtual machine, we should set VM driver to none, as we cannot virtualize on virtualization.

```
sudo minikube config set vm-driver none
```

You should see the following output, which you can ignore as we don't have minikube running yet.

```
These changes will take effect upon a minikube delete and then a minikube start
```

### Starting minikube

First, change permissions for your `$USER` to the `.minikube` directory.

```
sudo chown -R $USER $HOME/.minikube; chmod -R u+wrx $HOME/.minikube
```

Then, start `minikube` using the following command:

```
minikube start --driver=docker --delete-on-failure
```

_Warning: The option `--driver=none` should not be used in Windows._

A successful output should have the following:

```
😄  minikube v1.20.0 on Ubuntu 18.04
✨  Using the docker driver based on user configuration
👍  Starting control plane node minikube in cluster minikube
🚜  Pulling base image ...
💾  Downloading Kubernetes v1.20.2 preload ...
    > preloaded-images-k8s-v10-v1...: 491.71 MiB / 491.71 MiB  100.00% 7.71 MiB
    > gcr.io/k8s-minikube/kicbase...: 358.09 MiB / 358.10 MiB  100.00% 5.30 MiB
    > gcr.io/k8s-minikube/kicbase...: 358.10 MiB / 358.10 MiB  100.00% 5.90 MiB
🔥  Creating docker container (CPUs=2, Memory=2200MB) ...
🐳  Preparing Kubernetes v1.20.2 on Docker 20.10.6 ...
    ▪ Generating certificates and keys ...
    ▪ Booting up control plane ...
    ▪ Configuring RBAC rules ...
🔎  Verifying Kubernetes components...
    ▪ Using image gcr.io/k8s-minikube/storage-provisioner:v5
🌟  Enabled addons: storage-provisioner, default-storageclass
🏄  Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default
```

### Troubleshooting

1. If you get the following error on `sudo minikube start`:

```
Exiting due to GUEST_MISSING_CONNTRACK: Sorry, Kubernetes 1.20.2 requires conntrack to be installed in root's path
```

The following command should resolve the above issue:

```
sudo apt-get install -y conntrack
```

2. If you get the following error on `sudo minikube start --driver=docker`:

```
Exiting due to DRV_AS_ROOT: The "docker" driver should not be used with root privileges.
```

You should perform `minikube start --driver=docker` without `sudo` privilege.

3. If you get the following error on `minikube start --driver=docker`:

```
Exiting due to HOST_HOME_PERMISSION: Failed to save config: open /home/dennislwm/.minikube/profiles/minikube/config.json: permission denied
```

The following command should resolve the above issue:

```
sudo chown -R $USER $HOME/.minikube; chmod -R u+wrx $HOME/.minikube
```

4. If you get the following error on `sudo minikube start --driver=docker`:

```
Exiting due to GUEST_DRIVER_MISMATCH: The existing "minikube" cluster was created using the "none" driver, which is incompatible with requested "docker" driver.
```

Check if an existing profile exists using `sudo minikube profile list`. The following command should resolve the above issue:

```
sudo minikube delete --purge=true --all=true
```
