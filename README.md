# Ansible WordPress CI/CD with Self-Hosted Runner

A practical **DevOps CI/CD project** that automates WordPress deployment on **AWS EC2** using **Ansible, Docker, Docker Compose, GitHub Actions, and a self-hosted GitHub Actions runner**.

## 📌 Project Overview

This project demonstrates how to build an automated WordPress deployment pipeline using Ansible and GitHub Actions.

The application is deployed on an **AWS EC2 instance** using Docker containers. Ansible is used for server configuration and application deployment, while GitHub Actions automates the CI/CD workflow through a **self-hosted runner** installed on the EC2 server.

Whenever changes are pushed to the GitHub repository, the GitHub Actions workflow can automatically execute the deployment process.

## 🛠️ Technologies Used

* **AWS EC2** – Cloud server
* **Ansible** – Configuration management and deployment automation
* **Docker** – Containerization
* **Docker Compose** – Multi-container application management
* **GitHub Actions** – CI/CD automation
* **Self-Hosted Runner** – Executes GitHub Actions directly on the EC2 server
* **WordPress** – Application
* **MySQL** – Database

## 🔄 CI/CD Workflow

```text
Developer
    │
    │ git push
    ▼
GitHub Repository
    │
    ▼
GitHub Actions
    │
    ▼
Self-Hosted Runner
    │
    ▼
Ansible
    │
    ├── Configure Server
    ├── Install/Configure Docker
    ├── Deploy WordPress
    └── Start Containers
    │
    ▼
Docker Compose
    │
    ├── WordPress Container
    └── MySQL Container
    │
    ▼
Running WordPress Application
```

## 🎯 Project Objectives

* Automate WordPress deployment using Ansible
* Use Docker for application containerization
* Manage WordPress and MySQL using Docker Compose
* Implement CI/CD using GitHub Actions
* Configure and use a self-hosted GitHub Actions runner
* Deploy and manage the application on AWS EC2
* Reduce manual deployment steps

## 📂 Project Structure

```text
Ansible-WordPress-CI-CD-with-Self-Hosted-Runner/
│
├── .github/
│   └── workflows/
│       └── deploy.yml
│
├── ansible/
│   ├── inventory
│   ├── wordpress.yml
│   └── ...
│
├── docker-compose.yml
├── README.md
└── ...
```

## 🚀 Deployment Process

1. Create and configure an **AWS EC2 instance**.
2. Install and configure Docker on the server.
3. Configure the **GitHub Actions self-hosted runner**.
4. Configure the Ansible inventory and playbook.
5. Push project changes to GitHub.
6. GitHub Actions triggers the deployment workflow.
7. The self-hosted runner executes the workflow on EC2.
8. Ansible configures the server and deploys the application.
9. Docker Compose starts the WordPress and MySQL containers.
10. WordPress becomes available through the EC2 instance.

## 🔑 Key Features

* Automated deployment
* Infrastructure configuration using Ansible
* Containerized WordPress application
* Docker Compose-based service management
* GitHub Actions CI/CD pipeline
* Self-hosted GitHub Actions runner
* AWS EC2 deployment
* Repeatable deployment process

## 📚 What This Project Demonstrates

This project provides hands-on experience with important DevOps concepts including:

* **Configuration Management**
* **Infrastructure Automation**
* **Containerization**
* **CI/CD**
* **Cloud Deployment**
* **GitHub Actions**
* **Self-Hosted Runners**
* **Ansible Playbooks**
* **Docker Compose**

## 👨‍💻 Author

**Shyam Raut**

---

⭐ If you find this project useful, consider giving the repository a star.
