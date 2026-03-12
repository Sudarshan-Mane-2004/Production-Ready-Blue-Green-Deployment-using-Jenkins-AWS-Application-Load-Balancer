# 🚀 Production-Ready Blue-Green Deployment using Jenkins & AWS Application Load Balancer

## 📌 Project Overview

This project demonstrates a **production-ready Blue-Green Deployment strategy** using **Jenkins CI/CD pipeline and AWS Application Load Balancer (ALB)**.

Traditional deployments often cause **downtime**, because updates are deployed directly to the production server.
This project solves that problem by introducing **two identical environments**:

* 🔵 **Blue Environment** – current production
* 🟢 **Green Environment** – new deployment

Jenkins automatically deploys updates to the **inactive environment**, performs a **health check**, and switches traffic through the **Application Load Balancer** only if the deployment is successful.

This ensures **zero-downtime deployments**.

---

# 🎯 Aim of the Project

The aim of this project is to **implement a zero-downtime deployment pipeline** using:

* Jenkins (CI/CD automation)
* GitHub (source control)
* AWS EC2 (application servers)
* AWS Application Load Balancer (traffic routing)

The pipeline automatically:

1. Pulls code from GitHub
2. Deploys it to the **inactive environment**
3. Performs **health checks**
4. Switches ALB traffic
5. Ensures **continuous availability of the application**

---

# 🧰 Technologies & Tools Used

| Tool          | Purpose                   |
| ------------- | ------------------------- |
| AWS EC2       | Application servers       |
| Jenkins       | CI/CD automation          |
| GitHub        | Source code repository    |
| AWS ALB       | Traffic switching         |
| Target Groups | Blue & Green environments |
| Nginx         | Web server                |
| AWS CLI       | ALB traffic control       |
| SSH / SCP     | Remote deployment         |

---

# 🏗️ Architecture

```
Developer
   │
   ▼
GitHub Repository
   │
   ▼
Jenkins Pipeline
   │
   ▼
Deploy to Green Environment
   │
   ▼
Health Check
   │
   ▼
Application Load Balancer
   │
   ▼
User Traffic
```

---

# ⚙️ AWS Services Used and Purpose

## 1️⃣ EC2 Instances

Three EC2 instances were created:

| Instance       | Purpose                        |
| -------------- | ------------------------------ |
| Jenkins Server | CI/CD pipeline execution       |
| Blue Server    | Current production environment |
| Green Server   | New deployment environment     |

Purpose:

* Host application environments
* Allow Jenkins to deploy new versions

---

## 2️⃣ Application Load Balancer

Purpose:

* Route traffic between environments
* Ensure **zero downtime**
* Switch between Blue and Green environments

Traffic example:

```
Before Deployment

User → ALB → blue-tg → Blue Server

After Deployment

User → ALB → green-tg → Green Server
```

---

## 3️⃣ Target Groups

Two target groups were created:

| Target Group | Environment    |
| ------------ | -------------- |
| blue-tg      | Production     |
| green-tg     | New deployment |

Purpose:

* Separate application environments
* Allow ALB to switch traffic safely

---

# 🔄 Deployment Flow

### Step 1 – Developer Push

Developer pushes code to GitHub.

```
git add .
git commit -m "update"
git push origin main
```

---

### Step 2 – Jenkins Pipeline Trigger

Jenkins pulls latest code.

```
git clone https://github.com/username/repo.git
```

---

### Step 3 – Deploy to Green Server

Jenkins deploys the application using SCP.

```
scp -i key.pem index.html ubuntu@GREEN_SERVER_IP:/var/www/html/index.html
```

---

### Step 4 – Health Check

Jenkins verifies application availability.

```
curl http://GREEN_SERVER_IP
```

Expected response:

```
HTTP 200 OK
```

---

### Step 5 – Switch Traffic

ALB traffic is switched using AWS CLI.

```
aws elbv2 modify-listener \
--listener-arn LISTENER_ARN \
--default-actions Type=forward,TargetGroupArn=GREEN_TG_ARN
```

---

# 🧪 How to Test the Project

### 1️⃣ Open Load Balancer DNS

```
http://ALB-DNS
```

Initially shows:

```
Blue Environment
```

---

### 2️⃣ Push New Code

Modify `index.html`:

```
<h1>Version 2 Deployment</h1>
```

Push to GitHub.

---

### 3️⃣ Run Jenkins Pipeline

```
Build Now
```

---

### 4️⃣ Verify Traffic Switch

Refresh ALB DNS.

You should see:

```
Version 2 Deployment
```

This confirms **traffic switched to Green environment**.

---

# 🧪 Commands Used During Development

## Install Jenkins

```
sudo apt update
sudo apt install openjdk-17-jdk -y
sudo apt install jenkins -y
```

---

## Start Jenkins

```
sudo systemctl start jenkins
sudo systemctl enable jenkins
```

---

## Install Nginx

```
sudo apt install nginx -y
sudo systemctl start nginx
```

---

## AWS CLI Installation

```
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o awscliv2.zip
unzip awscliv2.zip
sudo ./aws/install
```

---

## Configure AWS CLI

```
aws configure
```

---

## SSH Key Setup

```
chmod 400 key.pem
```

---

## Test SSH Connection

```
ssh -i key.pem ubuntu@SERVER_IP
```

---

# 🛠️ Problems Faced During Development

### ❌ Jenkins cannot clone GitHub repo

Error:

```
Couldn't find any revision to build
```

Solution:

Specify branch in pipeline.

```
git branch: 'main'
```

---

### ❌ Permission denied (publickey)

Cause:

SSH key not configured.

Solution:

Copy `.pem` key to Jenkins server.

```
scp key.pem ubuntu@jenkins:/home/ubuntu
```

---

### ❌ Permission denied writing to `/var/www/html`

Cause:

Folder owned by root.

Solution:

```
sudo chown -R ubuntu:ubuntu /var/www/html
```

---

### ❌ AWS CLI not found

Error:

```
aws: not found
```

Solution:

Install AWS CLI manually.

---

### ❌ Unable to locate credentials

Cause:

AWS credentials not configured for Jenkins user.

Solution:

```
sudo su - jenkins
aws configure
```

---

# 📊 Project Outcome

The project successfully demonstrates:

✔ Automated CI/CD pipeline
✔ Zero downtime deployments
✔ Safe environment switching
✔ Health-check based deployment
✔ Production-style infrastructure

---

# 📚 Learning Outcomes

This project helped in understanding:

* CI/CD pipelines
* Infrastructure automation
* AWS Load Balancing
* DevOps deployment strategies
* Blue-Green deployment models

---

# 🤝 Contribution Guide

1. Fork the repository
2. Create a feature branch

```
git checkout -b feature-name
```

3. Commit your changes

```
git commit -m "feature added"
```

4. Push to GitHub

```
git push origin feature-name
```

5. Create a Pull Request

---

# 📌 Conclusion

In this project, a **Blue-Green deployment pipeline was successfully implemented using Jenkins and AWS Application Load Balancer**.

The CI/CD pipeline automatically deploys new application versions to the inactive environment, verifies the deployment using health checks, and switches traffic without interrupting user access.

This approach eliminates downtime, improves deployment reliability, and demonstrates real-world **DevOps practices used in production environments**.

---

# ⭐ If you found this project helpful

Give this repository a ⭐ and feel free to contribute!
