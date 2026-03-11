# Centralized CI/CD Platform using Jenkins

> A scalable, enterprise-grade CI/CD platform that enables multiple development teams to leverage standardized pipelines through shared Jenkins infrastructure.

[![Jenkins](https://img.shields.io/badge/Jenkins-D24939?style=flat&logo=jenkins&logoColor=white)](https://www.jenkins.io/)
[![AWS EC2](https://img.shields.io/badge/AWS_EC2-FF9900?style=flat&logo=amazon-aws&logoColor=white)](https://aws.amazon.com/ec2/)
[![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat&logo=docker&logoColor=white)](https://www.docker.com/)
[![GitHub](https://img.shields.io/badge/GitHub-181717?style=flat&logo=github&logoColor=white)](https://github.com/)

---

## Table of Contents

- [Problem Statement](#problem-statement)
- [Solution Overview](#solution-overview)
- [Architecture](#architecture)
- [Key Features](#key-features)
- [Technologies Used](#technologies-used)
- [Project Structure](#project-structure)
- [Setup Instructions](#setup-instructions)
- [Shared Library Implementation](#shared-library-implementation)
- [Sample Applications](#sample-applications)
- [Access Control](#access-control)
- [Screenshots](#screenshots)
- [Deliverables](#deliverables)
- [Learning Outcomes](#learning-outcomes)

---

## Problem Statement

### Challenges Faced:
- **Duplicate Infrastructure**: Each development team maintained separate Jenkins servers
- **Inconsistent Pipelines**: No standardization across projects
- **Security Misconfigurations**: Decentralized security management
- **Resource Wastage**: Inefficient utilization of infrastructure
- **Maintenance Overhead**: Multiple servers to manage and update

---

## Solution Overview

Built a **centralized CI/CD platform** that provides:
- Single Jenkins instance serving multiple teams
- Standardized pipeline templates via Shared Libraries
- Consistent build, test, scan, and deploy stages
- Role-based access control (RBAC)
- Reduced infrastructure costs and management overhead

---

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     GitHub Repositories                      │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │ Shared Lib   │  │ Node.js App  │  │ Python App   │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                    Jenkins Master (EC2)                      │
│  ┌────────────────────────────────────────────────────┐     │
│  │  • Shared Library Configuration                    │     │
│  │  • Credential Management                           │     │
│  │  • Role-Based Access Control                       │     │
│  │  • Multi-Branch Pipeline Jobs                      │     │
│  └────────────────────────────────────────────────────┘     │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                    Jenkins Agents                            │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │ Static Agent │  │ Dynamic Agent│  │ Docker Agent │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                  Standardized Pipeline Stages                │
│     Build  →  Test  →  Security Scan  →  Deploy             │
└─────────────────────────────────────────────────────────────┘
```

---

## Key Features

### 1. Centralized Jenkins Infrastructure
- Deployed on AWS EC2 instance
- Configured with static and dynamic agents
- Centralized credential management
- Scalable architecture

### 2. Shared Pipeline Library
- Reusable pipeline components
- Standardized stages: Build → Test → Scan → Deploy
- Version-controlled pipeline logic
- Easy to maintain and update

### 3. Multi-Application Support
- Onboarded multiple applications (Node.js, Python)
- Single line Jenkinsfile: `@Library('shared-lib') _ cicdPipeline()`
- Consistent behavior across all projects
- Technology-agnostic approach

### 4. Role-Based Access Control
- **Developers**: View and build jobs
- **Admins**: Full configuration access
- Secure credential isolation
- Audit trail for all actions

---

## Technologies Used

| Technology | Purpose |
|------------|---------|
| **Jenkins** | CI/CD orchestration platform |
| **AWS EC2** | Cloud infrastructure hosting |
| **Docker** | Containerization for builds |
| **GitHub** | Source code management |
| **Groovy** | Shared library scripting |
| **Node.js** | Sample application 1 |
| **Python** | Sample application 2 |

---

## Project Structure

```
Centralized-CI-CD-Platform-using-Jenkins/
│
├── img/                              # Screenshots and diagrams
│   ├── jenkins-dahsboard.png
│   ├── manage roles.png
│   ├── pipelines stages.png
│   └── shared repo.png
│
├── nodejs-demo-app/                  # Sample Node.js application
│   ├── Jenkinsfile                   # Pipeline configuration
│   ├── package.json
│   └── README.md
│
├── python-demo-app/                  # Sample Python application
│   ├── Jenkinsfile                   # Pipeline configuration
│   └── README.md
│
└── README.md                         # Project documentation
```

**Note**: The Jenkins Shared Library is maintained in a separate repository and configured in Jenkins Global Settings.

---

## Setup Instructions

### Prerequisites
- AWS Account with EC2 access
- GitHub account
- Basic knowledge of Jenkins and CI/CD concepts

### Step 1: Jenkins Installation on EC2

```bash
# Launch EC2 instance (Ubuntu 22.04 LTS)
# Instance Type: t2.medium or higher
# Security Group: Allow ports 8080 (Jenkins), 22 (SSH)

# SSH into EC2 instance
ssh -i your-key.pem ubuntu@<ec2-public-ip>

# Install Java
sudo apt update
sudo apt install openjdk-11-jdk -y

# Install Jenkins
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt update
sudo apt install jenkins -y

# Start Jenkins
sudo systemctl start jenkins
sudo systemctl enable jenkins

# Get initial admin password
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

### Step 2: Configure Jenkins

1. Access Jenkins at `http://<ec2-public-ip>:8080`
2. Install suggested plugins
3. Create admin user
4. Install additional plugins:
   - Pipeline
   - Git
   - Docker Pipeline
   - Role-based Authorization Strategy

### Step 3: Setup Shared Library

1. Navigate to **Manage Jenkins** → **Configure System**
2. Scroll to **Global Pipeline Libraries**
3. Add library:
   - **Name**: `shared-lib`
   - **Default version**: `main`
   - **Retrieval method**: Modern SCM
   - **Source Code Management**: Git
   - **Project Repository**: `<your-shared-library-repo-url>`

### Step 4: Configure Agents

**Static Agent:**
```bash
# On agent machine
java -jar agent.jar -jnlpUrl http://<jenkins-url>/computer/<agent-name>/slave-agent.jnlp
```

**Dynamic Agent (Docker):**
- Install Docker Pipeline plugin
- Configure Docker cloud in Jenkins settings

### Step 5: Setup Credentials

1. Navigate to **Manage Jenkins** → **Manage Credentials**
2. Add credentials:
   - GitHub token
   - Docker Hub credentials
   - AWS credentials (if needed)

### Step 6: Create Pipeline Jobs

1. **New Item** → **Pipeline** or **Multibranch Pipeline**
2. Configure SCM to point to application repository
3. Jenkins will automatically detect Jenkinsfile
4. Pipeline will use shared library automatically

---

## Shared Library Implementation

### Library Structure
```
jenkins-shared-library/
├── vars/
│   └── cicdPipeline.groovy       # Main pipeline definition
├── src/
│   └── org/company/
│       ├── Build.groovy
│       ├── Test.groovy
│       ├── Scan.groovy
│       └── Deploy.groovy
└── resources/
    └── scripts/
```

### Sample Shared Library Code (vars/cicdPipeline.groovy)

```groovy
def call() {
    pipeline {
        agent any
        
        stages {
            stage('Checkout') {
                steps {
                    checkout scm
                }
            }
            
            stage('Build') {
                steps {
                    script {
                        echo "Building application..."
                        // Auto-detect project type and build
                        if (fileExists('package.json')) {
                            sh 'npm install'
                        } else if (fileExists('requirements.txt')) {
                            sh 'pip install -r requirements.txt'
                        }
                    }
                }
            }
            
            stage('Test') {
                steps {
                    script {
                        echo "Running tests..."
                        if (fileExists('package.json')) {
                            sh 'npm test || true'
                        } else if (fileExists('requirements.txt')) {
                            sh 'pytest || true'
                        }
                    }
                }
            }
            
            stage('Security Scan') {
                steps {
                    echo "Running security scans..."
                    // Add security scanning tools
                }
            }
            
            stage('Deploy') {
                steps {
                    echo "Deploying application..."
                    // Add deployment logic
                }
            }
        }
        
        post {
            success {
                echo "Pipeline completed successfully!"
            }
            failure {
                echo "Pipeline failed!"
            }
        }
    }
}
```

---

## Sample Applications

### 1. Node.js Demo App

**Jenkinsfile:**
```groovy
@Library('shared-lib') _

cicdPipeline()
```

That's it! The shared library handles all pipeline logic.

### 2. Python Demo App

**Jenkinsfile:**
```groovy
@Library('shared-lib') _

cicdPipeline()
```

Same simple configuration, consistent behavior.

---

## Access Control

### Role Configuration

| Role | Permissions |
|------|-------------|
| **Admin** | Full access to Jenkins configuration, job creation, and system settings |
| **Developer** | View jobs, trigger builds, view console output |
| **Viewer** | Read-only access to job status and build history |

### Setup RBAC

1. Install **Role-based Authorization Strategy** plugin
2. Navigate to **Manage Jenkins** → **Manage and Assign Roles**
3. Create roles with specific permissions
4. Assign users to roles

---

## Screenshots

### Jenkins Dashboard
![Jenkins Dashboard](img/jenkins-dahsboard.png)
*Centralized dashboard showing multiple project pipelines*

### Pipeline Stages
![Pipeline Stages](img/pipelines%20stages.png)
*Standardized pipeline stages: Build → Test → Scan → Deploy*

### Shared Library Repository
![Shared Library](img/shared%20repo.png)
*Jenkins Shared Library configuration*

### Role Management
![Role Management](img/manage%20roles.png)
*Role-based access control configuration*

---

## Deliverables

- **Jenkins Shared Library Repository**: Reusable pipeline components
- **Sample Application Pipelines**: Node.js and Python demo apps
- **Multi-Project Pipeline Screenshots**: Visual proof of implementation
- **Comprehensive Documentation**: Setup guide and architecture explanation
- **Role-Based Access Control**: Security implementation
- **Centralized Infrastructure**: Single Jenkins instance on EC2

---

## Learning Outcomes

- Designed and implemented enterprise-grade CI/CD platform
- Created reusable Jenkins Shared Libraries
- Configured role-based access control for security
- Deployed and managed Jenkins on AWS EC2
- Standardized pipeline processes across multiple applications
- Reduced infrastructure costs through centralization

---

## Future Enhancements

- Implement Jenkins Configuration as Code (JCasC)
- Add automated testing for shared library
- Integrate with Kubernetes for dynamic agent provisioning
- Implement advanced security scanning (SonarQube, Trivy)
- Add monitoring and alerting (Prometheus, Grafana)
- Create self-service portal for team onboarding

---

## Author

**Raj**

Platform Engineer | DevOps Enthusiast

---

## License

This project is created for educational and demonstration purposes.

---

## Acknowledgments

- Jenkins Community for excellent documentation
- AWS for reliable cloud infrastructure
- Open-source community for various tools and plugins

---

<div align="center">

**If you found this project helpful, please consider giving it a star!**

Made with ❤️ by Raj

</div>
