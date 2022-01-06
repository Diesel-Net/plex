[![Build Status](https://drone.kiwi-labs.net/api/badges/Diesel-Net/plex/status.svg)](https://drone.kiwi-labs.net/Diesel-Net/plex)

# plex
Plex Media Server on Docker Swarm, bootstrapped with [swarm-bootstrapper](https://github.com/Diesel-Net/swarm-bootstrapper).

## Features/Notes
- [x] Proxmox VE [PCIe Passthrough](https://pve.proxmox.com/wiki/PCI(e)_Passthrough) (Nvidia Quadro P2200)
- [x] [Ubuntu Server 20.04.3 LTS](https://releases.ubuntu.com/20.04/) (Focal Fossa)
- [x] Nvidia Driver installation (see [Diesel-Net/ansible-role-ubuntu](https://github.com/Diesel-Net/ansible-role-ubuntu/blob/stable/tasks/install_nvidia_drivers.yaml))
- [x] [Nvidia Container Toolkit](https://github.com/NVIDIA/nvidia-docker) installation (see [Diesel-Net/ansible-role-docker](https://github.com/Diesel-Net/ansible-role-docker/blob/stable/tasks/install_nvidia_toolkit.yaml))
- [x] Official [Plex Media Server docker image](https://github.com/plexinc/pms-docker)
  - [Get your Plex Claim Token here!](https://www.plex.tv/claim/)
- [x] Factored-out library (using docker [NFSv4 volume](https://docs.docker.com/storage/volumes/#create-a-service-which-creates-an-nfs-volume))
- [x] Factored-out Plex Media Server configuration

## Notes on Nvidia GPUs on Docker Swarm
After installing `nvidia-docker2` I had to manually configure `/etc/docker/daemon.json` to use the `nvidia` runtime by default for the host, and then pass the [appropriate environment variables](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/user-guide.html#environment-variables-oci-spec), namely `NVIDIA_VISIABLE_DEVICES`, and `NVIDIA_DRIVER_CAPABILITIES`.

- Docker
  - [swarmkit #1244](https://github.com/docker/swarmkit/issues/1244)
  - [Passing generic resources to Docker services](https://docs.docker.com/engine/reference/commandline/service_create/#create-services-requesting-generic-resources)
    - Not applicable and/or pretty much useless if using `docker stack deploy` with compose file format
    - Probably still applies for OTHER generic resources but I do not think this is the recommended way of doing this for Nvidia specific GPUs nowadays
- Nvidia
  - [nvidia-docker #1268](https://github.com/NVIDIA/nvidia-docker/issues/1268)
  - [nvidia-docker #1035](https://github.com/NVIDIA/nvidia-docker/issues/1035)
  - [nvidia-docker #1575](https://github.com/NVIDIA/nvidia-docker/issues/1575)
- Other
  - [Super insightful Hack](https://gist.github.com/tomlankhorst/33da3c4b9edbde5c83fc1244f010815c)
    - Outdated as most of the stuff no longer applies, but helped with understanding the need/how to set `daemon.json` manually
  - Python on Whale's user guide - [Generic Resources on Docker Swarm](https://gabrieldemarmiesse.github.io/python-on-whales/user_guide/generic_resources/)
    - helped me understand generic swarmkit resources, which seemed to be the old "hacky way", but restricts a GPU (or any device) to a single swarm service
- [Accessing GPU's from a Docker Swarm Service](http://cowlet.org/2018/05/21/accessing-gpus-from-a-docker-swarm-service.html)
  - Outdated or no longer applies, but still insightful
   

## Deployments
This application is configured and deployed automatically with [Drone CI](https://github.com/harness/drone) and [Ansible](https://github.com/ansible/ansible), however there might be situations where you would prefer to run the Ansible playbooks manually. 

You will need to have the [Ansible Vault](https://docs.ansible.com/ansible/latest/user_guide/vault.html#encrypting-content-with-ansible-vault) password file configured on your machine, if there are any vaulted variables in the inventory. Please read the relevant ansible documentation on [setting a default password source](https://docs.ansible.com/ansible/latest/user_guide/vault.html#setting-a-default-password-source).

If you are trying to reuse this Ansible configuration for _your own_ purposes, then you will need to encrypt all of _your own_ secrets using _your own_ Ansible Vault password and replace those variables in the [Ansible configuration](.ansible) after forking/cloning.

### Requirements
I recommend [installing Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#installing-ansible) with `pip` (globally) versus other package managers like `apt` or `brew`. It makes upgrading and using third party modules much easier. If you are on Windows, I would also recommend installing Ansible onto the WSL filesystem instead of the Windows filesytem. 
```bash
python3 -m pip install --user ansible
```

### Steps
1. Install [Ansible roles](https://docs.ansible.com/ansible/latest/user_guide/playbooks_reuse_roles.html) (playbook dependencies). This will download the roles defined in [requirements.yaml](.ansible/roles/requirements.yaml) and place them into `.ansible/roles` for you.
   ```bash
   ansible-galaxy install -r .ansible/roles/requirements.yaml -p .ansible/roles --force
   ```
2. Run [Ansible playbook](https://docs.ansible.com/ansible/latest/user_guide/playbooks_intro.html) against the `production` inventory.
   ```bash
   ansible-playbook .ansible/deploy.yaml -i .ansible/inventories/production
   ```
