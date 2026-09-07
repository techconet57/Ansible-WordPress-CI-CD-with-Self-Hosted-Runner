# Ansible-WordPress-CI-CD-with-Self-Hosted-Runner

A practical **DevOps CI/CD project** that automates WordPress deployment on AWS EC2 using **Ansible, Docker, Docker Compose, GitHub Actions, and a self-hosted GitHub Actions runner**.

This project demonstrates how infrastructure automation and application deployment can be integrated into a CI/CD pipeline. Ansible is used to remotely configure and deploy WordPress on an AWS EC2 instance, while GitHub Actions provides automated continuous integration and continuous deployment through a self-hosted runner.

---

## 🚀 Project Overview

The project uses two AWS EC2 instances with clearly separated responsibilities:

### EC2-1 — Ansible Control Node / Self-Hosted Runner

**Hostname:** `sam-ansible-runner`

EC2-1 acts as both:

* Ansible Control Node
* GitHub Actions Self-Hosted Runner

It contains:

* Ansible
* Git
* Python
* GitHub Actions Runner
* Ansible inventory
* Ansible playbooks
* Docker Compose templates
* CI/CD workflow files

Ansible running on EC2-1 connects to EC2-2 through SSH and automates the WordPress deployment.

### EC2-2 — WordPress Application Server

**Hostname:** `sam-wordpress`

EC2-2 is the target application server where WordPress is deployed.

It contains:

* Docker
* Docker Compose
* WordPress container
* MySQL container
* Persistent Docker volumes

Ansible remotely configures this server and starts the WordPress application stack.

---

# 🏗️ Architecture

```text
                         ┌─────────────────────────┐
                         │        GitHub            │
                         │                         │
                         │  Source Code Repository │
                         │  GitHub Actions         │
                         └────────────┬────────────┘
                                      │
                                  git push
                                      │
                                      ▼
                    ┌──────────────────────────────────┐
                    │ EC2-1                            │
                    │ sam-ansible-runner               │
                    │                                  │
                    │ GitHub Actions Self-Hosted      │
                    │ Runner                           │
                    │                                  │
                    │ Ansible Control Node             │
                    │                                  │
                    │ ┌──────────────────────────────┐ │
                    │ │ ansible.cfg                  │ │
                    │ │ inventory                    │ │
                    │ │ wordpress.yml                │ │
                    │ │ docker-compose.yml.j2        │ │
                    │ └──────────────────────────────┘ │
                    └───────────────┬──────────────────┘
                                    │
                              SSH / Ansible
                                    │
                                    ▼
                    ┌──────────────────────────────────┐
                    │ EC2-2                            │
                    │ sam-wordpress                    │
                    │                                  │
                    │ Docker                           │
                    │ Docker Compose                   │
                    │                                  │
                    │ ┌──────────────────────────────┐ │
                    │ │ WordPress Container           │ │
                    │ └──────────────┬───────────────┘ │
                    │                │                 │
                    │ ┌──────────────▼───────────────┐ │
                    │ │ MySQL Container               │ │
                    │ └──────────────────────────────┘ │
                    │                                  │
                    │ Persistent Docker Volumes        │
                    └───────────────┬──────────────────┘
                                    │
                                  HTTP :80
                                    │
                                    ▼
                                Web Browser
```

---

# 🔄 CI/CD Workflow

The complete deployment workflow is:

```text
Developer
    │
    │ git add
    │ git commit
    │ git push
    ▼
GitHub Repository
    │
    ▼
GitHub Actions Workflow
    │
    ▼
Self-Hosted Runner
EC2-1
    │
    ▼
Checkout Repository
    │
    ▼
Ansible Connectivity Test
    │
    ▼
Ansible Syntax Check
    │
    ▼
Ansible Playbook
    │
    │ SSH
    ▼
EC2-2
    │
    ▼
Install / Configure Docker
    │
    ▼
Create WordPress Directory
    │
    ▼
Generate Docker Compose File
    │
    ▼
docker compose up -d
    │
    ├── WordPress
    │
    └── MySQL
    │
    ▼
WordPress Application
```

---

# 🛠️ Technologies Used

| Technology         | Purpose                                        |
| ------------------ | ---------------------------------------------- |
| AWS EC2            | Cloud infrastructure                           |
| Amazon Linux 2023  | Operating system                               |
| Ansible            | Server configuration and deployment automation |
| Docker             | Application containerization                   |
| Docker Compose     | Multi-container application deployment         |
| WordPress          | Web application                                |
| MySQL              | WordPress database                             |
| Git                | Version control                                |
| GitHub             | Source code management                         |
| GitHub Actions     | CI/CD automation                               |
| Self-Hosted Runner | Executes GitHub Actions on EC2-1               |
| SSH                | Secure communication between EC2 instances     |

---

# 📁 Project Structure

```text
Ansible-WordPress-CI-CD-with-Self-Hosted-Runner/
│
├── .github/
│   └── workflows/
│       └── deploy.yml
│
├── ansible/
│   ├── ansible.cfg
│   ├── inventory
│   ├── wordpress.yml
│   │
│   └── templates/
│       └── docker-compose.yml.j2
│
├── .gitignore
│
└── README.md
```

---

# 📂 Directory and File Description

## `.github/workflows/deploy.yml`

This file defines the GitHub Actions CI/CD pipeline.

The workflow is triggered when changes are pushed to the `main` branch.

It performs:

1. Repository checkout
2. Ansible version verification
3. Connectivity testing
4. Ansible syntax validation
5. WordPress deployment

Example:

```yaml
name: Deploy WordPress

on:
  push:
    branches:
      - main

  workflow_dispatch:

jobs:

  deploy:
    runs-on: self-hosted

    steps:

      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Check Ansible version
        run: ansible --version

      - name: Test connection to WordPress server
        run: ansible wordpress -m ping -i ansible/inventory

      - name: Validate Ansible playbook
        run: ansible-playbook ansible/wordpress.yml --syntax-check

      - name: Deploy WordPress
        run: ansible-playbook ansible/wordpress.yml
```

---

# ⚙️ Ansible Configuration

## `ansible/ansible.cfg`

The Ansible configuration defines the inventory location, remote user, SSH key, and other Ansible settings.

Example:

```ini
[defaults]
inventory = ./inventory
host_key_checking = False
remote_user = ec2-user
private_key_file = ~/.ssh/wordpress-key.pem
interpreter_python = auto_silent
```

---

# 🖥️ Ansible Inventory

## `ansible/inventory`

The inventory identifies EC2-2 as the WordPress server.

Example:

```ini
[wordpress]
wordpress-server ansible_host=10.0.6.250
```

The architecture uses the private IP address so that Ansible communicates with the target EC2 instance through the AWS VPC network.

---

# 📜 Ansible Playbook

## `ansible/wordpress.yml`

The Ansible playbook automates the complete WordPress server deployment.

The playbook performs tasks such as:

* Installing Docker
* Starting Docker
* Enabling Docker at boot
* Creating the WordPress deployment directory
* Deploying the Docker Compose configuration
* Starting the WordPress containers
* Displaying container status

Example workflow:

```text
Install Docker
      ↓
Start Docker
      ↓
Create /opt/wordpress
      ↓
Deploy docker-compose.yml
      ↓
docker compose up -d
      ↓
Verify containers
```

---

# 🐳 Docker Compose Template

## `ansible/templates/docker-compose.yml.j2`

The Docker Compose template defines the WordPress application stack.

The stack contains:

### WordPress

```text
wordpress:latest
```

### MySQL

```text
mysql:8.0
```

### Persistent Volumes

```text
wordpress_data
db_data
```

The relationship is:

```text
                 WordPress
                     │
                     │ MySQL connection
                     ▼
                   MySQL
                     │
             ┌───────┴────────┐
             ▼                ▼
      wordpress_data       db_data
```

Docker volumes ensure that application and database data are not lost simply because containers are recreated.

---

# 🔐 Security

This project follows several basic security practices.

## SSH Authentication

Ansible connects to EC2-2 using SSH.

```text
EC2-1
  │
  │ SSH key
  ▼
EC2-2
```

## Private IP Communication

Where possible, EC2-1 communicates with EC2-2 using the private VPC address rather than exposing SSH unnecessarily through the public internet.

## `.gitignore`

Private SSH keys and environment files should never be committed to GitHub.

Example:

```gitignore
*.pem
*.key
.env
.env.*
*.retry
__pycache__/
```

> **Important:** Never commit an AWS private key, password, database credential, GitHub token, or other secret to the repository.

For a production implementation, credentials should be managed using appropriate secret-management mechanisms rather than hard-coded values.

---

# ☁️ AWS Infrastructure

The project uses two EC2 instances.

## EC2-1

```text
Name: sam-ansible-runner
Role: Ansible Control Node + GitHub Self-Hosted Runner
```

Responsibilities:

* Run Ansible
* Execute GitHub Actions jobs
* Connect to EC2-2
* Deploy the application

## EC2-2

```text
Name: sam-wordpress
Role: WordPress Application Server
```

Responsibilities:

* Run Docker
* Run Docker Compose
* Host WordPress
* Host MySQL
* Store persistent application/database data

---

# 🔥 Security Group Requirements

## EC2-1

Allow SSH access from the administrator's trusted IP address.

```text
TCP 22
Source: Your trusted IP
```

## EC2-2

Allow SSH from EC2-1.

Prefer using the EC2-1 security group as the source rather than opening SSH to the entire internet.

```text
TCP 22
Source: EC2-1 Security Group
```

For WordPress:

```text
TCP 80
Source: 0.0.0.0/0
```

For HTTPS in a production environment:

```text
TCP 443
Source: 0.0.0.0/0
```

---

# 🚀 Deployment Process

## 1. Clone the repository

```bash
git clone <repository-url>
cd Ansible-WordPress-CI-CD-with-Self-Hosted-Runner
```

## 2. Verify Ansible

```bash
ansible --version
```

## 3. Verify inventory

```bash
cd ansible
ansible-inventory --graph
```

## 4. Test EC2-2 connectivity

```bash
ansible wordpress -m ping
```

Expected:

```text
wordpress-server | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

## 5. Validate the playbook

```bash
ansible-playbook wordpress.yml --syntax-check
```

## 6. Run the deployment

```bash
ansible-playbook wordpress.yml
```

## 7. Verify Docker containers

On EC2-2:

```bash
cd /opt/wordpress
docker compose ps
```

---

# 🔄 Automated CI/CD Deployment

Once the self-hosted runner is configured, deployment can be triggered automatically.

Make a change:

```bash
git add .
git commit -m "Update WordPress deployment"
git push origin main
```

GitHub Actions detects the push and starts the workflow.

The self-hosted runner on EC2-1 executes the deployment:

```text
Git Push
   ↓
GitHub
   ↓
GitHub Actions
   ↓
Self-Hosted Runner
   ↓
Ansible
   ↓
EC2-2
   ↓
Docker Compose
   ↓
WordPress
```

This removes the need to manually SSH into EC2-2 and execute deployment commands every time the configuration changes.

---

# 🧪 Verification

After deployment, verify the following.

### Ansible connectivity

```bash
ansible wordpress -m ping
```

### Docker service

```bash
systemctl status docker
```

### Docker containers

```bash
docker ps
```

### Docker Compose

```bash
docker compose ps
```

### WordPress application

Open:

```text
http://<EC2-2-PUBLIC-IP>
```

The WordPress installation page should be displayed.

---

# 📊 Project Benefits

This project demonstrates several important DevOps concepts:

### Infrastructure Automation

Ansible eliminates repetitive manual server configuration.

### Configuration Management

The desired server configuration is defined as code.

### Containerization

Docker packages WordPress and MySQL into isolated containers.

### Application Orchestration

Docker Compose manages the multi-container WordPress application.

### Continuous Integration

GitHub Actions automatically validates the deployment configuration.

### Continuous Deployment

Successful workflows automatically execute the Ansible deployment.

### Self-Hosted CI/CD Infrastructure

Instead of using only GitHub-hosted runners, the pipeline executes on an AWS EC2 instance managed by the project.

### Infrastructure as Code

Ansible configuration, inventory, playbooks, templates, and workflows are stored in Git.

### Reproducibility

The same Ansible playbook can be used repeatedly to configure the target server.

---

# 🎯 Learning Objectives

By completing this project, you gain practical experience with:

* AWS EC2
* Linux server administration
* SSH
* Git and GitHub
* Ansible
* Ansible inventory
* Ansible playbooks
* Jinja2 templates
* Docker
* Docker Compose
* WordPress containerization
* MySQL containers
* Docker volumes
* GitHub Actions
* Self-hosted GitHub Actions runners
* CI/CD pipelines
* Remote server automation
* Infrastructure as Code

---

# 🔮 Future Improvements

This project can be extended into a more production-ready architecture.

Possible improvements include:

* HTTPS with SSL/TLS
* Nginx reverse proxy
* Domain name configuration
* AWS Route 53
* AWS Application Load Balancer
* Ansible Vault for secrets
* AWS Secrets Manager
* AWS Systems Manager
* Docker image version pinning
* Automated backup of WordPress data
* MySQL backup automation
* Monitoring and logging
* Prometheus and Grafana
* CloudWatch monitoring
* Separate staging and production environments
* GitHub Actions approval gates
* Rolling deployments
* Automated rollback
* Infrastructure provisioning with Terraform

---

# 📌 Project Summary

This project demonstrates a complete automated deployment pipeline for WordPress using modern DevOps tools.

The solution separates the **CI/CD control layer** from the **application layer**:

```text
EC2-1
Ansible + GitHub Actions Runner
          │
          │
          ▼
EC2-2
Docker + WordPress + MySQL
```

A Git push triggers GitHub Actions, the self-hosted runner executes the Ansible playbook, and Ansible remotely configures EC2-2 and deploys the containerized WordPress application.

The result is a repeatable and automated CI/CD workflow that combines **AWS, Ansible, Docker, GitHub Actions, and self-hosted infrastructure**.

---

# 👨‍💻 Author

**Shyam Raut**

This project was created as a practical DevOps learning project to demonstrate automated WordPress deployment using Ansible, Docker, AWS EC2, and GitHub Actions.

---

# ⭐ If You Find This Project Useful

Feel free to explore the repository, experiment with the automation, and extend the architecture with additional DevOps and cloud technologies.

