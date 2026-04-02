# End-to-End DevSecOps CI/CD Pipeline for Full-Stack Blogging App

This project is a comprehensive, production-level implementation of a CI/CD pipeline for a Full-Stack Blogging Application. It covers the entire DevOps lifecycle—from Infrastructure as Code (IaC) and automated security scanning to Kubernetes orchestration and real-time observability.

## 🏗️ Project Architecture & Workflow

The project is divided into four critical phases, ensuring a secure and scalable deployment:

### 1. Infrastructure Setup & Tool Configuration
* **Cloud Provider:** AWS (Amazon Web Services).
* **Environment:** Provisioned virtual machines (EC2) for core DevOps tools:
    * **Jenkins:** CI/CD Automation server.
    * **SonarQube:** Static code quality analysis.
    * **Nexus:** Artifact management and repository.
* **Network Security:** Detailed configuration of Security Groups and IAM roles to ensure secure inter-tool communication.

### 2. CI/CD Pipeline Development (The Heart of the Project)
A robust Jenkins Declarative Pipeline was developed from scratch to automate:
* **Unit Testing:** Validating code logic before building.
* **Static Code Analysis:** Integrated **SonarQube** to identify code smells and vulnerabilities.
* **Security Scanning (Trivy):** * Performed **File System scans** to detect vulnerabilities in dependencies.
    * Performed **Docker Image scans** to ensure container security.
* **Artifact Management:** * Published build artifacts to **Sonatype Nexus**.
    * Pushed finalized container images to **Docker Hub**.

### 3. Kubernetes Deployment (EKS)
* **Orchestration:** Used **Terraform** to provision a production-grade **Amazon EKS (Elastic Kubernetes Service)** cluster.
* **RBAC Implementation:** Configured Role-Based Access Control to securely allow Jenkins to deploy applications into specific Kubernetes namespaces.

### 4. Post-Deployment & Observability
* **Custom Domain:** Configured application access via custom domains.
* **Monitoring Stack:** Implemented a full observability suite using:
    * **Prometheus:** For metric collection.
    * **Blackbox Exporter:** To monitor endpoint health and uptime via the AWS Load Balancer.
    * **Grafana:** To visualize system health and performance through real-time dashboards.

---

## 🛠️ Tech Stack

| Category           | Tools Used                                     |
|--------------------|------------------------------------------------|
| **Cloud** | AWS (EC2, EKS, ELB, IAM)                       |
| **CI/CD** | Jenkins                                        |
| **Infrastructure** | Terraform                                      |
| **Security** | SonarQube, Trivy                               |
| **Artifacts** | Sonatype Nexus, Docker Hub                     |
| **Containers** | Docker, Kubernetes                             |
| **Observability** | Prometheus, Grafana, Blackbox Exporter         |

---

## 🚀 Key DevSecOps Practices Implemented
* **Shift-Left Security:** Integrating Trivy and SonarQube early in the pipeline.
* **Automation:** End-to-end flow from code commit to EKS deployment.
* **Observability:** "Outside-in" monitoring of the production Load Balancer.
* **Principle of Least Privilege:** Using RBAC for Kubernetes security.

---

## 📈 Monitoring & Health Checks
Once deployed, the application health is tracked via Grafana dashboards, monitoring:
* **Probe Success:** Tracking 200 OK status codes from the ELB.
* **Latency:** Measuring response duration for the end-user.
* **Cluster Health:** Monitoring node and pod status within EKS.

---
