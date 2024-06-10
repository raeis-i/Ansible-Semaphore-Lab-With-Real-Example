# Ansible_Semaphore_Lab_With_Real_Example

This repository creates 3 nodes for [Ansible_Semaphore](https://semui.co/) lab using Vagrant and Virtualbox.
You can define the number of nodes in "vars.rf" and also you can change IP range.

We run Semaphore Ansible web UI in Docker Container [Official Link](https://docs.semui.co/administration-guide/installation#docker) on node1 and we will test jobs on node2 and node3.

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
- Open [http://localhost:3000/auth/login](http://localhost:3000/auth/login) in your web browser in your host.
    - Username: admin
    - Password: changeme
- Select Create Project
    - Project Name: Test


### Repositories

A Repository is a place to store and manage Ansible content like playbooks and roles.[More](https://docs.semui.co/user-guide/repositories)

In this test we use local as we mounted playbooks in container.
```bash
- Select `Repositories`
- Select `New Repository`
    - Name: Local
    - URL: /tmp/playbooks
```



### Key Stores
The Key Store in Semaphore is used to store credentials for accessing remote Repositories, accessing remote hosts, sudo credentials, and Ansible vault passwords.
```bash
- Select `Key Store`
- Select `New Key`
    - Key Name: vagrant-key
    - Type: Login with Password
    - Username: vagrant
    - Password: vagrant
```

### Environment
The Environment section of Semaphore is a place to store additional variables for an inventory and must be stored in JSON format. All task templates require an environment to be defined even if it is empty.

In this case we are going to Disabling host key checking, create envinronment:
```bash
Environment Name:ANSIBLE_HOST_KEY_CHECKING
Extra Varibales:

{
    "ANSIBLE_HOST_KEY_CHECKING" : false
}
```


### Inventories

The Ansible inventory file defines the hosts and groups of hosts upon which commands, modules, and tasks in a playbook operate.

```bash
- Select `Inventory`
- Select `New Inventory`
    - Name: All
    - User Credentials: vagrant-key
    - Sudo Credentials: 
    - Type: Static
        [all]
        node2 ansible_hosts=192.168.56.2
        node3 ansible_hosts=192.168.56.3
```


### Task Templates
A task is an instance of launching an Ansible playbook. You can create the task from Task Template by clicking the button Run/Build/Deploy for the required template.[More](https://docs.semui.co/user-guide/tasks)

In this case we are going to create a task for taking backup from /etc and save as tar in /tmp every min to test cron.[More](https://crontab-generator.org/)
#### Task1:Run a task every min
    - Name: Take a copy from "/etc" every minute
    - Playbook Filename: cp.yml
    - Inventory:All
    - Repository:Local
    - Cron: * * * * *
    - Environment:ANSIBLE_HOST_KEY_CHECKING

#### Task2:Install Nginx
    - Name: install nginx
    - Playbook Filename: install-nginx.yml
    - Inventory:All
    - Repository:Local
    - Environment:ANSIBLE_HOST_KEY_CHECKING

## Task Execution
- You can see result in dashboard.


![demo](https://raw.githubusercontent.com/raeis-i/Ansible-Semaphore-Lab-With-Real-Example/main/screenshots/semaphore.gif)


