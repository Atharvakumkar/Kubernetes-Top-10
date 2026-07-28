# Kubernetes OWASP Top 10 Security Lab

## Hands-on Kubernetes Security Learning Repository

Kubernetes OWASP Top 10 Security Lab is a practical learning repository that demonstrates the **OWASP Kubernetes Top 10 (2025)** security risks through vulnerable deployments, attack demonstrations, remediation techniques, architecture diagrams, flowcharts, and production hardening practices.

The repository was created to help understand how Kubernetes misconfigurations can be exploited and how to secure clusters using industry best practices through hands-on implementation.

---

# Repository Features

- Complete coverage of the OWASP Kubernetes Top 10 (2025)
- Step-by-step vulnerable Kubernetes deployments
- Practical attack demonstrations
- Secure remediation examples
- Production-ready Kubernetes hardening techniques
- Architecture diagrams for every security topic
- Attack flowcharts illustrating exploitation paths
- Kubernetes production readiness checklist
- Local Kubernetes cluster setup using Kind

---

# Security Topics Covered

- **K01** – Insecure Workload Configurations
- **K02** – Overly Permissive Authorization Configurations
- **K03** – Secrets Management Failures
- **K04** – Lack of Cluster-Level Policy Enforcement
- **K05** – Missing Network Segmentation Controls
- **K06** – Overly Exposed Kubernetes APIs
- **K07** – Weak or Inadequate Authentication
- **K08** – Supply Chain Vulnerabilities
- **K09** – Resource Exhaustion
- **K10** – Inadequate Logging and Monitoring

---

# Documentation Features

Each security topic includes:

- Security Overview
- OWASP Threat Scenario
- Vulnerable Kubernetes Configuration
- Step-by-step Deployment
- Attack Demonstration
- Security Observations
- Hardened Configuration
- Verification Steps
- Architecture Diagram
- Attack Flowchart
- Final Security Summary

---

# Technology Stack

## Container Platform

- Kubernetes
- Kind (Kubernetes in Docker)
- kubectl

---

## Security

- OWASP Kubernetes Top 10 (2025)
- Kubernetes RBAC
- Network Policies
- Pod Security Standards
- Kubernetes Secrets
- Resource Quotas
- Security Contexts

---

## Infrastructure

- Docker
- YAML Manifests
- Linux
- Kind Cluster Configuration

---

## Documentation

- Markdown
- Architecture Diagrams
- Security Flowcharts
- Production Readiness Checklists

---

# Repository Structure

```text
Kubernetes-Top-10/
│
├── K01.md
├── K01_Diagram.png
├── K01_FlowChart.png
│
├── K02.md
├── K02_Diagram.png
├── K02_FlowChart.png
│
├── ...
│
├── K10.md
├── K10_Diagram.png
├── K10_FlowChart.png
│
├── kubernetesArchitecture.md
├── kubernetesComponents.md
├── checklists.md
├── kind-config.yaml
│
├── K8s_Architecture_Diagram.png
├── K8s_Flow_Diagram.png
└── K8s_Flow_Diagram2.png
```

---

# Getting Started

## Clone Repository

```bash
git clone https://github.com/Atharvakumkar/Kubernetes-Top-10.git

cd Kubernetes-Top-10
```

---

## Create Local Kubernetes Cluster

```bash
kind create cluster --config kind-config.yaml
```

Verify the cluster:

```bash
kubectl get nodes
```

---

# Production Readiness Checklist

The repository also contains a Kubernetes production readiness checklist covering:

- Kubernetes Version Management
- RBAC Configuration
- API Server Security
- Network Policies
- Pod Security Standards
- Secrets Management
- Image Security
- Resource Limits
- Logging & Monitoring
- Backup & Disaster Recovery
- High Availability
- Cluster Hardening
- Compliance Verification

---

# Author

**Atharva Kumkar**

---

# License

This repository is intended for educational and research purposes to promote secure Kubernetes practices.

Use the vulnerable examples only in isolated lab environments.
