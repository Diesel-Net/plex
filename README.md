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

## Mass Confusion with Nvidia GPUs on Docker Swarm
Nvidia-gpu support of docker swarm is **not clearly documented**. I personally feel that the reason for all the confusion is due to the fact that there are essentially 3 ways to
- [#1244](https://github.com/docker/swarmkit/issues/1244)
- [#1268](https://github.com/NVIDIA/nvidia-docker/issues/1268)
- [#1035](https://github.com/NVIDIA/nvidia-docker/issues/1035)
- [GitHub Gist hack?](https://gist.github.com/tomlankhorst/33da3c4b9edbde5c83fc1244f010815c)(outdated kinda, but helped with understanding the need to set daemon.json manually)
- Official Docker Docs - [Passing resources to Docker services](https://docs.docker.com/engine/reference/commandline/service_create/#create-services-requesting-generic-resources) (N/A or useless if using `docker stack deploy`)
- Python on Whale's user guide - [Generic resource on Docker Swarm](https://gabrieldemarmiesse.github.io/python-on-whales/user_guide/generic_resources/) (helped me understand generic resources, which seemed to be the old "hack", but restricts a GPU to a single swarm service)
- [Acessing GPU's from a Docker Swarm Service (2018)](http://cowlet.org/2018/05/21/accessing-gpus-from-a-docker-swarm-service.html)
   

## Toolchain
- python `3.9.9`
- ansible-core `2.12.1`
- ansible `5.0.1`

## Install Dependencies
```bash
ansible-galaxy install -r .ansible/roles/requirements.yaml -p .ansible/roles --force
```

## Configure and Deploy
You will need to have the ansible-vault password file configured on your machine. Please read the relevant [ansible documentation](https://docs.ansible.com/ansible/latest/user_guide/vault.html#setting-a-default-password-source) for more information.
```bash
ansible-playbook .ansible/update.yaml -i .ansible/inventories/development
```
