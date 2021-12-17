[![Build Status](https://drone.kiwi-labs.net/api/badges/Diesel-Net/plex/status.svg)](https://drone.kiwi-labs.net/Diesel-Net/plex)

# plex
Plex Media Server on docker swarm running on **Ubuntu Server 20.04.3 LTS** (_Focal Fossa_). The virtual host is running on Proxmox VE hypervisor and was bootstrapped with [swarm-bootstrapper](https://github.com/Diesel-Net/swarm-bootstrapper). An **Nvidia Quadro P2200** has been [configured for use with docker](https://github.com/NVIDIA/nvidia-docker) on the host. This allows me to use the GPU's compute/transcoder with other additional containers concurrently with plex! :tada:

## Features/Notes
- [x] Proxmox VE [GPU Passthrough](https://pve.proxmox.com/wiki/Pci_passthrough#GPU_Passthrough)
- [x] [Ubuntu Server 20.04.3 LTS](https://releases.ubuntu.com/20.04/) (Focal Fossa)
- [x] [Docker Swarm](https://docs.docker.com/engine/swarm/)
- [x] Nvidia Driver installation 
- [x] [Nvidia Container Toolkit](https://github.com/NVIDIA/nvidia-docker) installation
- [x] Official [Plex Media Server docker image](https://github.com/plexinc/pms-docker)
- [x] Factored-out library (using docker volumes [NFSv4 driver](https://docs.docker.com/storage/volumes/#create-a-service-which-creates-an-nfs-volume))
- [x] Factored-out Plex Media Server configuration

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
