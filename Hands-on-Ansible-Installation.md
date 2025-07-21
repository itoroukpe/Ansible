
## 🐧 Installing Ansible on Ubuntu

### 🧾 Step-by-Step

```bash
# 1. Set the hostname (optional)
sudo hostnamectl set-hostname ansible

# 2. Create a dedicated Ansible user
sudo adduser ansible

# 3. Grant sudo privileges without password
echo "ansible ALL=(ALL) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/ansible

# 4. Switch to ansible user
sudo su - ansible

# 5. Add the Ansible PPA and install
sudo apt update
sudo apt-add-repository --yes --update ppa:ansible/ansible
sudo apt install ansible -y
```

---

## 🧑‍💻 Installing Ansible on Red Hat / Amazon Linux EC2

### 🧾 Step-by-Step

```bash
# 1. Create ansible user and set hostname
sudo useradd ansible
sudo hostnamectl set-hostname ansible

# 2. Grant passwordless sudo
echo "ansible ALL=(ALL) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/ansible

# 3. Switch to ansible user and set password (manual step required)
sudo su - ansible
passwd  # Set password if needed and enable PasswordAuthentication in sshd_config

# 4. Install Python 3 and set as default
sudo yum install python3 -y
sudo alternatives --install /usr/bin/python python /usr/bin/python3 1
sudo alternatives --set python /usr/bin/python3

# 5. Install pip and Ansible
sudo yum install python3-pip -y
pip3 install --user ansible
```

---

✅ **Post-Install Check**

```bash
ansible --version
```

This confirms the installation and shows the installed version.

---

