sudo apt install --no-install-recommends nvidia-cuda-toolkit nvidia-headless-470 nvidia-utils-470 libnvidia-encode-470



distribution=$(. /etc/os-release;echo $ID$VERSION_ID) \
   && curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add - \
   && curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list



rsync -avh --no-compress --progress tv_shows /mnt/Media


rsync -avxh --progress --dry-run --ignore-existing tv_shows /mnt/Media



qm set 125 -efidisk0 local-zfs:1,format=<format>,efitype=4m,pre-enrolled-keys=1


Need to use UEFI and q35 machine
Switch to q35 machine architecture


Now I am trying to figure out whether the driver install should be a part of an ansible role?
    - ubuntu role?

Same with the nvidia docker toolkit?
    - docker role?


Or do we just hardcode them into the playbooks for now?


reusablility (other repos that might want the same stuff isntalled):
    - plex
    - AI/ML applications


UGHhh... :((  after all that work, I found out that nvidia-gpu support of docker swarm is super flaky and im starting to find all these hacky vague instructions on how to get it working..

https://gist.github.com/tomlankhorst/33da3c4b9edbde5c83fc1244f010815c
