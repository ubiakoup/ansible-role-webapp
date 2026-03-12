# Ansible Role: Webapp (Apache Docker Deployment)

## Overview

This project provides an Ansible role that deploys an Apache web server running inside a Docker container.

The role automates the installation of required dependencies, generates a custom HTML page, and deploys the Apache container exposing the web service to the host.

The project demonstrates how to build reusable infrastructure components using Ansible roles and how to distribute them using Ansible Galaxy.

---

## Architecture

```
[ Ansible Control Node ]
          |
          | SSH
          v
[ Target Host ]
          |
          | Docker
          v
[ Apache Container ]
          |
          v
      Web Server
```

---

# Project Structure

ansible-role-webapp/

tasks/
templates/
defaults/
meta/
tests/
README.md
LICENSE
ansible.cfg

Explanation:

* **tasks/** → main role logic
* **templates/** → HTML template for Apache
* **defaults/** → default variables
* **meta/** → role metadata
* **tests/** → test environment for the role

---

# Requirements

* Ansible 2.10+
* Python installed on the target host
* Docker installed and running
* SSH access to the target server

---

# Role Variables

| Variable        | Description              | Default |
| --------------- | ------------------------ | ------- |
| container_name  | Docker container name    | webapp  |
| container_image | Docker image             | httpd   |
| container_port  | Port exposed on the host | 80      |

Example:

container_name: webapp_prod
container_port: 8080

---
# SSH Configuration

Before running the playbook, configure SSH access between the Ansible control node and the target host.

Generate an SSH key:
```
ssh-keygen -t rsa
```

Copy the key to the target server:

```
ssh-copy-id admin@<server-ip>
```
This allows passwordless SSH authentication.
---

# Testing the Role

The role includes a test playbook located in the `tests` directory.

Run the test:

ansible-playbook -i tests/inventory tests/test.yml

This verifies that the role works correctly before using it in another project.

Inventory must be ajusted with the Client ip-adress

---

# Using the Role in a Project

To use the role in another Ansible project, install it using **Ansible Galaxy**.

## Step 1 – Create roles directory

mkdir roles

---

## Step 2 – Create roles/requirements.yml

roles/requirements.yml


---
```
- src: https://github.com/ubiakoup/ansible-role-webapp.git

```

---

## Step 3 – Install the role

ansible-galaxy install -r roles/requirements.yml

The role will be installed in:

~/.ansible/roles/

---

# Deployment Configuration

## Inventory file

hosts.yml

```
---
all:
  vars:
    ansible_user: admin

  children:
    prod:
      hosts:
        client:
          ansible_host: 10.0.22.5
```

---

## Playbook

deploy_webapp.yml

```
---
- name: Deploy webapp
  hosts: prod
  become: true

  vars:
    container_name: webapp_prod
    container_port: 8080

  roles:
    - webapp
```

---

## Ansible Configuration

ansible.cfg

```
[defaults]
retry_files_enabled = False
host_key_checking = False

[privilege_escalation]
become_ask_pass = true
```

This configuration:

* disables retry files
* disables SSH host key verification
* automatically asks for the sudo password

---

## Group Variables

group_vars/prod.yml

```
---
ansible_user: admin
```

---

# Running the Deployment

Run the playbook with:

```
ansible-playbook -i hosts.yml deploy_webapp.yml
```
The role will:

1. install required dependencies
2. configure Docker
3. deploy an Apache container
4. expose the web service

---

# Result

Once the playbook finishes, the web application will be accessible at:

http://<server-ip>:<container_port>

Example:

http://10.0.22.5:8080

---

# Automation with Git

To simplify deployment, the configuration files can be stored in a Git repository.

Users can clone the repository:

git clone <repository-url>

Then install the role and run the deployment:
```
ansible-galaxy install -r roles/requirements.yml
```
```
ansible-playbook -i hosts.yml deploy_webapp.yml
```
---

# Author

Ulrich Kouatang
