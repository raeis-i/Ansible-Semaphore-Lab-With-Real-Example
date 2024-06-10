# Ansible_Semaphore_Lab_With_Real_Example

This repository creates 3 nodes for [Ansible_Semaphore](https://semui.co/) lab using Vagrant and Virtualbox.
You can define the number of nodes in "vars.rf" and also you can change IP range.

node1: Run Semaphore Ansible web UI in Docker Container [Official Link](https://docs.semui.co/administration-guide/installation#docker)

node2: As a Database VM

node3: As A Application VM

## Prerequisites

- Vagrant
- VirtualBox

## Installation
Clone Project Repository


```bash
$ git clone https://github.com/raeis-i/Ansible-Semaphore-Lab-With-Real-Example

$ cd Ansible-Semaphore-Lab-With-Real-Example

$ vagrant up

```
It takes some minutes to be up and ready, after that you can check status:

```bash
$ vagrant status
node1                     running (virtualbox)
node2                     running (virtualbox)
node3                     running (virtualbox)
```

## Configuration
- Open your web browser in your host
- Navigate to http://localhost:3000/auth/login
    - Username: admin
    - Password: changeme
- Select `Create Project`
    - Project Name: Test
- Click `Create`


### Key Stores
The Key Store in Semaphore is used to store credentials for accessing remote Repositories, accessing remote hosts, sudo credentials, and Ansible vault passwords.

- Select `Key Store`
- Select `New Key`
    - Key Name: vagrant-key
    - Type: Login with Password
    - Username: vagrant
    - Password: vagrant


### Repositories

A Repository is a place to store and manage Ansible content like playbooks and roles.[More](https://docs.semui.co/user-guide/repositories)

In this case we use local as we have a playbook in container

- Select `Repositories`
- Select `New Repository`
    - Name: RHEL 8 STIG
    - URL: git@gitlab.test.lab:cte/rhel-8-stig.git
        - This value can be a path to a local directory, git over ssh , or git over https
    - Branch: main
    - Access Key: Optional, Select from list of credentials created in previous steps
- Click `Create`


### Inventories

The Ansible inventory file defines the hosts and groups of hosts upon which commands, modules, and tasks in a playbook operate.

#### Static
- Select `Inventory`
- Select `New Inventory`
    - Name: Test
    - User Credentials: vagrant-key
    - Sudo Credentials: 
    - Type: Static
        ```
        [all]
        node2 ansible_hosts=192.168.56.2
        node3 ansible_hosts=192.168.56.3

        [database]
        node2 ansible_hosts=192.168.56.2

        [application]
        node3 ansible_hosts=192.168.56.3
        ```
- Click `Create`

### Task Templates
A task is an instance of launching an Ansible playbook. You can create the task from Task Template by clicking the button Run/Build/Deploy for the required template.[More](https://docs.semui.co/user-guide/tasks)


#### Create a Task Template
- Select `Task Templates`
- Select `New Template`
    - Name: Dev Lab - RHEL 8 STIG
    - Description: Applies the Red Hat Entperise Linux 8 STIG to hosts in the inventory
    - Playbook Filename: 
    - Inventory:
    - Repository:
    - Environment: all
    - Vault Password:
    - Survey Variables:
    - View: Leave Blank (Select a `View` from the dropdown, if you set that up in the previous step)
    - Cron: Leave Blank, Accepts Cron Time format `45 11 * * *` ,[More](https://crontab-generator.org/)
    - Allow CLI Args in Task: Check the box
- Click `Create`

## Task Execution


- Click `Run`
- At this point a new view within Semaphore will open and show you the live output of the running Tasks

