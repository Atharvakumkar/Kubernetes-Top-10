# Kubernetes Checklists

## 1) Kubernetes Version updated
### Ensure the cluster running a supported kubernetes version with the latest security patches and bug fixes to reduce vulnerabilities and maintain compatibility.

## 2) Cluster Health verified
### Verify that all the control plane components and worker nodes are healthy and functioning properly.

## 3) RBAC Configured
### Confirm that Role Based Access Control is implemented using the principle of least privilege so users and services only have the permission they need.

## 4) API Server Secured
### Ensure the API Server uses TLS encryption, secure authentication, audit logging and restricted access to prevent unauthorized cluster administration.

## 5) Network Policies Applied
### Verify that NetworkPolicies restrict the unneccesary communication between Pods, reducing the risk of lateral movement during an attack.

## 6) Pod Security Standards Enforced
### Ensure pods follow K8s security standards by preventing privileged containers, host access and other insecure configurations.

## 7) Containers running as Non Root User
### Verify the containers do not run as root user, reducing the impact of container compormise and limiting privilege escalation.

## 8) Container Image Scan Passed
### Confirm that all the images have been scanned for vulnerabilities and contain no Critical or High severity security issues.

## 9) Secrets Properly Managed
### Ensure passwords, API Keys and certificates are stored properly and are never hardcoded into code or manifests.

## 10) Resource Requests & Limits Configured
### Verify that CPU and memory requests and limits are defined to prevent resource exhaustion and ensure fair scheduling across the cluster.
