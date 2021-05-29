[![Build Status](https://drone.kiwi-labs.net/api/badges/Diesel-Net/plex/status.svg)](https://drone.kiwi-labs.net/Diesel-Net/plex)

# plex
Plex Media Server automation

## Requirements
- Ansible 2.10+

## Installing Dependencies
```bash
ansible-galaxy install -r .ansible/roles/requirements.update.yaml -p .ansible/roles --force
```

## Update packages (Plex, Nvidia drivers & more)
Right now each environment is defined as an independent Virtual Machine (single-node swarm leaders)
```bash
ansible-playbook .ansible/update.yaml -i .ansible/inventory/production/hosts --vault-id ~/.tokens/master_id
```
