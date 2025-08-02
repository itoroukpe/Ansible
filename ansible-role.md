Here’s a **step-by-step hands-on guide to creating and running Ansible roles**, designed for DevOps learners:

---

## 🧰 Prerequisites

* Ansible installed (`ansible --version`)
* A Linux control node (e.g., Ubuntu)
* An inventory of target nodes (e.g., VMs with SSH access)
* SSH access set up (key-based preferred)

---

## 📁 Step 1: Create a Project Structure

```bash
mkdir -p ansible-project/roles
cd ansible-project
```

You can initialize a role using:

```bash
ansible-galaxy init roles/webserver
```

This creates a structure like:

```
roles/
└── webserver/
    ├── defaults/
    │   └── main.yml
    ├── files/
    ├── handlers/
    │   └── main.yml
    ├── meta/
    │   └── main.yml
    ├── tasks/
    │   └── main.yml
    ├── templates/
    ├── tests/
    │   ├── inventory
    │   └── test.yml
    └── vars/
        └── main.yml
```

---

## 📝 Step 2: Define Tasks

Edit `roles/webserver/tasks/main.yml`:

```yaml
---
- name: Install Apache
  apt:
    name: apache2
    state: present
    update_cache: yes

- name: Start Apache service
  service:
    name: apache2
    state: started
    enabled: true
```

---

## 🧩 Step 3: Set Default Variables (Optional)

Edit `roles/webserver/defaults/main.yml`:

```yaml
---
http_port: 80
```

---

## 📄 Step 4: Create the Playbook

In your project root, create `site.yml`:

```yaml
---
- name: Configure Web Server
  hosts: web
  become: yes
  roles:
    - webserver
```

---

## 📂 Step 5: Create an Inventory File

Create a file called `inventory.ini`:

```ini
[web]
192.168.56.11 ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/id_rsa
```

---

## 🚀 Step 6: Run the Playbook

```bash
ansible-playbook -i inventory.ini site.yml
```

---

## 🔄 Step 7: Add More Roles (Optional)

Repeat the same process using:

```bash
ansible-galaxy init roles/dbserver
```

---

## ✅ Bonus Tips

* Use `handlers/main.yml` for service restarts:

```yaml
- name: Restart Apache
  service:
    name: apache2
    state: restarted
```

* Trigger from a task:

```yaml
notify: Restart Apache
```

---

