---
layout: post
title: "Ansible for DevOps Engineers: A Practical Guide"
date: 2025-01-09
categories: [devops, automation]
tags: [ansible, configuration-management, automation, devops, tutorial, beginner-friendly, hands-on, infrastructure, orchestration]
---

Ansible automates IT tasks through simple, human-readable playbooks. It manages configuration, deploys applications and orchestrates complex workflows across servers without requiring agents on target machines. This guide introduces Ansible's concepts, architecture and commands with practical examples you can follow along.

---

## 1. What Is Ansible and Why Does It Exist

Before configuration management tools, system administrators configured servers manually through SSH sessions or remote desktop. This approach was slow, inconsistent and difficult to replicate. Making changes to multiple servers required running the same commands repeatedly. Documenting server configurations meant maintaining wikis or runbooks that quickly became outdated.

Ansible solves these problems by treating infrastructure configuration as code. You write playbooks that describe the desired state of your systems. Ansible connects to your servers and makes the necessary changes to achieve that state. This approach provides several benefits:

- Configuration changes are versioned in Git
- Changes can be tested before applying to production
- Configuration is reproducible across environments
- Multiple team members can collaborate safely
- Server state is documented in code rather than wikis
- No agents required on managed nodes

Ansible uses SSH for Linux systems and WinRM for Windows. It requires no special infrastructure, databases or daemons running on target machines. This agentless architecture makes Ansible simple to deploy and maintain.

---

## 2. Core Concepts

Ansible uses several key concepts to manage infrastructure.

### Control Node

The machine where Ansible is installed and from which you run commands. This is your laptop, a bastion host or a CI/CD server.

### Managed Nodes

The servers you manage with Ansible. Also called hosts. Managed nodes require Python but no Ansible installation.

### Inventory

A file that lists your managed nodes. Inventories can be static files or dynamic scripts that query cloud providers.

### Modules

Units of code that Ansible executes. Each module has a specific purpose like installing packages, copying files or managing users.

### Tasks

Single actions that use modules. Tasks are the building blocks of playbooks.

### Playbooks

YAML files that define a series of tasks. Playbooks describe the desired state of your systems.

### Plays

Sections within a playbook that map tasks to hosts. A playbook can contain multiple plays.

### Roles

Collections of tasks, variables, files and templates organised in a standard structure. Roles allow you to package and reuse automation.

### Facts

System information that Ansible automatically collects from managed nodes. Facts include IP addresses, operating systems, hardware details and more.

### Handlers

Tasks that run only when notified by other tasks. Handlers are typically used to restart services after configuration changes.

### Variables

Values that can change based on the system or situation. Variables make playbooks flexible and reusable.

---

## 3. Installing Ansible

Ansible runs on Linux, macOS and Windows Subsystem for Linux. It cannot run natively on Windows as a control node.

**Install on Ubuntu/Debian:**

```bash
sudo apt update
sudo apt install ansible
```

**Install on RHEL/CentOS:**

```bash
sudo yum install epel-release
sudo yum install ansible
```

**Install on macOS with Homebrew:**

```bash
brew install ansible
```

**Install with pip:**

```bash
pip install ansible
```

**Verify installation:**

```bash
ansible --version
```

You should see the version number and configuration file location.

---

## 4. Basic Ansible Workflow

Ansible follows a consistent workflow:

1. **Define** your inventory of hosts
2. **Write** playbooks describing desired state
3. **Run** playbooks against inventory
4. **Verify** changes were applied correctly

This workflow applies to every Ansible project.

---

## 5. Your First Ansible Command

Create a simple inventory and run an ad-hoc command.

**Create an inventory file:**

```bash
mkdir ansible-demo
cd ansible-demo
```

Create `inventory.ini`:

```ini
[local]
localhost ansible_connection=local
```

**Run a command:**

```bash
ansible local -i inventory.ini -m ping
```

The `ping` module tests connectivity. You should see:

```json
localhost | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

**Run a shell command:**

```bash
ansible local -i inventory.ini -m shell -a "echo Hello from Ansible"
```

This executes the echo command on localhost.

---

## 6. Basic Ansible Commands

**Test connectivity:**

```bash
ansible all -i inventory.ini -m ping
```

**Run ad-hoc command:**

```bash
ansible all -i inventory.ini -m shell -a "uptime"
```

**Check playbook syntax:**

```bash
ansible-playbook playbook.yml --syntax-check
```

**Run playbook:**

```bash
ansible-playbook playbook.yml -i inventory.ini
```

**Check mode (dry run):**

```bash
ansible-playbook playbook.yml -i inventory.ini --check
```

**Show differences:**

```bash
ansible-playbook playbook.yml -i inventory.ini --check --diff
```

**Limit to specific hosts:**

```bash
ansible-playbook playbook.yml -i inventory.ini --limit webservers
```

**Run with tags:**

```bash
ansible-playbook playbook.yml -i inventory.ini --tags "install"
```

**Skip tags:**

```bash
ansible-playbook playbook.yml -i inventory.ini --skip-tags "deploy"
```

**Run with extra variables:**

```bash
ansible-playbook playbook.yml -i inventory.ini -e "version=1.2.3"
```

**Verbose output:**

```bash
ansible-playbook playbook.yml -i inventory.ini -v
```

Use `-vv`, `-vvv` or `-vvvv` for more verbosity.

---

## 7. Understanding Inventory Files

Inventories define your infrastructure.

### INI Format

```ini
# Ungrouped hosts
mail.example.com

[webservers]
web1.example.com
web2.example.com
web3.example.com

[databases]
db1.example.com
db2.example.com

[east:children]
webservers
databases
```

### YAML Format

```yaml
all:
  hosts:
    mail.example.com:
  children:
    webservers:
      hosts:
        web1.example.com:
        web2.example.com:
        web3.example.com:
    databases:
      hosts:
        db1.example.com:
        db2.example.com:
    east:
      children:
        webservers:
        databases:
```

### Host Variables

```ini
[webservers]
web1.example.com http_port=80 ansible_user=admin
web2.example.com http_port=8080 ansible_user=root
```

### Group Variables

```ini
[webservers:vars]
ansible_user=deploy
http_port=80
```

### IP Addresses and Aliases

```ini
[webservers]
web1 ansible_host=192.168.1.10
web2 ansible_host=192.168.1.11
```

### Patterns for Ranges

```ini
[webservers]
web[01:05].example.com

[databases]
db-[a:c].example.com
```

This creates web01 through web05 and db-a through db-c.

---

## 8. Your First Playbook

Create a playbook that installs and starts Apache.

**Create playbook.yml:**

```yaml
---
- name: Install and start Apache
  hosts: webservers
  become: yes
  
  tasks:
    - name: Install Apache
      apt:
        name: apache2
        state: present
        update_cache: yes
      
    - name: Start Apache service
      service:
        name: apache2
        state: started
        enabled: yes
      
    - name: Create index page
      copy:
        content: "<h1>Hello from Ansible</h1>"
        dest: /var/www/html/index.html
```

**Run the playbook:**

```bash
ansible-playbook playbook.yml -i inventory.ini
```

Ansible connects to webservers, installs Apache, starts the service and creates an index page.

---

## 9. Playbook Structure

Playbooks are YAML files with specific structure.

### Basic Structure

```yaml
---
- name: Play name
  hosts: target_hosts
  become: yes
  
  vars:
    variable_name: value
  
  tasks:
    - name: Task description
      module_name:
        parameter: value
```

### Multiple Plays

```yaml
---
- name: Configure web servers
  hosts: webservers
  tasks:
    - name: Install Apache
      apt:
        name: apache2
        state: present

- name: Configure databases
  hosts: databases
  tasks:
    - name: Install PostgreSQL
      apt:
        name: postgresql
        state: present
```

### Task Syntax

Short form:

```yaml
- name: Install package
  apt: name=nginx state=present
```

Long form (preferred):

```yaml
- name: Install package
  apt:
    name: nginx
    state: present
```

---

## 10. Common Modules

### Package Management

**apt (Debian/Ubuntu):**

```yaml
- name: Install packages
  apt:
    name:
      - nginx
      - git
      - vim
    state: present
    update_cache: yes
```

**yum (RHEL/CentOS):**

```yaml
- name: Install packages
  yum:
    name:
      - httpd
      - git
    state: present
```

**package (generic):**

```yaml
- name: Install package
  package:
    name: nginx
    state: present
```

### File Management

**copy:**

```yaml
- name: Copy file
  copy:
    src: /local/path/file.txt
    dest: /remote/path/file.txt
    owner: www-data
    group: www-data
    mode: '0644'
```

**template:**

```yaml
- name: Copy template
  template:
    src: config.j2
    dest: /etc/app/config.conf
    owner: root
    mode: '0644'
```

**file:**

```yaml
- name: Create directory
  file:
    path: /opt/app
    state: directory
    owner: deploy
    mode: '0755'

- name: Remove file
  file:
    path: /tmp/old-file
    state: absent
```

**lineinfile:**

```yaml
- name: Add line to file
  lineinfile:
    path: /etc/hosts
    line: "192.168.1.100 app.example.com"
    state: present
```

**blockinfile:**

```yaml
- name: Add configuration block
  blockinfile:
    path: /etc/ssh/sshd_config
    block: |
      PermitRootLogin no
      PasswordAuthentication no
```

### Service Management

```yaml
- name: Start and enable service
  service:
    name: nginx
    state: started
    enabled: yes

- name: Restart service
  service:
    name: apache2
    state: restarted

- name: Stop service
  service:
    name: mysql
    state: stopped
```

### User Management

```yaml
- name: Create user
  user:
    name: deploy
    shell: /bin/bash
    groups: sudo
    append: yes
    create_home: yes

- name: Add SSH key
  authorized_key:
    user: deploy
    state: present
    key: "{{ lookup('file', '~/.ssh/id_rsa.pub') }}"
```

### Command Execution

**command:**

```yaml
- name: Run command
  command: /usr/bin/make install
  args:
    chdir: /opt/app
```

**shell:**

```yaml
- name: Run shell command
  shell: echo $HOME > /tmp/home.txt
```

**script:**

```yaml
- name: Run script
  script: /local/path/script.sh
```

### Git

```yaml
- name: Clone repository
  git:
    repo: https://github.com/user/repo.git
    dest: /opt/app
    version: main
```

---

## 11. Variables

Variables make playbooks flexible and reusable.

### Defining Variables

**In playbook:**

```yaml
---
- name: Install web server
  hosts: webservers
  vars:
    http_port: 80
    max_clients: 200
  
  tasks:
    - name: Create config
      template:
        src: httpd.conf.j2
        dest: /etc/httpd/httpd.conf
```

**In separate file:**

Create `vars.yml`:

```yaml
http_port: 80
max_clients: 200
server_name: web.example.com
```

Include in playbook:

```yaml
---
- name: Install web server
  hosts: webservers
  vars_files:
    - vars.yml
  
  tasks:
    - name: Show variables
      debug:
        msg: "Port: {{ http_port }}, Clients: {{ max_clients }}"
```

**In inventory:**

```ini
[webservers]
web1.example.com http_port=80

[webservers:vars]
max_clients=200
```

**Command line:**

```bash
ansible-playbook playbook.yml -e "http_port=8080"
```

### Using Variables

```yaml
- name: Install package
  apt:
    name: "{{ package_name }}"
    state: present

- name: Create directory
  file:
    path: "/opt/{{ app_name }}"
    state: directory
```

### Variable Precedence

From lowest to highest priority:

1. Role defaults
2. Inventory file or script group vars
3. Inventory group_vars/all
4. Playbook group_vars/all
5. Inventory group_vars/*
6. Playbook group_vars/*
7. Inventory file or script host vars
8. Inventory host_vars/*
9. Playbook host_vars/*
10. Host facts
11. Play vars
12. Play vars_files
13. Role vars
14. Task vars
15. Extra vars from command line

---

## 12. Facts

Ansible automatically collects system information.

### Viewing Facts

```bash
ansible hostname -i inventory.ini -m setup
```

### Using Facts

```yaml
- name: Display facts
  debug:
    msg: "{{ ansible_hostname }} runs {{ ansible_distribution }} {{ ansible_distribution_version }}"

- name: Install package based on OS
  apt:
    name: apache2
    state: present
  when: ansible_os_family == "Debian"

- name: Install package on RedHat
  yum:
    name: httpd
    state: present
  when: ansible_os_family == "RedHat"
```

### Common Facts

- `ansible_hostname`: Short hostname
- `ansible_fqdn`: Fully qualified domain name
- `ansible_distribution`: OS distribution
- `ansible_distribution_version`: OS version
- `ansible_os_family`: OS family (Debian, RedHat, etc.)
- `ansible_architecture`: CPU architecture
- `ansible_processor_cores`: Number of CPU cores
- `ansible_memtotal_mb`: Total memory in MB
- `ansible_default_ipv4.address`: Default IPv4 address
- `ansible_all_ipv4_addresses`: All IPv4 addresses

### Disabling Fact Gathering

```yaml
---
- name: Quick playbook
  hosts: all
  gather_facts: no
  
  tasks:
    - name: Run command
      command: uptime
```

### Custom Facts

Create `/etc/ansible/facts.d/custom.fact`:

```json
{
  "application": {
    "name": "myapp",
    "version": "1.2.3"
  }
}
```

Access with:

```yaml
- name: Display custom fact
  debug:
    msg: "{{ ansible_local.custom.application.name }}"
```

---

## 13. Conditionals

Execute tasks based on conditions.

### When Statement

```yaml
- name: Install Apache on Debian
  apt:
    name: apache2
    state: present
  when: ansible_os_family == "Debian"

- name: Install Apache on RedHat
  yum:
    name: httpd
    state: present
  when: ansible_os_family == "RedHat"
```

### Multiple Conditions

```yaml
- name: Install on production Ubuntu
  apt:
    name: nginx
    state: present
  when:
    - ansible_distribution == "Ubuntu"
    - environment == "production"
```

### Or Condition

```yaml
- name: Install on Debian or Ubuntu
  apt:
    name: nginx
    state: present
  when: ansible_distribution == "Debian" or ansible_distribution == "Ubuntu"
```

### Check Variable Defined

```yaml
- name: Use variable if defined
  debug:
    msg: "{{ my_variable }}"
  when: my_variable is defined
```

### Check Command Result

```yaml
- name: Check if file exists
  stat:
    path: /etc/app/config.conf
  register: config_file

- name: Create config if missing
  template:
    src: config.j2
    dest: /etc/app/config.conf
  when: not config_file.stat.exists
```

---

## 14. Loops

Repeat tasks with different values.

### Simple Loop

```yaml
- name: Install packages
  apt:
    name: "{{ item }}"
    state: present
  loop:
    - nginx
    - git
    - vim
```

### Loop with Dictionary

```yaml
- name: Create users
  user:
    name: "{{ item.name }}"
    groups: "{{ item.groups }}"
    state: present
  loop:
    - { name: 'alice', groups: 'sudo' }
    - { name: 'bob', groups: 'users' }
    - { name: 'charlie', groups: 'sudo,docker' }
```

### Loop over Variables

```yaml
- name: Create directories
  file:
    path: "{{ item }}"
    state: directory
  loop: "{{ directories }}"
```

### Loop with Range

```yaml
- name: Create numbered files
  file:
    path: "/tmp/file{{ item }}.txt"
    state: touch
  loop: "{{ range(1, 6) | list }}"
```

This creates file1.txt through file5.txt.

### Loop with Dictionary Items

```yaml
- name: Set variables
  debug:
    msg: "{{ item.key }} = {{ item.value }}"
  loop: "{{ my_dict | dict2items }}"
```

---

## 15. Handlers

Handlers run when notified by tasks.

```yaml
---
- name: Configure web server
  hosts: webservers
  become: yes
  
  tasks:
    - name: Copy Apache config
      template:
        src: apache.conf.j2
        dest: /etc/apache2/apache2.conf
      notify: Restart Apache
    
    - name: Copy site config
      template:
        src: site.conf.j2
        dest: /etc/apache2/sites-available/site.conf
      notify: Restart Apache
  
  handlers:
    - name: Restart Apache
      service:
        name: apache2
        state: restarted
```

Handlers run once at the end of a play, even if notified multiple times.

### Multiple Handlers

```yaml
tasks:
  - name: Update config
    template:
      src: app.conf.j2
      dest: /etc/app/app.conf
    notify:
      - Restart app
      - Clear cache

handlers:
  - name: Restart app
    service:
      name: app
      state: restarted
  
  - name: Clear cache
    command: /usr/bin/clear-cache
```

### Force Handler Execution

```yaml
- name: Force handlers to run now
  meta: flush_handlers
```

---

## 16. Templates

Templates use Jinja2 syntax to generate configuration files.

### Basic Template

Create `nginx.conf.j2`:

```jinja2
server {
    listen {{ http_port }};
    server_name {{ server_name }};
    
    root /var/www/{{ app_name }};
    
    location / {
        try_files $uri $uri/ =404;
    }
}
```

**Use in playbook:**

```yaml
- name: Copy nginx config
  template:
    src: nginx.conf.j2
    dest: /etc/nginx/sites-available/site.conf
```

### Conditional Blocks

```jinja2
{% if enable_ssl %}
server {
    listen 443 ssl;
    ssl_certificate {{ ssl_cert_path }};
    ssl_certificate_key {{ ssl_key_path }};
}
{% endif %}
```

### Loops in Templates

```jinja2
upstream backend {
{% for host in backend_servers %}
    server {{ host }}:{{ backend_port }};
{% endfor %}
}
```

### Filters

```jinja2
# Convert to uppercase
{{ server_name | upper }}

# Default value
{{ timeout | default(30) }}

# Join list
{{ dns_servers | join(',') }}

# Convert to JSON
{{ config_dict | to_json }}

# Convert to YAML
{{ config_dict | to_yaml }}
```

### Comments

```jinja2
{# This is a comment and won't appear in output #}
```

---

## 17. Roles

Roles organize playbooks into reusable components.

### Role Directory Structure

```
roles/
  webserver/
    tasks/
      main.yml
    handlers/
      main.yml
    templates/
      nginx.conf.j2
    files/
      index.html
    vars/
      main.yml
    defaults/
      main.yml
    meta/
      main.yml
```

### Creating a Role

```bash
ansible-galaxy init webserver
```

### Role Files

**roles/webserver/tasks/main.yml:**

```yaml
---
- name: Install nginx
  apt:
    name: nginx
    state: present

- name: Copy nginx config
  template:
    src: nginx.conf.j2
    dest: /etc/nginx/nginx.conf
  notify: Restart nginx

- name: Start nginx
  service:
    name: nginx
    state: started
    enabled: yes
```

**roles/webserver/handlers/main.yml:**

```yaml
---
- name: Restart nginx
  service:
    name: nginx
    state: restarted
```

**roles/webserver/defaults/main.yml:**

```yaml
---
http_port: 80
worker_processes: auto
```

**roles/webserver/vars/main.yml:**

```yaml
---
nginx_user: www-data
```

### Using Roles

```yaml
---
- name: Configure web servers
  hosts: webservers
  become: yes
  
  roles:
    - webserver
    - { role: database, database_name: myapp }
```

### Role with Variables

```yaml
---
- name: Configure web servers
  hosts: webservers
  become: yes
  
  roles:
    - role: webserver
      vars:
        http_port: 8080
        worker_processes: 4
```

### Ansible Galaxy Roles

Install community roles:

```bash
ansible-galaxy install geerlingguy.nginx
```

Use in playbook:

```yaml
---
- name: Install nginx
  hosts: webservers
  become: yes
  
  roles:
    - geerlingguy.nginx
```

---

## 18. Practical Example: Complete Web Application Deployment

This example deploys a Python web application with nginx as a reverse proxy.

**inventory.ini:**

```ini
[webservers]
web1.example.com
web2.example.com

[webservers:vars]
ansible_user=ubuntu
ansible_ssh_private_key_file=~/.ssh/id_rsa
```

**playbook.yml:**

```yaml
---
- name: Deploy web application
  hosts: webservers
  become: yes
  
  vars:
    app_name: myapp
    app_user: webapp
    app_dir: /opt/myapp
    python_version: "3.10"
    http_port: 80
    app_port: 8000
  
  tasks:
    - name: Update apt cache
      apt:
        update_cache: yes
        cache_valid_time: 3600
    
    - name: Install system packages
      apt:
        name:
          - python3
          - python3-pip
          - python3-venv
          - nginx
          - git
        state: present
    
    - name: Create application user
      user:
        name: "{{ app_user }}"
        system: yes
        shell: /bin/bash
        create_home: no
    
    - name: Create application directory
      file:
        path: "{{ app_dir }}"
        state: directory
        owner: "{{ app_user }}"
        group: "{{ app_user }}"
        mode: '0755'
    
    - name: Clone application repository
      git:
        repo: https://github.com/user/myapp.git
        dest: "{{ app_dir }}"
        version: main
      become_user: "{{ app_user }}"
      notify: Restart application
    
    - name: Create virtual environment
      command: python3 -m venv {{ app_dir }}/venv
      args:
        creates: "{{ app_dir }}/venv/bin/activate"
      become_user: "{{ app_user }}"
    
    - name: Install Python dependencies
      pip:
        requirements: "{{ app_dir }}/requirements.txt"
        virtualenv: "{{ app_dir }}/venv"
      become_user: "{{ app_user }}"
      notify: Restart application
    
    - name: Copy systemd service file
      template:
        src: myapp.service.j2
        dest: /etc/systemd/system/myapp.service
      notify: Restart application
    
    - name: Enable and start application service
      systemd:
        name: myapp
        enabled: yes
        state: started
        daemon_reload: yes
    
    - name: Copy nginx configuration
      template:
        src: nginx-site.conf.j2
        dest: /etc/nginx/sites-available/myapp
      notify: Restart nginx
    
    - name: Enable nginx site
      file:
        src: /etc/nginx/sites-available/myapp
        dest: /etc/nginx/sites-enabled/myapp
        state: link
      notify: Restart nginx
    
    - name: Remove default nginx site
      file:
        path: /etc/nginx/sites-enabled/default
        state: absent
      notify: Restart nginx
    
    - name: Ensure nginx is started
      service:
        name: nginx
        state: started
        enabled: yes
  
  handlers:
    - name: Restart application
      systemd:
        name: myapp
        state: restarted
    
    - name: Restart nginx
      service:
        name: nginx
        state: restarted
```

**templates/myapp.service.j2:**

```jinja2
[Unit]
Description={{ app_name }} web application
After=network.target

[Service]
Type=simple
User={{ app_user }}
WorkingDirectory={{ app_dir }}
Environment="PATH={{ app_dir }}/venv/bin"
ExecStart={{ app_dir }}/venv/bin/python {{ app_dir }}/app.py
Restart=always

[Install]
WantedBy=multi-user.target
```

**templates/nginx-site.conf.j2:**

```jinja2
server {
    listen {{ http_port }};
    server_name {{ ansible_fqdn }};
    
    location / {
        proxy_pass http://127.0.0.1:{{ app_port }};
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
    
    location /static {
        alias {{ app_dir }}/static;
    }
}
```

**Deploy the application:**

```bash
ansible-playbook playbook.yml -i inventory.ini
```

---

## 19. Dynamic Inventory

Dynamic inventories query cloud providers for current infrastructure.

### AWS EC2 Dynamic Inventory

Install boto3:

```bash
pip install boto3
```

Create `aws_ec2.yml`:

```yaml
plugin: aws_ec2
regions:
  - eu-west-1
  - us-east-1
keyed_groups:
  - key: tags.Environment
    prefix: env
  - key: tags.Role
    prefix: role
hostnames:
  - tag:Name
compose:
  ansible_host: public_ip_address
```

**Use dynamic inventory:**

```bash
ansible-playbook playbook.yml -i aws_ec2.yml
```

### Azure Dynamic Inventory

Create `azure_rm.yml`:

```yaml
plugin: azure_rm
include_vm_resource_groups:
  - myresourcegroup
auth_source: auto
keyed_groups:
  - key: tags.environment
    prefix: env
  - key: tags.role
    prefix: role
```

### GCP Dynamic Inventory

Create `gcp_compute.yml`:

```yaml
plugin: gcp_compute
projects:
  - my-project
auth_kind: serviceaccount
service_account_file: /path/to/credentials.json
keyed_groups:
  - key: labels.environment
    prefix: env
  - key: labels.role
    prefix: role
```

---

## 20. Ansible Vault

Encrypt sensitive data like passwords and keys.

### Creating Encrypted Files

```bash
ansible-vault create secrets.yml
```

Enter vault password and edit the file:

```yaml
db_password: supersecret
api_key: abc123xyz
```

### Encrypting Existing Files

```bash
ansible-vault encrypt vars.yml
```

### Editing Encrypted Files

```bash
ansible-vault edit secrets.yml
```

### Viewing Encrypted Files

```bash
ansible-vault view secrets.yml
```

### Decrypting Files

```bash
ansible-vault decrypt secrets.yml
```

### Using Vault in Playbooks

```yaml
---
- name: Deploy application
  hosts: webservers
  vars_files:
    - secrets.yml
  
  tasks:
    - name: Configure database
      template:
        src: db.conf.j2
        dest: /etc/app/db.conf
```

**Run with vault:**

```bash
ansible-playbook playbook.yml -i inventory.ini --ask-vault-pass
```

### Vault Password File

Create a file with your vault password:

```bash
echo "mypassword" > .vault_pass
chmod 600 .vault_pass
```

**Run with password file:**

```bash
ansible-playbook playbook.yml -i inventory.ini --vault-password-file .vault_pass
```

Add to ansible.cfg:

```ini
[defaults]
vault_password_file = .vault_pass
```

### Encrypting Strings

```bash
ansible-vault encrypt_string 'supersecret' --name 'db_password'
```

Paste the output into your playbook:

```yaml
db_password: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          66386439653836336662306662323...
```

---

## 21. Error Handling

Control how Ansible handles failures.

### Ignore Errors

```yaml
- name: Try to stop service
  service:
    name: app
    state: stopped
  ignore_errors: yes
```

### Failed When

```yaml
- name: Run command
  command: /usr/bin/check-status
  register: result
  failed_when: "'ERROR' in result.stdout"
```

### Changed When

```yaml
- name: Check service status
  command: systemctl is-active nginx
  register: result
  changed_when: false
```

### Block, Rescue, Always

```yaml
tasks:
  - name: Handle errors
    block:
      - name: Copy file
        copy:
          src: file.txt
          dest: /opt/file.txt
      
      - name: Run command
        command: /usr/bin/process-file
    
    rescue:
      - name: Handle failure
        debug:
          msg: "Something went wrong"
      
      - name: Cleanup
        file:
          path: /opt/file.txt
          state: absent
    
    always:
      - name: Always run this
        debug:
          msg: "This always runs"
```

### Any Errors Fatal

```yaml
---
- name: Critical updates
  hosts: webservers
  any_errors_fatal: true
  
  tasks:
    - name: Update packages
      apt:
        upgrade: yes
```

---

## 22. Tags

Organize and selectively run tasks.

### Defining Tags

```yaml
---
- name: Deploy application
  hosts: webservers
  
  tasks:
    - name: Install packages
      apt:
        name: nginx
        state: present
      tags:
        - install
        - packages
    
    - name: Copy configuration
      template:
        src: nginx.conf.j2
        dest: /etc/nginx/nginx.conf
      tags:
        - config
        - deploy
    
    - name: Restart service
      service:
        name: nginx
        state: restarted
      tags:
        - deploy
        - restart
```

### Running Specific Tags

```bash
ansible-playbook playbook.yml --tags "install"
ansible-playbook playbook.yml --tags "config,deploy"
```

### Skipping Tags

```bash
ansible-playbook playbook.yml --skip-tags "restart"
```

### Special Tags

```yaml
- name: Always run this
  debug:
    msg: "Always runs"
  tags:
    - always

- name: Never run automatically
  debug:
    msg: "Only runs with --tags never"
  tags:
    - never
```

---

## 23. Debugging Playbooks

Troubleshoot issues in playbooks.

### Debug Module

```yaml
- name: Display variable
  debug:
    var: ansible_hostname

- name: Display message
  debug:
    msg: "The value is {{ my_variable }}"

- name: Display with verbosity
  debug:
    msg: "Debug info"
    verbosity: 2
```

### Register Variables

```yaml
- name: Run command
  command: whoami
  register: user_output

- name: Display output
  debug:
    var: user_output

- name: Display stdout only
  debug:
    var: user_output.stdout
```

### Check Mode

```bash
ansible-playbook playbook.yml --check
```

### Diff Mode

```bash
ansible-playbook playbook.yml --diff
```

### Step Mode

```bash
ansible-playbook playbook.yml --step
```

### Start at Task

```bash
ansible-playbook playbook.yml --start-at-task="Install packages"
```

### Verbose Output

```bash
ansible-playbook playbook.yml -vvv
```

---

## 24. Ansible Configuration

Customize Ansible behaviour with configuration files.

### Configuration File Locations

Ansible searches in this order:

1. `ANSIBLE_CONFIG` environment variable
2. `./ansible.cfg` in current directory
3. `~/.ansible.cfg` in home directory
4. `/etc/ansible/ansible.cfg`

### Common Settings

**ansible.cfg:**

```ini
[defaults]
inventory = inventory.ini
remote_user = ansible
host_key_checking = False
retry_files_enabled = False
gathering = smart
fact_caching = jsonfile
fact_caching_connection = /tmp/ansible_facts
fact_caching_timeout = 86400
forks = 20
timeout = 30

[privilege_escalation]
become = True
become_method = sudo
become_user = root
become_ask_pass = False

[ssh_connection]
ssh_args = -o ControlMaster=auto -o ControlPersist=60s
pipelining = True
```

### Environment Variables

```bash
export ANSIBLE_CONFIG=~/my-ansible.cfg
export ANSIBLE_INVENTORY=~/inventory.ini
export ANSIBLE_REMOTE_USER=deploy
export ANSIBLE_HOST_KEY_CHECKING=False
```

---

## 25. Best Practices

### Playbook Organization

- Use meaningful names for tasks
- Keep playbooks focused and modular
- Use roles for complex configurations
- Organize files in a standard structure
- Document variables and their purpose

### Idempotency

Write tasks that can run multiple times without changing the result:

```yaml
# Good - idempotent
- name: Ensure directory exists
  file:
    path: /opt/app
    state: directory

# Bad - not idempotent
- name: Create directory
  command: mkdir /opt/app
```

### Variable Naming

- Use descriptive names
- Prefix role variables with role name
- Use underscores not hyphens
- Keep names lowercase

### Security

- Use Ansible Vault for secrets
- Never commit passwords to version control
- Use SSH keys instead of passwords
- Limit sudo access
- Use become only when necessary

### Testing

- Test in development first
- Use check mode for dry runs
- Use molecule for role testing
- Validate syntax before running
- Use version control

### Performance

- Disable fact gathering when not needed
- Use pipelining in SSH connections
- Increase forks for parallel execution
- Use async tasks for long operations
- Cache facts between runs

---

## 26. Ansible with CI/CD

### GitHub Actions

```yaml
name: Deploy

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v4
      
      - name: Setup Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.10'
      
      - name: Install Ansible
        run: pip install ansible
      
      - name: Setup SSH key
        run: |
          mkdir -p ~/.ssh
          echo "${{ secrets.SSH_PRIVATE_KEY }}" > ~/.ssh/id_rsa
          chmod 600 ~/.ssh/id_rsa
      
      - name: Run Ansible playbook
        run: ansible-playbook playbook.yml -i inventory.ini
        env:
          ANSIBLE_HOST_KEY_CHECKING: False
```

### GitLab CI

```yaml
stages:
  - deploy

deploy:
  stage: deploy
  image: python:3.10
  before_script:
    - pip install ansible
    - eval $(ssh-agent -s)
    - echo "$SSH_PRIVATE_KEY" | ssh-add -
    - mkdir -p ~/.ssh
    - echo "$SSH_KNOWN_HOSTS" > ~/.ssh/known_hosts
  script:
    - ansible-playbook playbook.yml -i inventory.ini
  only:
    - main
```

### Jenkins

```groovy
pipeline {
    agent any
    
    stages {
        stage('Deploy') {
            steps {
                ansiblePlaybook(
                    playbook: 'playbook.yml',
                    inventory: 'inventory.ini',
                    credentialsId: 'ansible-ssh-key'
                )
            }
        }
    }
}
```

---

## 27. Using Ansible with Terraform

Terraform provisions infrastructure while Ansible configures it. This combination provides a complete infrastructure as code solution.

### Workflow

1. Terraform creates infrastructure (VMs, networks, security groups)
2. Terraform generates Ansible inventory from created resources
3. Ansible configures the provisioned servers
4. Applications are deployed and services configured

### Terraform Configuration

**main.tf:**

```hcl
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

provider "aws" {
  region = var.region
}

# Create EC2 instances
resource "aws_instance" "web" {
  count         = var.instance_count
  ami           = data.aws_ami.ubuntu.id
  instance_type = var.instance_type
  key_name      = var.key_name
  
  vpc_security_group_ids = [aws_security_group.web.id]
  subnet_id              = aws_subnet.public[count.index % length(aws_subnet.public)].id
  
  tags = {
    Name        = "web-${count.index + 1}"
    Environment = var.environment
    Role        = "webserver"
  }
}

# Generate Ansible inventory
resource "local_file" "ansible_inventory" {
  content = templatefile("${path.module}/templates/inventory.tpl", {
    web_servers = aws_instance.web[*]
  })
  filename = "${path.module}/inventory.ini"
}

# Generate Ansible variables
resource "local_file" "ansible_vars" {
  content = jsonencode({
    environment     = var.environment
    app_version     = var.app_version
    database_host   = aws_db_instance.main.address
    database_name   = var.database_name
  })
  filename = "${path.module}/terraform_vars.json"
}

data "aws_ami" "ubuntu" {
  most_recent = true
  owners      = ["099720109477"]
  
  filter {
    name   = "name"
    values = ["ubuntu/images/hvm-ssd/ubuntu-22.04-amd64-server-*"]
  }
}
```

**templates/inventory.tpl:**

```ini
[webservers]
%{ for server in web_servers ~}
${server.tags.Name} ansible_host=${server.public_ip} ansible_user=ubuntu
%{ endfor ~}

[webservers:vars]
ansible_ssh_private_key_file=~/.ssh/terraform-key.pem
environment=${web_servers[0].tags.Environment}
```

**variables.tf:**

```hcl
variable "region" {
  description = "AWS region"
  type        = string
  default     = "eu-west-1"
}

variable "instance_count" {
  description = "Number of web servers"
  type        = number
  default     = 2
}

variable "instance_type" {
  description = "EC2 instance type"
  type        = string
  default     = "t3.micro"
}

variable "environment" {
  description = "Environment name"
  type        = string
}

variable "key_name" {
  description = "SSH key name"
  type        = string
}

variable "app_version" {
  description = "Application version"
  type        = string
  default     = "1.0.0"
}

variable "database_name" {
  description = "Database name"
  type        = string
  default     = "myapp"
}
```

### Ansible Playbook

**playbook.yml:**

```yaml
---
- name: Configure web servers
  hosts: webservers
  become: yes
  
  vars_files:
    - terraform_vars.json
  
  tasks:
    - name: Wait for system to become reachable
      wait_for_connection:
        timeout: 300
    
    - name: Update apt cache
      apt:
        update_cache: yes
        cache_valid_time: 3600
    
    - name: Install required packages
      apt:
        name:
          - nginx
          - python3-pip
          - git
        state: present
    
    - name: Deploy application version {{ app_version }}
      git:
        repo: https://github.com/user/myapp.git
        dest: /opt/myapp
        version: "{{ app_version }}"
      notify: Restart application
    
    - name: Configure database connection
      template:
        src: config.j2
        dest: /opt/myapp/config.conf
      notify: Restart application
    
    - name: Ensure application is running
      systemd:
        name: myapp
        state: started
        enabled: yes
  
  handlers:
    - name: Restart application
      systemd:
        name: myapp
        state: restarted
```

**templates/config.j2:**

```jinja2
[database]
host = {{ database_host }}
name = {{ database_name }}
port = 5432

[application]
environment = {{ environment }}
version = {{ app_version }}
```

### Deployment Script

**deploy.sh:**

```bash
#!/bin/bash

set -e

echo "Provisioning infrastructure with Terraform..."
cd terraform/
terraform init
terraform apply -auto-approve

echo "Waiting for instances to be ready..."
sleep 30

echo "Configuring servers with Ansible..."
cd ../ansible/
ansible-playbook -i ../terraform/inventory.ini playbook.yml

echo "Deployment complete!"
```

### Using Terraform Provisioners

You can also use Terraform's provisioner to trigger Ansible:

```hcl
resource "null_resource" "ansible_provisioner" {
  depends_on = [aws_instance.web]
  
  provisioner "local-exec" {
    command = "ansible-playbook -i inventory.ini playbook.yml"
    working_dir = "${path.module}"
  }
  
  triggers = {
    instance_ids = join(",", aws_instance.web[*].id)
  }
}
```

### Dynamic Inventory with Terraform Outputs

**outputs.tf:**

```hcl
output "web_server_ips" {
  description = "Web server IP addresses"
  value       = aws_instance.web[*].public_ip
}

output "ansible_inventory" {
  description = "Ansible inventory in JSON format"
  value = {
    webservers = {
      hosts = {
        for idx, instance in aws_instance.web :
        instance.tags.Name => {
          ansible_host = instance.public_ip
          ansible_user = "ubuntu"
          environment  = var.environment
        }
      }
    }
  }
}
```

Generate JSON inventory:

```bash
terraform output -json ansible_inventory > inventory.json
ansible-playbook -i inventory.json playbook.yml
```

### Complete Workflow Example

**Step 1: Provision Infrastructure**

```bash
cd terraform
terraform init
terraform apply -var="environment=production"
```

**Step 2: Wait for Instances**

```bash
sleep 30
```

**Step 3: Configure with Ansible**

```bash
ansible-playbook -i inventory.ini playbook.yml
```

**Step 4: Verify Deployment**

```bash
ansible webservers -i inventory.ini -m shell -a "systemctl status myapp"
```

### Benefits of Terraform + Ansible

- **Separation of Concerns**: Terraform handles infrastructure, Ansible handles configuration
- **Idempotency**: Both tools ensure consistent state
- **Version Control**: Infrastructure and configuration are both code
- **Reusability**: Terraform modules and Ansible roles can be shared
- **Testing**: Changes can be tested before production
- **Documentation**: Code serves as documentation

### Best Practices

- Use Terraform for all infrastructure provisioning
- Use Ansible for all configuration management
- Generate Ansible inventory from Terraform outputs
- Pass variables from Terraform to Ansible via JSON files
- Keep Terraform and Ansible code in the same repository
- Use CI/CD pipelines to orchestrate both tools
- Test infrastructure and configuration changes together
- Use Terraform workspaces and Ansible inventories for multiple environments

---

## 28. Testing Ansible

### Syntax Checking

```bash
ansible-playbook playbook.yml --syntax-check
```

### Dry Run

```bash
ansible-playbook playbook.yml --check --diff
```

### Molecule

Framework for testing Ansible roles.

**Install:**

```bash
pip install molecule molecule-docker
```

**Initialize:**

```bash
cd roles/myrole
molecule init scenario
```

**Test:**

```bash
molecule test
```

### Ansible Lint

Check playbooks for best practices:

```bash
pip install ansible-lint
ansible-lint playbook.yml
```

---

## 29. Advanced Topics

Once you master the basics, explore these topics:

- **Ansible Tower/AWX**: Web UI and REST API for Ansible
- **Ansible Collections**: Packaged content including roles, modules and plugins
- **Custom Modules**: Write your own modules in Python
- **Callback Plugins**: Customize Ansible output
- **Connection Plugins**: Support additional connection methods
- **Lookup Plugins**: Retrieve data from external sources
- **Filter Plugins**: Transform data in templates
- **Ansible for Windows**: Manage Windows servers
- **Network Automation**: Configure network devices
- **Container Orchestration**: Manage Docker and Kubernetes
- **Ansible Semaphore**: Alternative web UI for Ansible

---

## 30. Useful Resources

**Official Documentation:**

- [docs.ansible.com](https://docs.ansible.com)
- [galaxy.ansible.com](https://galaxy.ansible.com)

**Module Index:**

- [Module documentation](https://docs.ansible.com/ansible/latest/collections/index_module.html)

**Learning Resources:**

- Ansible examples on GitHub
- Red Hat Ansible tutorials
- Community roles on Ansible Galaxy

---

## Key Takeaway

Ansible transforms server management by treating configuration as code. Understanding inventory, playbooks, modules and roles gives you the foundation to automate any IT task. Start with simple playbooks, use roles to organize code and implement proper testing as your automation grows. Combined with Terraform for infrastructure provisioning, Ansible enables teams to maintain consistent, reproducible environments across development, staging and production with minimal manual intervention.

---