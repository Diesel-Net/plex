[![Build Status](https://drone.kiwi-labs.net/api/badges/Diesel-Net/plex/status.svg)](https://drone.kiwi-labs.net/Diesel-Net/plex)

# plex
Plex Media Server on docker swarm running on **Ubuntu Server 20.04.3 LTS** (_Focal Fossa_). The virtual host is running on Proxmox VE hypervisor and was bootstrapped with [swarm-bootstrapper](https://github.com/Diesel-Net/swarm-bootstrapper). An **Nvidia Quadro P2200** has been [configured for use with docker](https://github.com/NVIDIA/nvidia-docker) on the host. This allows me to use the GPU's compute/transcoder with other additional containers concurrently with plex! :tada:

## Features/Notes
- [x] Proxmox VE [GPU Passthrough](https://pve.proxmox.com/wiki/Pci_passthrough#GPU_Passthrough)
- [x] [Ubuntu Server 20.04.3 LTS](https://releases.ubuntu.com/20.04/) (Focal Fossa)
- [x] [Docker Swarm](https://docs.docker.com/engine/swarm/)
- [x] Nvidia Driver installation (see [Diesel-Net/ansible-role-ubuntu](https://github.com/Diesel-Net/ansible-role-ubuntu/blob/stable/tasks/install_nvidia_drivers.yaml))
- [x] Nvidia Container Toolkit installation (see [Diesel-Net/ansible-role-docker](https://github.com/Diesel-Net/ansible-role-docker/blob/stable/tasks/install_nvidia_toolkit.yaml))
- [x] [Nvidia Container Toolkit](https://github.com/NVIDIA/nvidia-docker) installation
- [x] Official [Plex Media Server docker image](https://github.com/plexinc/pms-docker)
  - [Get your Plex Claim Token here!](https://www.plex.tv/claim/)
- [x] Factored-out library (using docker [NFSv4 volume](https://docs.docker.com/storage/volumes/#create-a-service-which-creates-an-nfs-volume))
- [x] Factored-out Plex Media Server configuration

## Confusion with Nvidia GPUs on Docker Swarm
Nvidia-gpu support of docker swarm is **not clearly documented** and caused me quite the headache. I still don't fully understand if `nvidia-docker2` is deprecated or not-recommended or what. I personally feel that the reason for all the confusion is due to the fact that there are essentially 3 main ways to start docker containers with automation tooling, i.e. `docker run`, `docker-compose up`, and `docker stack deploy` (swarm mode). Most of the documentation seems old and mainly deals with being able to start containers with the first two options, however I was able to get everything functioning in Docker Swarm by installing `nvidia-docker2` and then configuring `/etc/docker/daemon.json` to use the `nvidia` runtime by default for the host, and that's it. As long as you use the appropriate environment variables, you should be good to go hopefully :thumbsup:

- Docker
  - [swarmkit #1244](https://github.com/docker/swarmkit/issues/1244)
  - [Passing resources to Docker services](https://docs.docker.com/engine/reference/commandline/service_create/#create-services-requesting-generic-resources)
    - Not applicable and/or pretty much useless if using `docker stack deploy` with compose file format

- Nvidia
- [nvidia-docker #1268](https://github.com/NVIDIA/nvidia-docker/issues/1268)
- [nvidia-docker #1035](https://github.com/NVIDIA/nvidia-docker/issues/1035)

- Other
  - [Super insightful Hack](https://gist.github.com/tomlankhorst/33da3c4b9edbde5c83fc1244f010815c)
    - Outdated as most of the stuff no longer applies, but helped with understanding the need to set `daemon.json` manually
  - Python on Whale's user guide - [Generic resource on Docker Swarm](https://gabrieldemarmiesse.github.io/python-on-whales/user_guide/generic_resources/)
    - helped me understand generic swarmkit resources, which seemed to be the old "hacky way", but restricts a GPU (or any device) to a single swarm service
- [Accessing GPU's from a Docker Swarm Service](http://cowlet.org/2018/05/21/accessing-gpus-from-a-docker-swarm-service.html)
  - Outdated or no longer applies, but still insightful
   

## Manual Deployments
The below is set up to automatically configure and deploy using Drone CI, however you may use these commands to manually test and deploy or make changes if needed.

### Requirements
Recommended way to install Ansible is with `pip` for `Python3.9+`
- ansible `5.0.1`

1. Install Ansible Dependencies (external roles)
```bash
ansible-galaxy install -r .ansible/roles/requirements.yaml -p .ansible/roles --force
```
2. Configure and Deploy
You will need to have the ansible-vault password file configured on your machine. Please read the relevant [ansible documentation](https://docs.ansible.com/ansible/latest/user_guide/vault.html#setting-a-default-password-source) for more information.
```bash
ansible-playbook .ansible/update.yaml -i .ansible/inventories/development
```
