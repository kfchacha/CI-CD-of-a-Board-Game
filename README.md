# 🎲 BoardGame Database — End-to-End CI/CD Pipeline

> A full production-grade DevOps pipeline for a Java Spring Boot board game web application, built on AWS with Jenkins, Docker, Kubernetes, SonarQube, Nexus, and more.

![Pipeline Status](https://img.shields.io/badge/Pipeline-Passing-brightgreen?style=for-the-badge&logo=jenkins)
![Docker](https://img.shields.io/badge/Docker-Containerized-2496ED?style=for-the-badge&logo=docker)
![Kubernetes](https://img.shields.io/badge/Kubernetes-Deployed-326CE5?style=for-the-badge&logo=kubernetes)
![AWS](https://img.shields.io/badge/AWS-EC2-FF9900?style=for-the-badge&logo=amazonaws)
![SonarQube](https://img.shields.io/badge/SonarQube-Analyzed-4E9BCD?style=for-the-badge&logo=sonarqube)

---

## 📐 Pipeline Architecture

The full workflow — from a developer pushing code to a live deployed application, with security scanning, quality gates, artifact management, and automated notifications at every stage.

![Pipeline Architecture](./Project%20Screenshots/pipeline-overview.png)

---

## ✅ Jenkins — Build #22 (All Stages Green)

Every stage completed successfully in **1 minute 15 seconds**, from tool install through Kubernetes deployment and email notification.

![Jenkins Build Stages](./screenshots/jenkins-stages-build22.png)

### Multi-Build Stage View

Comparing Build #22 (success) vs Build #21 (failed) — you can see the improvement across all stages.

![Jenkins Multi-Pipeline Graph](./screenshots/jenkins-multi-pipeline.png)

---

## 🏗️ Architecture & Tech Stack

| Layer | Tool | Purpose |
|---|---|---|
| **Source Control** | GitHub | Code hosting & webhook triggers |
| **CI/CD Orchestration** | Jenkins | Pipeline automation (Declarative Pipeline) |
| **Build Tool** | Maven | Compile, test & package Java app |
| **Code Quality** | SonarQube 9.9 | Static analysis, quality gate enforcement |
| **Vulnerability Scan** | Trivy (Aqua) | Filesystem & Docker image CVE scanning |
| **Artifact Registry** | Sonatype Nexus | Maven snapshot/release hosting |
| **Containerization** | Docker | Image build, tag & push |
| **Container Registry** | Docker Hub | Image distribution |
| **Orchestration** | Kubernetes (K8s) | Production deployment & scaling |
| **Security Audit** | KubeAudit | Kubernetes security posture scanning |
| **Notifications** | Gmail (SMTP) | Build success/failure email alerts |
| **Infrastructure** | AWS EC2 (eu-north-1) | All compute nodes |

---

## 🔁 CI/CD Pipeline Stages

```
Declarative: Tool Install → Git Checkout → Compilation → Test
  → File System Scan → SonarQube Analysis → Quality Gate
  → Build → Publish To Nexus → Build & Tag Docker Image
  → Docker Image Scan → Push Docker Image → Deploy To Kubernetes
  → Verify Deployment → Declarative: Post Actions
```

| Stage | Duration | Description |
|---|---|---|
| Tool Install | ~0.15s | Installs Maven, JDK tooling |
| Git Checkout | ~1s | Pulls latest code from GitHub |
| Compilation | ~3s | `mvn compile` |
| Test | ~12s | `mvn test` — runs all JUnit test cases |
| File System Scan | ~0.79s | Trivy scans source for known CVEs |
| SonarQube Analysis | ~11s | Sends code to SonarQube for analysis |
| Quality Gate | ~2s | Fails build if quality thresholds not met |
| Build | ~13s | `mvn package` — produces `.jar` artifact |
| Publish To Nexus | ~15s | Pushes artifact to Nexus maven-snapshots |
| Build & Tag Docker Image | ~3s | Builds image and tags with build number |
| Docker Image Scan | ~0.54s | Trivy scans the built Docker image |
| Push Docker Image | ~5s | Pushes image to Docker Hub |
| Deploy To Kubernetes | ~0.63s | Applies K8s manifests to cluster |
| Post Actions | ~2s | Sends email notification with Trivy report |

**Total Pipeline Duration: ~1 min 15 sec** ✅

---

## ☁️ AWS Infrastructure

All components are hosted on **AWS EC2 in eu-north-1 (Stockholm)**.

![AWS EC2 Instances](./screenshots/aws-ec2-instances.png)

| Instance | Type | Role |
|---|---|---|
| Master | c7i.flex.large | Kubernetes Master Node |
| Slave-1 | c7i.flex.large | Kubernetes Worker Node |
| Jenkins | m7i.flex.large | Jenkins CI/CD Server (`:8080`) |
| SonarQube | m7i.flex.large | SonarQube Server (`:9000`) |
| Nexus | m7i.flex.large | Nexus Repository Manager (`:8081`) |

### Security Group — DevOps Inbound Rules

| Protocol | Port | Purpose |
|---|---|---|
| HTTPS | 443 | Secure web traffic |
| HTTP | 80 | Application access |
| SSH | 22 | Remote management |
| Custom TCP | 6443 | Kubernetes API server |
| Custom TCP | 3000–10000 | App & DevOps tool ports |
| Custom TCP | 30000–32767 | Kubernetes NodePort range |
| SMTP | 25 | Email notifications |
| SMTPS | 465 | Secure email |

---

## 🔐 Security & Code Quality

### SonarQube Static Analysis

![SonarQube Analysis](./screenshots/sonarqube-analysis.png)

| Metric | Value | Rating |
|---|---|---|
| Bugs | 74 | C |
| Vulnerabilities | 0 | A ✅ |
| Code Smells | 57 | A ✅ |
| Coverage | 30.8% | — |
| Duplications | 29.2% | — |
| Lines | 3.4k | HTML / Java |

### Trivy — Filesystem & Image Scanning
- **Stage 1 (File System Scan):** Scans the codebase before build for known CVEs
- **Stage 2 (Docker Image Scan):** Scans the built container image
- Trivy HTML report is automatically attached to the pipeline notification email

### KubeAudit
Runs post-deployment to validate pod security contexts, network policies, and RBAC settings on the Kubernetes cluster.

---

## 📦 Artifact Management — Nexus Repository

Maven artifacts are published to Nexus under:

```
com.javaproject / database_service_project / 0.0.5-SNAPSHOT
```

![Nexus Artifact Repository](./screenshots/nexus-repo.png)

---

## 🖥️ Deployed Application

The live application is a **BoardGame Database** web app built with Java Spring Boot, deployed via Kubernetes NodePort on port `31968`.

![Deployed Website](./screenshots/deployed-website.png)

Features:
- Lists board games (Splendor, Clue, Linkee)
- User authentication (Login / Sign-up)
- Accessible at `http://<k8s-node-ip>:31968`

---

## 📧 Build Notifications

Jenkins sends an automated email on every build via the **Extended Email Notification** plugin, including the Trivy image scan report as an attachment.

![Gmail Build Notification](./screenshots/gmail-notification.png)

---

## 🚀 How to Run This Pipeline

### Prerequisites
- AWS account with EC2 access
- Jenkins server with plugins: Git, Maven, SonarQube Scanner, Docker Pipeline, Kubernetes CLI, Extended Email
- SonarQube server with a project token
- Nexus repository with `maven-snapshots` repo configured
- Docker Hub account
- Kubernetes cluster (master + worker nodes)

### Setup Steps

1. **Fork this repo** and configure a GitHub webhook pointing to your Jenkins server (`/github-webhook/`)
2. **Create Jenkins credentials** for: GitHub token, DockerHub, SonarQube token, Nexus credentials, Kubeconfig
3. **Configure Jenkins tools:** JDK 17, Maven 3, SonarQube Scanner
4. **Create a Jenkins pipeline job** pointing to this repo's `Jenkinsfile`
5. **Push a commit** — the pipeline triggers automatically!

---

## 📁 Repository Structure

```
Board-Game/
├── src/
│   ├── main/java/          # Spring Boot application source
│   └── test/java/          # JUnit test cases
├── k8s/
│   ├── deployment.yaml     # Kubernetes deployment manifest
│   └── service.yaml        # Kubernetes service (NodePort)
├── screenshots/            # Pipeline screenshots (used in this README)
│   ├── pipeline-overview.png
│   ├── jenkins-stages-build22.png
│   ├── jenkins-multi-pipeline.png
│   ├── aws-ec2-instances.png
│   ├── sonarqube-analysis.png
│   ├── nexus-repo.png
│   ├── deployed-website.png
│   └── gmail-notification.png
├── Dockerfile              # Multi-stage Docker build
├── Jenkinsfile             # Declarative Jenkins pipeline
├── pom.xml                 # Maven project configuration
└── README.md
```

---

## 📊 Build History

| Build | Status | Duration | Notes |
|---|---|---|---|
| #22 | ✅ Success | 1m 15s | Full pipeline — all stages green |
| #21 | ❌ Failed | 2m 30s | Previous run (fixed in #22) |

---

## 🙏 Credits

- Original project template: [NotHarshhaa/DevOps-Projects — Project 35](https://github.com/NotHarshhaa/DevOps-Projects/tree/master/DevOps-Project-35)
- Implemented & deployed by: **[Kenyatta Peter Chacha]** — [@kfchacha](https://github.com/kfchacha)

---

## 📬 Contact

- GitHub: [@kfchacha](https://github.com/kfchacha)
- LinkedIn: *[https://www.linkedin.com/in/kenyatta-chacha/]*

---

*Built with ☕ Java, 🐳 Docker, ⚙️ Jenkins, and a lot of AWS bills.*
