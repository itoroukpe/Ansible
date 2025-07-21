## 🔧 Configure Ansible Inventory and SSH Access

### 1. **Edit Ansible Inventory File**

Use `vi` or any text editor to modify the inventory file:

```bash
sudo vi /etc/ansible/hosts
```

> If permission is denied, take ownership (optional):

```bash
sudo chown -R ansible:ansible /etc/ansible
```

### 2. **Default SSH Authentication**

Ansible connects to remote hosts via SSH using key-based authentication.

---

## 🔑 3. **Create & Paste Private Key**

Paste your remote private key into a file:

```bash
vi /tmp/classkey.pem
```

> ⚠️ Ensure the key has proper permissions:

```bash
chmod 600 /tmp/classkey.pem
```

---

## 🗂️ 4. **Define Your Inventory File**

Here’s a cleaned-up inventory format:

```ini
[webserver]
52.38.24.142 ansible_user=ubuntu ansible_ssh_private_key_file=/tmp/classkey.pem

[appserver]
35.89.85.124 ansible_user=ubuntu ansible_ssh_private_key_file=/tmp/classkey.pem

[dbservers]
172.31.7.161 ansible_user=ec2-user ansible_ssh_private_key_file=/tmp/classkey.pem

[webservers]
172.31.9.118 ansible_user=ec2-user ansible_ssh_private_key_file=/tmp/classkey.pem
```

> ✅ **Note**:

* `ansible_user` = SSH username on remote server (e.g., `ubuntu` for Ubuntu AMIs, `ec2-user` for Amazon Linux).
* `ansible_ssh_private_key_file` = Path to your `.pem` key used for login.

---

## ✅ 5. **Test Connectivity**

```bash
ansible all -m ping -i /etc/ansible/hosts
```

This will ping all hosts using SSH. You should see a green `pong` response if the connection is successful.

---

