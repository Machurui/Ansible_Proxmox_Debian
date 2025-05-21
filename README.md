# Automated Debian VM Deployment on Proxmox Using Ansible

## Overview

This project automates the deployment of a Debian virtual machine (VM) on a Proxmox server using Ansible.

## Authentication

1. **Create Proxmox User**  
   Create a new Proxmox user named `ansible`.

2. **(Optional) Generate API Token**  
   You can authenticate Ansible using the user's password or an API token.  
   If using a token:
   - Assign it to the `ansible` user.
   - Label it appropriately, e.g., `AnsibleToken`.
   - Provide `token_id` and `token_secret` in your Ansible configuration.

## Ansible Installation

1. **Create a Debian VM**  
   - Use your Proxmox interface to create a new VM and name it (e.g., `ansible`).
   - Select Debian as the operating system and choose your hardware settings.

2. **Connect to the VM**  
   Use an SSH client (e.g., Termius) to connect to the Debian VM.

3. **Install pipx**
  ```bash
  apt update && apt install pipx
  pipx ensurepath //To set it in PATH
  ```

4. **Install Ansible using pipx**  
  ```bash
  pipx install --include-deps ansible
  pipx inject ansible argcomplete #Shell completion
  activate-global-python-argcomplete --user
  ansible --version
  ```

   - `ansible-core`: Minimal version
   - `ansible`: Full version with additional packages (recommended)

## Ansible Configuration

**Run this command**
  ```bash
  ansible-config init --disabled > ansible.cfg
  ```
In the hosts file, list the IP addresses of the servers you want Ansible to manage.
- **Configuration file:** `/etc/ansible/ansible.cfg`
- **Inventory file:** `/etc/ansible/hosts`  

## Semaphore Installation (Web UI for Ansible)

1. **Create a Linux user for Semaphore**
  ```bash
  adduser --system --group --home /home/your-name your-name
  ```

2. **Install MariaDB**
  ```bash
  sudo apt install mariadb-server
  sudo mysql_secure_installation
  ```

3. **Create Database and User**
  ```sql
  //already connected to your database
  CREATE DATABASE sema;
  GRANT ALL PRIVILEGES ON sema.* TO sema_u@localhost IDENTIFIED BY 'password';
  FLUSH PRIVILEGES;
  EXIT;
  ```

4. **Download and Install Semaphore**  
  ```bash
  wget https://github.com/semaphoreui/semaphore/releases/download/v2.13.14/semaphore_2.14.10_linux_amd64.deb
  sudo dpkg -i semaphore_2.14.10_linux_amd64.deb
  ```

5. **Configure Semaphore**
  Start the setup of Semaphore
  ```bash
  semaphore setup
  ```

   During setup, you will need to provide:
   - Linux user
   - Database name and credentials

*(Optional)* Move config files to a dedicated folder
  ```bash 
  chown your-name:your-name config.json
  
  mkdir /etc/my-folder
  chown your-name:your-name /etc/my-folder
  mv config.json /etc/my-folder/
  ```
     

7. **Run Semaphore**
   - Via CLI
   - Or configure it as a daemon/service

## First Task Setup in Semaphore

1. **Create a Repository**
   - Navigate to: `Repositories → New Repository`
   - Provide a name, Git URL, and branch (e.g., `main`)

2. **Create an Inventory**
   - Go to: `Inventory → New Inventory`
   - Name: `localhost`
   - Credentials: `None`
   - Inventory Type: `Static`
  ```bash
  [localhost]
  127.0.0.1 ansible_connection=local
  ```

3. **Add Environment Variables**
   - Navigate to: `Variable Groups → New Group`
   - Add credentials (e.g., Proxmox username, password, token, etc.)

4. **Create and Run a Template**
   - Go to: `Templates → New Template`
   - Fill in:
     - Template Name
     - Playbook File (e.g., `vm.yml`)
     - Select your Repository, Inventory, and Variable Group

5. **Run the Task**
   - Start the task from the template  
   🎉 Your automated VM deployment is now live!

## Sources

- [Learn Linux TV](https://www.youtube.com/c/LearnLinuxtv)
- [Ansible Documentation](https://docs.ansible.com/)
- [Semaphore Documentation](https://docs.ansible-semaphore.com/)
