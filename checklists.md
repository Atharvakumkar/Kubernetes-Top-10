# Kubernetes Checklists

## 1) Kubernetes Version updated
Ensure the cluster running a supported kubernetes version with the latest security patches and bug fixes to reduce vulnerabilities and maintain compatibility.

## 2) Cluster Health verified
Verify that all the control plane components and worker nodes are healthy and functioning properly.

## 3) RBAC Configured
Confirm that Role Based Access Control is implemented using the principle of least privilege so users and services only have the permission they need.

## 4) API Server Secured
Ensure the API Server uses TLS encryption, secure authentication, audit logging and restricted access to prevent unauthorized cluster administration.

## 5) Network Policies Applied
Verify that NetworkPolicies restrict the unneccesary communication between Pods, reducing the risk of lateral movement during an attack.

## 6) Pod Security Standards Enforced
Ensure pods follow K8s security standards by preventing privileged containers, host access and other insecure configurations.

## 7) Containers running as Non Root User
Verify the containers do not run as root user, reducing the impact of container compormise and limiting privilege escalation.

## 8) Container Image Scan Passed
Confirm that all the images have been scanned for vulnerabilities and contain no Critical or High severity security issues.

## 9) Secrets Properly Managed
Ensure passwords, API Keys and certificates are stored properly and are never hardcoded into code or manifests.

## 10) Resource Requests & Limits Configured
Verify that CPU and memory requests and limits are defined to prevent resource exhaustion and ensure fair scheduling across the cluster.

## 11) High Availability Configured
Ensure applications have multiple replicas, appropriate scheduling policies, and PodDisruptionBudgets so services remain available during failures or maintenance.

## 12) Health Probes Configured
Verify that Readiness, Liveness, and Startup probes are configured so Kubernetes can detect unhealthy containers and manage traffic appropriately.

## 13) Persistent Storage Verified
Confirm that Persistent Volumes (PVs) and Persistent Volume Claims (PVCs) are correctly configured, accessible, and protected against data loss.

## 14) Monitoring Enabled
Ensure monitoring tools are collecting metrics for the cluster and applications, enabling teams to identify performance or availability issues quickly.

## 15) Centralized Logging Enabled
Verify that application, system, and audit logs are collected centrally to simplify troubleshooting, auditing, and incident investigations.

## 16) Backups Verified
Ensure backups of etcd and persistent application data are configured, recent, and successfully completed without errors.

## 17) Disaster Recovery Tested
Verify that disaster recovery procedures have been tested successfully and the cluster can be restored within the required recovery objectives.

## 18) Performance Testing Completed
Confirm that load, stress, and scalability testing have been performed to ensure the application can handle expected production traffic.

## 19) CI/CD Security Checks Passed
Ensure all automated security gates including SAST, dependency scanning, container image scanning, secret scanning, and IaC scanning—have passed before deployment.

## 20) Production Go-Live Approval
Verify that documentation is complete, rollback procedures are prepared, monitoring is active, and all required stakeholders have approved the production release.
