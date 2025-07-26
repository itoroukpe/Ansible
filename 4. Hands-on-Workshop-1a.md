### **Hands-On Workshop: Ansible Basics**

This hands-on workshop introduces participants to Ansible, focusing on **ad-hoc commands**, **Ansible modules**, and **playbooks**. The exercises will provide practical knowledge for managing IT infrastructure using Ansible.

---

### **Workshop Objectives**
By the end of this workshop, participants will:
1. Understand how to use **ad-hoc commands** for immediate tasks.
2. Learn how to leverage **Ansible modules** for configuration and management.
3. Create and run **Ansible playbooks** for reusable automation.

---

### **Prerequisites**
- A control node (your local machine) with **Ansible installed**.
- At least one managed node (remote server) with SSH access.
- Ensure the control node can connect to the managed nodes via SSH using key-based authentication.

---

### **Part 1: Setting Up Ansible**

1. **Install Ansible on the Control Node**:
   - On Linux:
   - Installing Ansible on Ubuntu
```bash
sudo apt update
sudo hostname ansible
sudo adduser ansible
echo "ansible ALL=(ALL) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/ansible

sudo su - ansible
sudo apt-add-repository ppa:ansible/ansible
sudo apt install ansible -y
```

- Ansible installation on REDHAT EC2
```bash
sudo yum update
sudo useradd ansible
sudo hostname ansible
echo "ansible ALL=(ALL) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/ansible
sudo su - ansible # Enable PassowrdLogin and assign password to ansible user
sudo yum install python3 -y
sudo alternatives --set python /usr/bin/python3
sudo yum -y install python3-pip -y
pip3 install ansible --user
```
 
     
          
   - On macOS:
     ```bash
     brew install ansible
     ```

2. **Verify Installation**:
   ```bash
   ansible --version
   ```

3. **Create an Inventory File**:
   - Create a file named `inventory` and define managed nodes:
     ```
     [web]
     192.168.1.10
     192.168.1.11

     [db]
     192.168.1.20

   ```
   ---
**Create an Ansible Inventory**
```
 vi /etc/ansible/hosts
 sudo chown -R ansible:ansible /etc/ansible

 by default:
  ansible communicate using ssh keys and ansible user  

vi /tmp/k8.pem   #(copy your remote keypair and paste)
```
---
```
 [webserver]
 52.38.24.142 ansible_user=ubuntu ansible_ssh_private_key_file=/tmp/classkey.pem
 [appserver]
 35.89.85.124  ansible_user=ubuntu ansible_ssh_private_key_file=/tmp/classkey.pem 
 [dbservers]
  172.31.7.161 ansible_user=ec2-user ansible_ssh_private_key_file=/tmp/ classkey.pem 
 [webservers]
 172.31.9.118 ansible_user=ec2-user ansible_ssh_private_key_file=/tmp/ classkey.pem 

```

4. **Test Connectivity**:
   Use the `ping` module to verify connection:
   ```bash
   ansible all -i inventory -m ping
   ```

---

### **Part 2: Using Ad-Hoc Commands**

Ansible ad-hoc commands are used for one-off tasks.

1. **Check Uptime**:
   ```bash
   ansible all -i inventory -m command -a "uptime"
   ```

2. **Install a Package**:
   Example: Install `httpd` on web servers.
   ```bash
   ansible web -i inventory -m yum -a "name=httpd state=present"
   ```

3. **Restart a Service**:
   Restart the `httpd` service:
   ```bash
   ansible web -i inventory -m service -a "name=httpd state=restarted"
   ```

4. **Copy Files**:
   Copy a local file to all managed nodes:
   ```bash
   ansible all -i inventory -m copy -a "src=/path/to/local/file dest=/path/to/remote/file"
   ```

5. **Gather Facts**:
   Collect system information from all nodes:
   ```bash
   ansible all -i inventory -m setup
   ```

---

### **Part 3: Exploring Ansible Modules**

Ansible modules are reusable, standalone scripts for specific tasks.

1. **Using the `apt` Module** (for Debian-based systems):
   - Install `nginx`:
     ```bash
     ansible all -i inventory -m apt -a "name=nginx state=latest"
     ```

2. **Using the `file` Module**:
   - Create a directory:
     ```bash
     ansible all -i inventory -m file -a "path=/tmp/ansible-demo state=directory"
     ```

3. **Using the `cron` Module**:
   - Add a cron job to clear logs:
     ```bash
     ansible all -i inventory -m cron -a "name='clear logs' minute=0 hour=0 job='/usr/bin/find /var/log -type f -delete'"
     ```

4. **Using the `debug` Module**:
   - Output a message:
     ```bash
     ansible all -i inventory -m debug -a "msg='Hello from Ansible\!'"
     ```

---

### **Part 4: Writing and Running Playbooks**

Playbooks allow you to define complex automation tasks in YAML.

1. **Create a Basic Playbook**:
   Create a file named `site.yml`:
   ```yaml
   - name: Configure Web Servers
     hosts: web
     tasks:
       - name: Install Nginx
         apt:
           name: nginx
           state: latest
           update_cache: yes

       - name: Start Nginx Service
         service:
           name: nginx
           state: started
           enabled: yes
   ```

2. **Run the Playbook**:
   ```bash
   ansible-playbook -i inventory site.yml
   ```

3. **Verify Results**:
   Check that `nginx` is installed and running on the web servers:
   ```bash
   ansible web -i inventory -m command -a "systemctl status nginx"
   ```

---

### **Part 5: Advanced Playbook Example**

1. **Multi-Host Playbook**:
   Update the `site.yml` to include database configuration:
   ```yaml
   - name: Configure Web and Database Servers
     hosts: all
     tasks:
       - name: Install Common Tools
         apt:
           name: "{{ item }}"
           state: present
         with_items:
           - curl
           - vim

   - name: Configure Database Server
     hosts: db
     tasks:
       - name: Install MySQL
         apt:
           name: mysql-server
           state: latest
           update_cache: yes
   ```

2. **Run the Updated Playbook**:
   ```bash
   ansible-playbook -i inventory site.yml
   ```

---

### **Part 6: Best Practices**

1. **Use Variables**:
   Define variables in a `vars.yml` file:
   ```yaml
   web_packages:
     - nginx
   db_packages:
     - mysql-server
   ```

   Update `site.yml` to use variables:
   ```yaml
   - name: Install Packages
     hosts: web
     tasks:
       - name: Install Web Packages
         apt:
           name: "{{ web_packages }}"
           state: latest
   ```

   Include variables in the playbook:
   ```bash
   ansible-playbook -i inventory site.yml --extra-vars "@vars.yml"
   ```

2. **Organize Files with Roles**:
   Use roles for better structure:
   ```bash
   ansible-galaxy init webserver
   ```

3. **Enable Logging**:
   Add a log file to `ansible.cfg`:
   ```ini
   [defaults]
   log_path = ./ansible.log
   ```

---

### **Conclusion**
This workshop covers the essentials of Ansible, including ad-hoc commands, modules, and playbooks. By mastering these topics, participants will be able to automate infrastructure tasks efficiently and follow best practices for scalability and maintainability. Encourage participants to explore more advanced Ansible features like roles, Ansible Vault, and dynamic inventories for continued learning.
