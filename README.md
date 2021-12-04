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
You will need to have the ansible-vault password file configured on your machine. Please read the relevant [ansible documentation](https://docs.ansible.com/ansible/latest/user_guide/vault.html#setting-a-default-password-source) for more information.
```bash
ansible-playbook .ansible/update.yaml -i .ansible/inventory/production
```
