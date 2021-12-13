[![Build Status](https://drone.kiwi-labs.net/api/badges/Diesel-Net/plex/status.svg)](https://drone.kiwi-labs.net/Diesel-Net/plex)

# plex
Plex Media Server on docker swarm running on Ubuntu Server LTS 20.04.3 (Focal Fossa). The virtual host was bootstrapped with [swarm-bootstrapper](https://github.com/Diesel-Net/swarm-bootstrapper). An _Nvidia Quadro P2200_ has been [configured for use with docker on the host](https://github.com/NVIDIA/nvidia-docker). This allows me to use the GPU's compute/transcoder with additional containers concurrently with plex! :tada:

## Features/Notes
- [x] Nvidia Driver installation 
- [x] Nvidia Docker configuration
- [x] Official PMS docker image
- [x] Factored-out library (docker volumes [NFSv4 driver](https://docs.docker.com/storage/volumes/#create-a-service-which-creates-an-nfs-volume))
- [x] Factored-out PMS configuration

## Toolchain
- ansible-core `2.11.5`
- ansible `4.6.0`
- docker `20.10.11`

## Installing Dependencies
```bash
ansible-galaxy install -r .ansible/roles/requirements.yaml -p .ansible/roles --force
```

## Update packages (Plex, Nvidia drivers & more)
You will need to have the ansible-vault password file configured on your machine. Please read the relevant [ansible documentation](https://docs.ansible.com/ansible/latest/user_guide/vault.html#setting-a-default-password-source) for more information.
```bash
ansible-playbook .ansible/update.yaml -i .ansible/inventories/development
```
