# Setup High Available docker swarm cluster with Ansible

with this project you can setup high available and multi manager docker swarm cluster,
and automate installation process with Ansible.

## Prerequisites

- Ansible installed on your control node.
- Proper SSH access to your servers.
- Inventory file (`inventory/hosts.yml`) configured with your server details.

## Project structure

```sh
.
├── LICENSE
├── README.md
├── ansible.cfg
├── inventory
│   ├── group_vars
│   │   └── all
│   │       ├── preparing.yaml
│   │       └── swarm.yaml
│   └── hosts.yml
├── playbooks
│   ├── docker.yml
│   ├── preparing.yml
│   ├── pyscript.yml
│   └── swarm-cluster.yml
└── roles
    ├── docker
    │   ├── handlers
    │   │   └── main.yml
    │   ├── tasks
    │   │   ├── config_docker.yml
    │   │   ├── install_compose.yml
    │   │   ├── install_docker.yml
    │   │   └── main.yml
    │   └── templates
    │       └── daemon.j2
    └── preparing
        ├── handlers
        │   └── main.yaml
        ├── tasks
        │   ├── fstab.yml
        │   ├── hardening.yaml
        │   ├── hosts.yml
        │   ├── journalconf.yml
        │   ├── main.yml
        │   ├── packages.yml
        │   └── resolvconf.yml
        └── templates
            ├── hosts.allow.j2
            ├── hosts.deny.j2
            ├── hosts.j2
            ├── journald.conf.j2
            ├── logrotate.conf.j2
            ├── resolv.conf.j2
            ├── sshd_config.j2
            └── timeout.j2
```

## Usage

Follow the steps below to create docker swarm cluster.

### Step 1: Please read and change configuration files.

Please read hosts.yml file and change servers ip:

```sh
cat inventory/hosts.yml 

all:
  children:
    manager-servers:
      hosts:
        manager-server-1:
          ansible_host: 188.245.37.128   #change
          ansible_user: root
          ansible_port: 22

        manager-server-2:
          ansible_host: 188.245.37.132   #change
          ansible_user: root
          ansible_port: 22

        manager-server-3:
          ansible_host: 188.245.37.108  #change
          ansible_user: root
          ansible_port: 22
    
    worker-servers:
      hosts:
        worker-server-1:
          ansible_host: 188.245.36.92  #change 
          ansible_user: root
          ansible_port: 22

        worker-server-2:
          ansible_host: 49.13.162.129   #change 
          ansible_user: root
          ansible_port: 22

        worker-server-3:
          ansible_host: 168.119.172.101  #change 
          ansible_user: root
          ansible_port: 22
```

you can check ssh access to your servers with this command:

```sh
ansible all -m ping -i inventory/hosts.yml --user root --private-key /path/to/private-key
```

Change Advertise ip addresses:

```sh
cat inventory/group_vars/all/swarm.yaml

ADVERTISE_ADDRESS: 192.168.1.3
ADVERTISE_ADDRESS_MANAGER2: 192.168.1.4
ADVERTISE_ADDRESS_MANAGER3: 192.168.1.2
ADVERTISE_ADDRESS_WORKER1: 192.168.1.6
ADVERTISE_ADDRESS_WORKER2: 192.168.1.7
ADVERTISE_ADDRESS_WORKER3: 192.168.1.5

```

### Step 2: Run preparing playbook for prepare ubuntu servers

Run preparing playbook to prepare all ubuntu servers:

```sh
ansible-playbook -i inventory/hosts.yml playbooks/preparing.yml
```
### Step 3: Install docker on all ubuntu servers.

Run docker playbook to install docker on all ubuntu servers:

```sh
ansible-playbook -i inventory/hosts.yml playbooks/docker.yml 
```

### Step 4: Install Python Docker SDk in your servers

Run pyscript playbook for install python docker sdk on all servers:

```sh
ansible-playbook -i inventory/hosts.yml playbooks/pyscript.yml
```

### Step 5: Create your Docker swam cluster

Run swarm-cluster playbook for setup your cluster:

```sh
ansible-playbook -i inventory/hosts.yml playbooks/swarm-cluster.yml
```
