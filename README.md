Here's a complete `README.md` file for your Jenkins + Docker + GitHub CI/CD project, formatted clearly for documentation or GitHub usage:

---

```markdown
# 🚀 Jenkins + Docker CI/CD with GitHub (Project Setup)

This project demonstrates a **full CI/CD pipeline** using **Jenkins**, **Docker**, and **GitHub** on **AWS EC2 Instances**, including **multi-node (Master-Slaves) configuration**, **Docker deployment**, and **webhooks**.

---

## 📁 Project Structure

```

project-root/
│
├── Dockerfile
├── index.html
├── images/
│   ├── 1.jpg
│   └── 2.jpg

````

**Dockerfile:**

```dockerfile
FROM hshar/webapp
ADD ./devopsIQ /var/www/html/devopsIQ
````

**index.html:**

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

* Create **3 EC2 Ubuntu instances**:

  * 1 **Master**
  * 2 **Slaves**
* ⚠️ **Add Inbound Rule:** Allow **all traffic (0.0.0.0/0)** for all instances.

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

```text
http://<YOUR_MASTER_PUBLIC_IP>:8080
```

Get Jenkins password:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

---

## ⚙️ Agent (Slave Node) Configuration

### On Slaves:

1. Install Java:

```bash
sudo apt update
sudo apt install openjdk-21-jre-headless -y
```

2. Download & Connect Agent:

Get the commands from:

**Jenkins > Manage Jenkins > Nodes > NodeName > Launch agent**

Example:

```bash
curl -sO http://<master-ip>:8080/jnlpJars/agent.jar
java -jar agent.jar -url http://<master-ip>:8080/ -secret <secret> -name slaveX -webSocket -workDir "/home/ubuntu/jenkins"
```

⛔ **Do NOT close this terminal — keep it running.**

3. In a second terminal, install Docker:

```bash
sudo apt install docker.io -y
```

---

## 🛠 Jenkins Job Setup (Freestyle)

### Job 1 (on `slave1`)

* Source Code: GitHub Repo
* Branch: `*/main`
* Shell:

```bash
sudo docker rm -f $(sudo docker ps -a -q)
sudo docker build /home/ubuntu/jenkins/workspace/Test -t test
sudo docker run -it -p 82:80 -d test
```

* Add GitHub webhook trigger: ✅

---

### Job 2 (on `slave2`)

* Same as Job 1, but:

  * Use different port: `80:80`
  * No webhook trigger

---

### Trigger & Pipeline

* Add **Post Build Action** in Job 1 → trigger Job 2.
* Create a new Jenkins view: **Pipeline View**

  * Initial Job: `job1`
* Install **Build Pipeline Plugin** from **Manage Jenkins > Plugins**

---

## 🌐 GitHub Webhook Setup

* Go to your GitHub Repo > Settings > Webhooks
* Payload URL: `http://<master-ip>:8080/github-webhook/`
* Content type: `application/json`
* Events: **Just the push event**
* Click **Add webhook**

---

## 🔁 End-to-End CI/CD Flow

1. Make changes in local repo.
2. Push to GitHub.
3. Jenkins webhook triggers `job1`.
4. `job1` builds & deploys Docker container on `slave1`.
5. After success, `job2` is triggered.
6. `job2` builds & deploys container on `slave2`.

---

## ✅ Output Preview

* `http://<slave1-ip>:82` → Deployed site from Job 1
* `http://<slave2-ip>:80` → Deployed site from Job 2

---

## 👨‍💻 Notes

* Make sure **Docker** is running on both slave instances.
* Keep agent terminals open (or configure them as services).
* Allow public access to port **8080**, **80**, and **82** for external visibility.
* Make sure the GitHub repo has:

  * `Dockerfile`
  * `index.html`
  * `images/` folder with `1.jpg`, `2.jpg`

---

## 🧠 Summary

* ✅ Multi-node Jenkins setup with master-slave architecture.
* ✅ Real Docker deployment with GitHub CI/CD integration.
* ✅ Webhook-triggered build pipeline with Docker containers.

---

*This project is created and maintained by **Hurry** as a complete CI/CD demonstration using Jenkins, Docker, and GitHub. All rights reserved.*

```

Let me know if you want a `.sh` setup script or want me to turn this into a PDF or HTML for portfolio use.
```
