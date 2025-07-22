Here is a complete **Terraform manifest** to create **4 EC2 instances** on **AWS** (one Ansible control node + three target servers: webserver, appserver, dbserver). The control node (host server) will use a pre-generated SSH key stored at `/tmp/classkey.pem`.

---

## 📁 File: `main.tf`

```hcl
provider "aws" {
  region = "us-west-2"
}

# Read in your local public key for use in AWS key pair
resource "aws_key_pair" "classkey" {
  key_name   = "classkey"
  public_key = file("~/.ssh/classkey.pub") # Adjust if your public key is in a different path
}

# Create a security group allowing SSH
resource "aws_security_group" "allow_ssh" {
  name        = "allow_ssh"
  description = "Allow SSH inbound traffic"

  ingress {
    description = "SSH"
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"] # For demonstration only. Limit in production.
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}

# Common EC2 configuration
locals {
  instance_type = "t2.micro"
  ami_id        = "ami-0c55b159cbfafe1f0" # Amazon Linux 2 AMI (update based on your region)
}

# Create the Ansible control (host) server
resource "aws_instance" "host" {
  ami                         = local.ami_id
  instance_type               = local.instance_type
  key_name                    = aws_key_pair.classkey.key_name
  vpc_security_group_ids      = [aws_security_group.allow_ssh.id]
  associate_public_ip_address = true

  tags = {
    Name = "ansible-host"
  }
}

# Create webserver
resource "aws_instance" "webserver" {
  ami                         = local.ami_id
  instance_type               = local.instance_type
  key_name                    = aws_key_pair.classkey.key_name
  vpc_security_group_ids      = [aws_security_group.allow_ssh.id]
  associate_public_ip_address = true

  tags = {
    Name = "webserver"
  }
}

# Create appserver
resource "aws_instance" "appserver" {
  ami                         = local.ami_id
  instance_type               = local.instance_type
  key_name                    = aws_key_pair.classkey.key_name
  vpc_security_group_ids      = [aws_security_group.allow_ssh.id]
  associate_public_ip_address = true

  tags = {
    Name = "appserver"
  }
}

# Create dbserver
resource "aws_instance" "dbserver" {
  ami                         = local.ami_id
  instance_type               = local.instance_type
  key_name                    = aws_key_pair.classkey.key_name
  vpc_security_group_ids      = [aws_security_group.allow_ssh.id]
  associate_public_ip_address = true

  tags = {
    Name = "dbserver"
  }
}

output "host_ip" {
  value = aws_instance.host.public_ip
}

output "webserver_ip" {
  value = aws_instance.webserver.public_ip
}

output "appserver_ip" {
  value = aws_instance.appserver.public_ip
}

output "dbserver_ip" {
  value = aws_instance.dbserver.public_ip
}
```

---

## 🔧 Before You Apply

1. Place your **public key** at `~/.ssh/classkey.pub`.
2. Run:

```bash
terraform init
terraform apply
```

---

## 📝 After Provisioning

Use the IPs in your Ansible inventory file like:

```ini
[webserver]
<webserver_ip> ansible_user=ec2-user ansible_ssh_private_key_file=/tmp/classkey.pem

[appserver]
<appserver_ip> ansible_user=ec2-user ansible_ssh_private_key_file=/tmp/classkey.pem

[dbserver]
<dbserver_ip> ansible_user=ec2-user ansible_ssh_private_key_file=/tmp/classkey.pem
```


