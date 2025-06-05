Here's the **fully corrected and properly styled Markdown** version of your project setup. I've fixed the styling issues, formatting, and ensured consistent code blocks:

```
```markdown
# 🚀 Jenkins + Docker CI/CD with GitHub (Project Setup)

This project demonstrates a **full CI/CD pipeline** using **Jenkins**, **Docker**, and **GitHub** on **AWS EC2 Instances**, including **multi-node (Master-Slave) configuration**, **Docker deployment**, and **webhooks**.

```
## 📁 Project Structure


project-root/
├── Dockerfile
├── index.html
└── images/
    ├── 1.jpg
    └── 2.jpg
```

```
## 🐳 Dockerfile

```dockerfile
FROM hshar/webapp
ADD ./devopsIQ /var/www/html/devopsIQ
```

---

## 🌐 index.html

```html
<html>
  <title>Jenkins Final Website</title>
  <body background="images/1.jpg">
  </body>
</html>
```

---

## ☁️ Infrastructure Setup

### 🔧 EC2 Instance Creation

- Create **3 EC2 Ubuntu instances**:
  - 1 **Master**
  - 2 **Slaves**
- ⚠️ **Add Inbound Rule:** Allow **all traffic (0.0.0.0/0)** for all instances.

---

## 💡 Jenkins + Java Installation (Master Node)

### 1. Update System

```bash
sudo apt update && sudo apt upgrade -y
```

### 2. Install Java (Latest or Recommended)

```bash
sudo apt install openjdk-21-jre-headless -y
java -version
sudo update-alternatives --config java
```

### 3. Add Jenkins Repository (Secure Method)

```bash
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null

echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
```

### 4. Install Jenkins

```bash
sudo apt update
sudo apt install jenkins -y
```

### 5. Start & Enable Jenkins

```bash
sudo systemctl start jenkins
sudo systemctl enable jenkins
```

### 6. Allow Port (if UFW is enabled)

```bash
sudo ufw allow 8080
sudo ufw status
```

### 7. Access Jenkins UI

Visit:
```
http://<YOUR_MASTER_PUBLIC_IP>:8080
```

Get initial password:
```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

---

## ⚙️ Agent (Slave Node) Configuration

### On Each Slave

1. Install Java:

```bash
sudo apt update
sudo apt install openjdk-21-jre-headless -y
```

2. Download & Connect Agent:

Go to:
**Jenkins > Manage Jenkins > Nodes > NodeName > Launch agent**

Run:
```bash
curl -sO http://<master-ip>:8080/jnlpJars/agent.jar
java -jar agent.jar -url http://<master-ip>:8080/ -secret <secret> -name slaveX -webSocket -workDir "/home/ubuntu/jenkins"
```

➡️ **Keep this terminal open.**

3. In a new terminal, install Docker:

```bash
sudo apt install docker.io -y
```

---

## 🛠 Jenkins Job Setup (Freestyle)

### Job 1 (on `slave1`)

- Source: GitHub repo
- Branch: `*/main`
- Shell Build Step:

```bash
sudo docker rm -f $(sudo docker ps -a -q)
sudo docker build /home/ubuntu/jenkins/workspace/Test -t test
sudo docker run -it -p 82:80 -d test
```

- Trigger: ✅ GitHub hook

---

### Job 2 (on `slave2`)

- Same as Job 1, but:
  - Port: `80:80`
  - No trigger

---

## 🔄 Pipeline Setup

- **Post-build action** in Job 1: Trigger Job 2
- Create view: **Build Pipeline View**
  - Initial Job: `Job 1`
- Plugin: **Build Pipeline Plugin**

---

## 🔗 GitHub Webhook Setup

- Go to GitHub Repo → Settings → Webhooks
- Add new webhook:
  - Payload URL: `http://<master-ip>:8080/github-webhook/`
  - Content type: `application/json`
  - Trigger: Push events

---

## 🔁 CI/CD Flow Summary

1. Developer pushes code to GitHub
2. Jenkins gets triggered via webhook
3. Job 1 builds & deploys on Slave 1 (port 82)
4. Job 2 builds & deploys on Slave 2 (port 80)
5. Pipeline visualized in Jenkins dashboard

---

## ✅ Output Preview

- `http://<slave1-ip>:82` → Output from Job 1
- `http://<slave2-ip>:80` → Output from Job 2

---

## 🧠 Summary

- ✅ Multi-node Jenkins setup (Master + Slaves)
- ✅ GitHub-integrated CI/CD with Docker
- ✅ Live deployment on EC2 using webhooks & jobs

