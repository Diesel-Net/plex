[![Build Status](https://drone.kiwi-labs.net/api/badges/Diesel-Net/plex/status.svg)](https://drone.kiwi-labs.net/Diesel-Net/plex)

# plex
Plex Media Server on Docker Swarm, bootstrapped with [swarm-bootstrapper](https://github.com/Diesel-Net/swarm-bootstrapper). Gpu Passthrough via on Proxmox.

## Features/Notes
- [x] Proxmox VE [PCIe Passthrough](https://pve.proxmox.com/wiki/PCI(e)_Passthrough)
- [x] [Ubuntu Server 20.04.3 LTS](https://releases.ubuntu.com/20.04/) (Focal Fossa)
- [x] [Docker Swarm](https://docs.docker.com/engine/swarm/)
- [x] Nvidia Driver installation (see [Diesel-Net/ansible-role-ubuntu](https://github.com/Diesel-Net/ansible-role-ubuntu/blob/stable/tasks/install_nvidia_drivers.yaml))
- [x] [Nvidia Container Toolkit](https://github.com/NVIDIA/nvidia-docker) installation (see [Diesel-Net/ansible-role-docker](https://github.com/Diesel-Net/ansible-role-docker/blob/stable/tasks/install_nvidia_toolkit.yaml))
- [x] Official [Plex Media Server docker image](https://github.com/plexinc/pms-docker)
  - [Get your Plex Claim Token here!](https://www.plex.tv/claim/)
- [x] Factored-out library (using docker [NFSv4 volume](https://docs.docker.com/storage/volumes/#create-a-service-which-creates-an-nfs-volume))
- [x] Factored-out Plex Media Server configuration

## Confusion with Nvidia GPUs on Docker Swarm
After installing `nvidia-docker2` and I had to manually configure `/etc/docker/daemon.json` to use the `nvidia` runtime by default for the host, and then pass the [appropriate environment variables](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/user-guide.html#environment-variables-oci-spec), namely `NVIDIA_VISIABLE_DEVICES`, and `NVIDIA_DRIVER_CAPABILITIES` s:thumbsup:


- Docker
  - [swarmkit #1244](https://github.com/docker/swarmkit/issues/1244)
  - [Passing generic resources to Docker services](https://docs.docker.com/engine/reference/commandline/service_create/#create-services-requesting-generic-resources)
    - Not applicable and/or pretty much useless if using `docker stack deploy` with compose file format
    - Probably still applies for OTHER generic resources but I do not think this is the recommended way of doing this for Nvidia specific GPUs nowadays
- Nvidia
  - [nvidia-docker #1268](https://github.com/NVIDIA/nvidia-docker/issues/1268)
  - [nvidia-docker #1035](https://github.com/NVIDIA/nvidia-docker/issues/1035)
- Other
  - [Super insightful Hack](https://gist.github.com/tomlankhorst/33da3c4b9edbde5c83fc1244f010815c)
    - Outdated as most of the stuff no longer applies, but helped with understanding the need to set `daemon.json` manually
  - Python on Whale's user guide - [Generic Resources on Docker Swarm](https://gabrieldemarmiesse.github.io/python-on-whales/user_guide/generic_resources/)
    - helped me understand generic swarmkit resources, which seemed to be the old "hacky way", but restricts a GPU (or any device) to a single swarm service
- [Accessing GPU's from a Docker Swarm Service](http://cowlet.org/2018/05/21/accessing-gpus-from-a-docker-swarm-service.html)
  - Outdated or no longer applies, but still insightful
   

## Manual Deployments
The below is set up to automatically configure and deploy using Drone CI, however you may use these commands to manually test and deploy or make changes if needed.

### Requirements
Recommended way to install Ansible is with `pip` for `Python3.9+`. Ansible `5.0.1` was used at the time of this writing.
```bash
pip3 install --user ansible
```

1. Install Ansible Dependencies (external roles)
```bash
ansible-galaxy install -r .ansible/roles/requirements.yaml -p .ansible/roles --force
```
2. Configure and Deploy
You will need to have the ansible-vault password file configured on your machine. Please read the relevant [ansible documentation](https://docs.ansible.com/ansible/latest/user_guide/vault.html#setting-a-default-password-source) for more information.
```bash
ansible-playbook .ansible/update.yaml -i .ansible/inventories/development
```
