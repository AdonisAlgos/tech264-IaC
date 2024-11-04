# Learning Infrustructure as Code (IaC)

- [Learning Infrustructure as Code (IaC)](#learning-infrustructure-as-code-iac)
  - [What is IaC?](#what-is-iac)
  - [Benefits of IaC?](#benefits-of-iac)
  - [When/where to use IaC?](#whenwhere-to-use-iac)
  - [What are the tools available for IaC?](#what-are-the-tools-available-for-iac)
  - [What is configuration management (CM)?](#what-is-configuration-management-cm)
  - [What is provisioning of infrastructure? Do CM tools do it?](#what-is-provisioning-of-infrastructure-do-cm-tools-do-it)
  - [What is Ansible and how does it work?](#what-is-ansible-and-how-does-it-work)
  - [Who is using IaC and Ansible in the industry?](#who-is-using-iac-and-ansible-in-the-industry)
- [Learning Ansible](#learning-ansible)
  - [Installing Ansible on Ubuntu](#installing-ansible-on-ubuntu)
  - [Linking Ansible Controller to Target Nodes](#linking-ansible-controller-to-target-nodes)
    - [Inventory basics: formats, hosts and groups](#inventory-basics-formats-hosts-and-groups)
    - [Connecting to hosts: Variables](#connecting-to-hosts-variables)
      - [Important Things to Know about Ansible and best practices.](#important-things-to-know-about-ansible-and-best-practices)
  - [Setting Up An Ansible Controller VM and Two Target Nodes (Application \& Database)](#setting-up-an-ansible-controller-vm-and-two-target-nodes-application--database)
    - [Useful Ansible Commands](#useful-ansible-commands)
    - [Creating an Ansible Playbook to Install Nginx on the Aplication Target Node](#creating-an-ansible-playbook-to-install-nginx-on-the-aplication-target-node)
    - [Creating an Ansible Playbook to Install MongoDB on the Database Target Node](#creating-an-ansible-playbook-to-install-mongodb-on-the-database-target-node)
    - [Task: Work out the command to use to copy test.txt to your target node](#task-work-out-the-command-to-use-to-copy-testtxt-to-your-target-node)
    - [Task: Create playbook to provision the application VM](#task-create-playbook-to-provision-the-application-vm)
      - [**Stage 1**: Create a new playbook called prov\_app\_with\_npm\_start.yml to install nodejs on the target node](#stage-1-create-a-new-playbook-called-prov_app_with_npm_startyml-to-install-nodejs-on-the-target-node)
      - [**Stage 2**: Make an updated version of prov\_app\_with\_npm\_start.yml named prov\_app\_with\_pm2.yml (or similar)](#stage-2-make-an-updated-version-of-prov_app_with_npm_startyml-named-prov_app_with_pm2yml-or-similar)
    - [Task: Create playbooks to provision the app and database](#task-create-playbooks-to-provision-the-app-and-database)
      - [Part 1: Provision the database](#part-1-provision-the-database)
      - [Part 2: Provision the application](#part-2-provision-the-application)
    - [Task: Create a master playbook to run 'prov-app.yml' and 'prov-db.yml' playbooks](#task-create-a-master-playbook-to-run-prov-appyml-and-prov-dbyml-playbooks)

## What is IaC?
Infrastructure as Code (IaC) is the process of managing and provisioning computing infrastructure using machine-readable configuration files instead of manual processes.

![Infrastructure as Code](./images/iac.png)

## Benefits of IaC?

* Consistency and repeatability
* Scalability and faster deployments
* Version control for infrastructure
* Reduced manual errors
* Automated and reliable infrastructure management

## When/where to use IaC?

IaC is used in environments requiring automation, cloud deployments, CI/CD pipelines, when scaling infrastructure efficiently, when it saves time, if infrastructure is managed by multiple people and redunduncy/infrustucture versioning (security).

## What are the tools available for IaC?

* Terraform
* AWS CloudFormation
* Ansible
* Puppet
* Chef

## What is configuration management (CM)?
CM refers to the process of standardizing resource configurations to ensure consistency across systems, often automating changes and deployments.

## What is provisioning of infrastructure? Do CM tools do it?
Provisioning is the process of setting up resources such as servers, databases, and networks. Some CM tools (e.g. Ansible, Puppet) can provision infrastructure but are more commonly used for configuring it post-provisioning.

## What is Ansible and how does it work?
Ansible is an open-source automation tool for configuration management, provisioning, and application deployment. It works by connecting to nodes via SSH, executing playbooks (in YAML) that describe the desired state.

* Instructions is written in YAML --> certain tasks are defined on a playbook.
* Controller (central machine) carries out the configurations.

## Who is using IaC and Ansible in the industry?
Major companies like Netflix, Capital One, NASA, and Google use IaC and tools like Ansible for automating infrastructure management and deployments.

# Learning Ansible

Ansible is an agentless automation tool that you install on a single host (referred to as the control node).

From the control node, Ansible can manage an entire fleet of machines and other devices (referred to as managed nodes) remotely with SSH and numerous other transports.

## Installing Ansible on Ubuntu

*Before installing any new packages, it's good practice to update the local package index to ensure you have the latest information*

1. Update the Package List

```bash
sudo apt update
```

2. Add the Ansible PPA

```bash
sudo add-apt-repository --yes --update ppa:ansible/ansible
```

3. Install Ansible

```bash
sudo apt install ansible
```

4. Verify installation

```bash
ansible --version
```

Ansible is now installed on your Ubuntu controller system and ready to use.

## Linking Ansible Controller to Target Nodes

Ansible uses an **inventory file** to define the target nodes it manages. This file contains details such as **IP addresses**, **SSH keys**, **usernames**, etc.

* Default Inventory File: Located at `/etc/ansible/hosts`.
* Specify a Custom Inventory File:
  * Command Line: Use the `-i <path>` option with your Ansible commands.
    ```bash
    ansible-playbook -i /path/to/inventory playbook.yml
    ```
  * Ansible Configuration File: Set the `inventory` parameter in `ansible.cfg`.
    ```ini
    [defaults]
    inventory = /path/to/inventory
    ```

### Inventory basics: formats, hosts and groups

Depending on the installed pluggins the inventory file can be written in multiple formats. Most commonly used ones are **INI** and **YAML**. A basic **INI** `/etc/ansible/hosts` might look like this:

```ini
[web]
ec2-app-instance ansible_host=54.195.174.237 ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/tech264-name-aws-key.pem

[db]
ec2-db-instance ansible_host=70.192.17.251 ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/tech264-name-aws-key.pem

[test:children]
web
db
```

### Connecting to hosts: Variables

* `ansible_connection`: This can be the name of any Ansible connection plugin. The default is ssh.
* `ansible_host`: The name of the host / IP address to connect to.
* `ansible_user`: The username to use when connecting to the host.

Specific to the SSH connection:

* `ansible_ssh_private_key_file`: Private key file used by SSH.

#### Important Things to Know about Ansible and best practices.

* Utilise **Playbooks** to run multiple tasks on target nodes. These playbook files use the YAML structure. Ansible uses a declarative approach meaning we declare the state and Ansible will leverage python to format and run a script to achieve the desired state.
* **Linux** isn't item potent meaning that if you **run and re-run scripts you don't always get the same result**. **Ansible modules** on the other hand **provide item potensy, making it best practice to utilise them**.
* **Target nodes** do not need to be on the same **OS** as the **Controller**.
* **Ansible** does **not** need to be **installed** on **Target nodes**.

## Setting Up An Ansible Controller VM and Two Target Nodes (Application & Database)

![Ansible Architecture](./images/ansible-architecture.png)

1. Create Virtual Machines on AWS

   * Controller VM: Configure the Network Security Group (NSG) to **allow SSH (port 22)** only.
   * Application Target Node: Configure the NSG to **allow SSH (port 22), HTTP (port 80), and port 3000**.
   * Database Target Node: Configure the NSG to **allow SSH (port 22) and the default MongoDB port 27017**.

2. Update and Upgrade the VMs

    On both the controller and target nodes, run:

    ```bash
    sudo apt update && sudo apt upgrade -y
    ```

3. Install and verify Ansible on the Controller VM

    On the controller VM, execute:

    ```bash
    sudo apt-add-repository ppa:ansible/ansible
    sudo apt update
    sudo apt install ansible -y
    ansible --version
    ```

4. Configure SSH Access from Controller to Target Nodes

    Before the controller can manage the target nodes, set up SSH access:

   * Navigate the SSH directory
     ```bash
     cd ~/.ssh
     ```

   * Copy the SSH Private Key:

      The private key should be the one that pairs to the public key attached to the Target Node VMs

      Create a file (preferably named the same as your original private key file) and paste the private key contents:

      ```bash
      nano <ssh_private_key_file_name>
      ```

      **Note: Handle your private key securely and avoid exposing it.**

   * Set the Correct Permissions:

      ```bash
      chmod 400 <ssh_private_key_file_name>
      ```

5. Configure the Ansible Inventory

   1. Navigate to the Ansible directory:
      ```bash
      cd /etc/ansible
      ```

   2. Edit the Hosts File:

      If no hosts are listed, add them by editing the hosts file:

      ```bash
      sudo nano hosts
      ```

   3. Add Target Node Information:

      Specify the target node's details:

      ```ini
      # [web] includes application instances
      [web]
      target_node ansible_host=<TARGET_NODE_IP> ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/<ssh_private_key_file_name>

      # [db] includes db instances
      [db]
      db_node ansible_host=<DB_NODE_IP> ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/<ssh_private_key_file_name>

      # [test] parent group of everything included within groups [web] & [db]
      [test:children]
      web
      db
      ```

6. Test connectivity to Target Nodes

   * Ping All Configured Hosts:

      ```bash
      ansible all -m ping
      ```

   * Ping a specific group

      ```bash
      ansible web -m ping
      ```

### Useful Ansible Commands

 * List Inventory Groups:

    ```bash
    ansible-inventory --list
    ```

    For a tree-like format:

    ```bash
    ansible-inventory --graph
    ```

* Run Ad-Hoc Commands:

    ```bash
    ansible <group_or_host> -a "<command>"
    ```

    Example:

    ```bash
    ansible web -a "uname -a"
    ```

### Creating an Ansible Playbook to Install Nginx on the Aplication Target Node

1. Navigate to the Ansible directory:
    ```bash
    cd /etc/ansible
    ```

2. Create the playbook file

    ```bash
    sudo nano install_nginx.yaml
    ```

3. Insert the following content

    ```yml
    # Starts with --- (three hyphens)
    ---
    # Name of the play
    - name: install nginx
      # Where - on which devices - run this playbook
      hosts: web
    
      # Get comprehensive facts on the hosts / devices
      gather_facts: yes # If you want the playbook to run faster, turn this off using "no"
    
      # Do we need to provide admin access? Use sudo
      become: true
    
      # Instructions for this play, known as "tasks"
      # First Task: Install nginx on the Target Node
      tasks:
      - name: install and configure nginx
        # Use "nginx" package // "state=present" means we need it running
        apt:
          name: nginx
          state: present
    ```

4. Run the Ansible playbook

    Execute the playbook to install Nginx on the application target node

    ```bash
    ansible-playbook install_nginx.yaml
    ```

### Creating an Ansible Playbook to Install MongoDB on the Database Target Node

1. Navigate to the Ansible directory:
    ```bash
    cd /etc/ansible
    ```

2. Create the playbook file

    ```bash
    sudo nano install_mongodb.yaml
    ```

3. Insert the following content

    ```yml
    ---
    - name: install mongodb

      hosts: db
    
      gather_facts: yes
    
      become: true
    
      tasks:
      - name: Import the MongoDB public key
        ansible.builtin.apt_key:
          url: https://www.mongodb.org/static/pgp/server-7.0.asc
          state: present

      - name: Add the MongoDB repository
        ansible.builtin.apt_repository:
          repo: "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu {{ ansible_distribution_release }}/mongodb-org/7.0 multiverse"
          state: present
          update_cache: yes

      - name: Install MongoDB 7.0.6
        ansible.builtin.apt:
          name: mongodb-org=7.0.6
          state: present
          update_cache: yes
    ```

4. Run the Ansible playbook

    Execute the playbook to install MongoDB on the database target node

    ```bash
    ansible-playbook install_mongodb.yaml
    ```

### Task: Work out the command to use to copy test.txt to your target node

Use copy module to do it, rather than ad hoc command:

* `-m ansible.builtin.copy`: Specifies the Ansible module to use, which is the copy module from the built-in Ansible modules. This module is used to copy files from the controller machine to the target nodes.
* `-a "src=~/test.txt dest=~/test.txt mode 0400"`: Provides arguments to the copy module:
  * `src=~/test.txt`: The source file on the Ansible controller machine.
  * `dest=~/test.txt`: The destination path on the target nodes where the file will be copied to. 
  * `mode=0400`: Sets the file permissions of the destination file to 0400, which means read permission for the owner only.

```bash
ansible web -m ansible.builtin.copy -a "src=~/test.txt dest=~/test.txt mode=0400"
```

### Task: Create playbook to provision the application VM

#### **Stage 1**: Create a new playbook called prov_app_with_npm_start.yml to install nodejs on the target node

1. Navigate to playbook directory

    ```bash
    cd /etc/ansible
    ```

2. Create a playbook called prov_app_with_npm_start.yml 

    ```bash
    sudo nano prov_app_with_npm_start.yml
    ```

3. Add the following yaml code
  [Install node.js version 20.x and run application via npm start](./playbooks/prov_app_with_npm_start.yml)


#### **Stage 2**: Make an updated version of prov_app_with_npm_start.yml named prov_app_with_pm2.yml (or similar)

1. Navigate to playbook directory

    ```bash
    cd /etc/ansible
    ```

2. Create a playbook called prov_app_with_npm_start.yml 

    ```bash
    sudo nano prov_app_with_pm2.yml
    ```

3. Add the following yaml code
  [Install node.js version 20.x and run application via pm2 process manager](./playbooks/prov_app_with_pm2.yml)


### Task: Create playbooks to provision the app and database

#### Part 1: Provision the database

1. Navigate to the Ansible directory:
    ```bash
    cd /etc/ansible
    ```

2. Create the playbook file

    ```bash
    sudo nano prov_db.yml
    ```
3. Add the following blocks of YAML code to install and configure MongoDB v7.0.6

    ```yaml
    # playbook to provision the database
    ---
    - name: Provision MongoDB
      # where to install the db
      hosts: db
      gather_facts: yes
      become: true
      tasks:
    ```

   * Import the MongoDB Public Key

      ```yaml
      # Download the key
      - name: Import the MongoDB public key
        ansible.builtin.apt_key:
          url: https://www.mongodb.org/static/pgp/server-7.0.asc
          state: present
      ```
   * Add MongoDB's official repository to the system's package sources.

      ```yaml
      # Add MongoDB's official repository to the system's package sources.
      - name: Add the MongoDB repository
        ansible.builtin.apt_repository:
          repo: "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu {{ ansible_distribution_release }}/mongodb-org/7.0 multiverse"
          state: present
          update_cache: yes
      ```

   * Install MongoDB 7.0.6

      ```yaml
      # install mongodb
      - name: Install MongoDB 7.0.6
        ansible.builtin.apt:
          name: mongodb-org=7.0.6
          state: present
          update_cache: yes
      ```

   * Start the MongoDB service and enable it to start on boot.

      ```yaml
      # Starting and enabling mongodb
      - name: Ensure MongoDB service is started and enabled
        service:
          name: mongod
          state: started
          enabled: yes
      ```

   * Configure MongoDB to Allow Remote Connections

      ```yaml
      # modify MongoDB configuration to allow remote connections
      - name: Configure MongoDB to allow remote connections
        ansible.builtin.lineinfile:
          path: /etc/mongod.conf
          regexp: '^  bindIp:.*'
          line: '  bindIp: 0.0.0.0'
          state: present
      ```
      * Module: lineinfile
         * **path**: The configuration file to edit `/etc/mongod.conf`.
         * **regexp**: A regular expression to find the line starting with `bindIp:`.
         * **line**: The line to replace it with `bindIp: 0.0.0.0`.
         * **state**: present: Ensures the line is present in the file.

    * Restart MongoDB to Apply Configuration Changes

      ```yaml
      # restart MongoDB service to apply changes
      - name: Restart MongoDB to apply configuration changes
        service:
          name: mongod
          state: restarted
      ```
4. From the Ansible controller, run adhoc commands to:

   * Check the status of the database to make sure it's running

      ```bash
      ansible db -a "sudo systemctl status mongod"
      ```
  
   * Check the bindIp has been configured
      ```bash
      ansible db -a "cat /etc/mongod.conf" | grep "bindIp"
      ```

#### Part 2: Provision the application

On the app VM, manually create an env var DB_HOST, check it's been created, restart the app

1. SSH into the App EC2 instance and run the following command

    ```bash
    export "DB_HOST=mongodb://<db_instance_private_ip>:27017/"
    ```

2. SSH back into the Controller Node and restart the application by running the following playbook:

    [Application provision with pm2 process manager playbook](./playbooks/prov_app_with_pm2.yml)

**Note: Why the Environment Variable Isn't Found**

* **Ansible's Execution Context**: **Ansible** runs commands in a **non-interactive, non-login shell** on the remote host. This means that **shell initialization files** like .bashrc, .bash_profile, or .profile are **NOT** sourced.

* **Environment Variables Scope**: **Environment variables** defined in these **user-specific initialization files** are **ONLY** available in **interactive** (login) shells. Therefore, when **Ansible** runs the application, it **DOES NOT** have access to these variables.

In your app playbook, create an environment variable, then start the app in the background

* One way to access the environment variable within your playbook is to use your playbook to make it persistent

  ```yaml
  - name: Add DB_HOST environment variable to /etc/environment (persistent)
    ansible.builtin.lineinfile:
      path: /etc/environment
      line: 'DB_HOST=mongodb://{{ db_host_ip }}:27017/posts'
      state: present
  ```
* Alternatively, you can set the environment variable directly when starting your application with PM2.

  ```yaml
  # Start the application with pm2, setting environment variable
  - name: Start the application with pm2
    ansible.builtin.shell:
      cmd: pm2 start app.js --update-env
      chdir: /home/ubuntu/app
    environment:
      DB_HOST: "mongodb://{{ db_host_ip }}:27017/posts"
    tags: pm2_start
  ```

Add the prov-db.yml to one playbook called prov-app-all.yml so you can run one playbook to configure/provision the app and database VMs

Playbook link for `prov-db.yml`: [Provision both application and satabase target nodes](./playbooks/prov-app-all.yaml)

### Task: Create a master playbook to run 'prov-app.yml' and 'prov-db.yml' playbooks

1. **Do playbooks run by a master playbook run simultaneously or one another?**

    Playbooks run by a master playbook are executed sequentially, one after another, not simultaneously.

2. **If a playbook run by a master playbook fails, what happens to the other playbooks being run?**

    If a playbook fails during execution by a master playbook, Ansible stops the execution, and subsequent playbooks are not run.

Playbook link for `master-playbook.yml`: [Master playbook to provision both app and db](./playbooks/master-playbook.yml)

```yaml
---
- import_playbook: prov_db.yml
- import_playbook: prov_app.yml
```

