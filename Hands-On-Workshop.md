### **Hands-On Workshop for Ansible**
This workshop is designed to provide participants with hands-on experience in Ansible, focusing on ad-hoc commands, modules, and writing and executing playbooks. By the end of the workshop, participants will have practical knowledge of using Ansible to automate tasks across multiple systems.

---

### **Workshop Agenda**
1. Introduction to Ansible
2. Ad-hoc Commands and Ansible Modules
3. Writing Ansible Playbooks
4. Step-by-Step Playbook Exercises

---

### **1. Prerequisites**
- Installed **Ansible** on the control node.
- SSH access to all managed nodes with pre-configured keys.
- A basic understanding of YAML.
- A set of test machines (at least two) for practicing.

---

### **2. Ansible Ad-hoc Commands and Modules**
#### **Ad-hoc Command Examples:**
- **Ping all hosts:**
  ```bash
  ansible all -m ping
  ```
- **Check disk usage:**
  ```bash
  ansible all -a "df -h"
  ```
- **Install a package:**
  ```bash
  ansible all -m yum -a "name=httpd state=present" --become
  ```

---

### **3. Writing and Executing Playbooks**
Ansible playbooks are YAML files that define a series of tasks to be executed on managed nodes.

---

### **Step-by-Step Playbook Exercises**

#### **1. Ping Module Playbook to Test Connection**
- File: `ping_test.yml`
```yaml
- name: Test connection to all hosts
  hosts: all
  tasks:
    - name: Ping all hosts
      ansible.builtin.ping:
```
- Run the playbook:
  ```bash
  ansible-playbook ping_test.yml
  ```

---

#### **2. File Module Playbook to Create a File**
- File: `create_file.yml`
```yaml
- name: Create a file on all hosts
  hosts: all
  tasks:
    - name: Create a file
      ansible.builtin.file:
        path: /tmp/ansible_test_file.txt
        state: touch
```
- Run the playbook:
  ```bash
  ansible-playbook create_file.yml
  ```

---

#### **3. Playbook to Install Apache Server and Start It**
- File: `install_apache.yml`
```yaml
- name: Install and start Apache server
  hosts: all
  become: true
  tasks:
    - name: Install Apache
      ansible.builtin.yum:
        name: httpd
        state: present

    - name: Start and enable Apache
      ansible.builtin.service:
        name: httpd
        state: started
        enabled: true
```
- Run the playbook:
  ```bash
  ansible-playbook install_apache.yml
  ```

---

#### **4. Ping Module Playbook to Copy Inventory File**
- File: `copy_inventory.yml`
```yaml
- name: Copy ansible inventory file to all hosts
  hosts: all
  tasks:
    - name: Copy inventory file
      ansible.builtin.copy:
        src: /etc/ansible/hosts
        dest: /tmp/inventory_copy
```
- Run the playbook:
  ```bash
  ansible-playbook copy_inventory.yml
  ```

---

#### **5. Playbook to Install HTTP Server, Wget, and Vim Using Loops**
- File: `install_packages.yml`
```yaml
- name: Install multiple packages using a loop
  hosts: all
  become: true
  tasks:
    - name: Install packages
      ansible.builtin.yum:
        name: "{{ item }}"
        state: present
      loop:
        - httpd
        - wget
        - vim
```
- Run the playbook:
  ```bash
  ansible-playbook install_packages.yml
  ```

---

#### **6. Playbook to Create a User**
- File: `create_user.yml`
```yaml
- name: Create a user
  hosts: all
  become: true
  tasks:
    - name: Add user
      ansible.builtin.user:
        name: ansible_user
        state: present
        shell: /bin/bash
```
- Run the playbook:
  ```bash
  ansible-playbook create_user.yml
  ```

---

#### **7. Playbook to Clone a GitHub Repository**
- File: `clone_repo.yml`
```yaml
- name: Clone GitHub repository
  hosts: all
  tasks:
    - name: Clone repository
      ansible.builtin.git:
        repo: https://github.com/itoroukpe/AnsiblePlaybooks.git
        dest: /tmp/AnsiblePlaybooks
```
- Run the playbook:
  ```bash
  ansible-playbook clone_repo.yml
  ```

---

#### **8. Playbook to Install Jenkins, Maven, SonarQube, Nexus, Docker**
- File: `install_tools.yml`
```bash

---
- name: Install Jenkins, Maven, SonarQube, Nexus, and Docker
  hosts: all
  become: true
  tasks:
    - name: Update APT cache
      ansible.builtin.apt:
        update_cache: yes

    - name: Add Jenkins GPG key to keyrings
      ansible.builtin.shell: |
        curl -fsSL https://pkg.jenkins.io/debian/jenkins.io.key | gpg --dearmor -o /usr/share/keyrings/jenkins-keyring.gpg
      args:
        creates: /usr/share/keyrings/jenkins-keyring.gpg

    - name: Add Jenkins repository
      ansible.builtin.apt_repository:
        repo: "deb [signed-by=/usr/share/keyrings/jenkins-keyring.gpg] https://pkg.jenkins.io/debian binary/"
        state: present

    - name: Update APT cache after adding Jenkins repository
      ansible.builtin.apt:
        update_cache: yes

    - name: Install Jenkins
      ansible.builtin.apt:
        name: jenkins
        state: present

    - name: Install Maven
      ansible.builtin.apt:
        name: maven
        state: present

    - name: Add SonarQube repository and install SonarQube
      ansible.builtin.shell: |
        wget -qO - https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-latest.zip -P /opt
      args:
        creates: /opt/sonarqube-latest.zip

    - name: Add Nexus repository and install Nexus
      ansible.builtin.shell: |
        wget -qO - https://download.sonatype.com/nexus/3/nexus-3-latest-unix.tar.gz -P /opt
      args:
        creates: /opt/nexus-3-latest-unix.tar.gz

    - name: Install Docker
      ansible.builtin.apt:
        name: docker.io
        state: present

    - name: Start Docker
      ansible.builtin.service:
        name: docker
        state: started
        enabled: true

```

#### **9. Playbook to Install a Kubernetes Cluster**
- File: `install_k8s.yml`
```yaml
- name: Install Kubernetes cluster
  hosts: all
  become: true
  tasks:
    - name: Install kubeadm, kubelet, and kubectl
      ansible.builtin.yum:
        name: "{{ item }}"
        state: present
      loop:
        - kubeadm
        - kubelet
        - kubectl

    - name: Enable kubelet service
      ansible.builtin.service:
        name: kubelet
        state: started
        enabled: true

    - name: Initialize Kubernetes cluster (control node only)
      ansible.builtin.command:
        cmd: kubeadm init
      when: ansible_hostname == "<control-node-name>"
```
- Run the playbook:
  ```bash
  ansible-playbook install_k8s.yml
  ```

---

### **Conclusion**
This workshop covered practical exercises using Ansible ad-hoc commands, modules, and playbooks to automate various tasks. Participants now have hands-on experience with essential operations like testing connectivity, managing files, installing software, and configuring infrastructure. These exercises provide a solid foundation for mastering Ansible.
