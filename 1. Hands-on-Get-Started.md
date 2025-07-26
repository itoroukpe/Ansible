Here's a complete hands-on step-by-step guide to get started with **Ansible**:

---

## 🛠️ Step 1: Install Ansible

### ✅ On Ubuntu/Debian

```bash
sudo apt update
sudo apt install software-properties-common
sudo add-apt-repository --yes --update ppa:ansible/ansible
sudo apt install ansible -y
```

### ✅ On RHEL/CentOS

```bash
sudo yum install epel-release -y
sudo yum install ansible -y
```

### ✅ Verify Installation

```bash
ansible --version
```

---

## 📁 Step 2: Set Up Project Directory

```bash
mkdir ~/ansible-demo
cd ~/ansible-demo
```

---

## 📦 Step 3: Inventory File

### ➕ Create an Inventory File

```ini
# inventory.ini
[webservers]
192.168.1.10
192.168.1.11

[dbservers]
192.168.1.20
```

> Use IPs of your VMs or localhost for testing. Make sure SSH is enabled on target machines.

---

## 🔑 Step 4: Configure SSH Access

```bash
ssh-keygen
ssh-copy-id user@192.168.1.10  # Repeat for all target machines
```

Then test:

```bash
ansible -i inventory.ini all -m ping
```

---

## 🔍 Step 5: Use Ad-hoc Commands

### 👉 Examples:

```bash
# Ping all servers
ansible -i inventory.ini all -m ping

# Get uptime of webservers
ansible -i inventory.ini webservers -m command -a "uptime"

# Install Apache (httpd) on CentOS
ansible -i inventory.ini webservers -m yum -a "name=httpd state=present" --become

# Start Apache service
ansible -i inventory.ini webservers -m service -a "name=httpd state=started" --become
```

---

## 📜 Step 6: Write a Simple Playbook

### 📝 `webserver.yml`

```yaml
---
- name: Install and Start Apache
  hosts: webservers
  become: true

  tasks:
    - name: Install Apache
      yum:
        name: httpd
        state: present

    - name: Start Apache
      service:
        name: httpd
        state: started
```

### ▶️ Run the Playbook

```bash
ansible-playbook -i inventory.ini webserver.yml
```

---

## 📚 Step 7: Create and Use Roles

### 🏗️ Directory Structure

```bash
ansible-galaxy init roles/webserver
```

This creates:

```
roles/webserver/
├── defaults/
├── files/
├── handlers/
├── meta/
├── tasks/
├── templates/
└── vars/
```

### ✍️ Add Tasks

Edit `roles/webserver/tasks/main.yml`:

```yaml
---
- name: Install Apache
  yum:
    name: httpd
    state: present

- name: Start Apache
  service:
    name: httpd
    state: started
```

### 📝 Role-based Playbook

```yaml
---
- name: Use webserver role
  hosts: webservers
  become: true
  roles:
    - webserver
```

### ▶️ Run It

```bash
ansible-playbook -i inventory.ini role-playbook.yml
```

---

## 🧪 Extras

### 🔍 Check Facts

```bash
ansible all -m setup -i inventory.ini
```

### 🧰 Useful Modules

* `command`: Run shell commands
* `copy`: Copy files to remote machines
* `template`: Use Jinja2 templating
* `yum` / `apt`: Install packages
* `service`: Manage services
* `user`, `group`: Manage users and groups

---

Would you like a downloadable lab workbook or GitHub repo structure to accompany this guide?
