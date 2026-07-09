# Main Kubernetes Components

Kubernetes is built around a few core components that work together to deploy, manage, and expose containerized applications. The most fundamental components are Nodes, Pods, Services, and Ingress. Understanding these components is essential because almost every Kubernetes resource builds upon them.

## Node

A Node is a physical or virtual machine that is part of a Kubernetes cluster.

Nodes provide the computing resources required to run applications. Every application running in Kubernetes is ultimately executed on a Worker Node.

There are two types of Nodes:

- Control Plane Node
- Worker Node

### Each Node contains:

- Kubelet
- Container Runtime (such as containerd or CRI-O)
- Kube Proxy (in most Kubernetes setups)

### Responsibilities:

- Execute Pods
- Provide CPU, memory, and storage
- Communicate with the Control Plane
- Report node health status

### Example:

Think of a Node as a computer inside the Kubernetes cluster. Just like your laptop can run multiple applications, a Kubernetes Node can run multiple Pods simultaneously.

---

## Pod

A Pod is the smallest deployable unit in Kubernetes.

Kubernetes does not deploy containers directly. Instead, it creates Pods, and the containers run inside those Pods.

A Pod acts as an abstraction over one or more containers. This abstraction allows Kubernetes to manage applications more efficiently.

In most real-world scenarios, a Pod contains only one application container. However, multiple containers can exist inside the same Pod if they need to work closely together.

### Characteristics:

- Smallest deployable unit in Kubernetes
- Acts as an abstraction over one or more containers
- Usually contains one application container
- Has its own unique IP address
- Shares storage and networking between containers inside the Pod
- Runs on a single Worker Node

### Why does Kubernetes use Pods?

Instead of managing containers directly, Kubernetes manages Pods because Pods provide:

- A network identity
- Shared storage
- Lifecycle management
- A scheduling unit for Kubernetes

This makes it easier for Kubernetes to deploy, monitor, restart, and replace applications.

### Pods are Ephemeral

Pods are temporary by design.

If a Pod crashes or is deleted, Kubernetes creates a completely new Pod instead of repairing the existing one.

Pods can be recreated because of:

- Node failures
- Application crashes
- Rolling updates
- Scaling operations
- Resource shortages

### Pod IP Address

Every Pod receives its own unique IP address when it is created.

Since Pods are ephemeral, they receive a new IP address whenever they are recreated.

Example:

Old Pod

IP Address: 10.244.1.5

↓

Pod crashes

↓

New Pod

IP Address: 10.244.3.12

This changing IP address is the reason Kubernetes uses Services.

---

## Service

A Service provides a stable and permanent network endpoint for accessing Pods.

Instead of communicating directly with Pod IP addresses, applications communicate with a Service.

The Service automatically forwards requests to the appropriate Pods.

### Characteristics:

- Provides a permanent IP address
- Provides a stable DNS name
- Load balances traffic across multiple Pods
- Automatically discovers healthy Pods
- Continues working even when Pods are recreated

### Lifecycle of Pods and Services

The lifecycle of a Pod and a Service are completely independent.

This means:

- Pods can be created
- Pods can be deleted
- Pods can restart
- Pods can scale up or down

The Service continues to exist and automatically updates its routing to the available Pods.

### How does a Service find Pods?

A Service uses Labels and Selectors to identify which Pods should receive traffic.

For example, if multiple Pods have the label:

```
app=backend
```

The Service routes traffic to all Pods with that label.

Whenever a new Pod with the same label is created, it is automatically added to the Service.

### Example

Suppose an application has three backend Pods.

- Backend Pod A
- Backend Pod B
- Backend Pod C

All three Pods are connected to one Backend Service.

If Backend Pod B crashes:

- Kubernetes creates a replacement Pod.
- The replacement receives a new IP address.
- The Service automatically updates its routing.
- Users continue accessing the application without interruption.

---

## Ingress

An Ingress is a Kubernetes resource that manages external HTTP and HTTPS access to applications running inside the cluster.

It acts as the main entry point for incoming traffic and routes requests to the appropriate Services.

Instead of exposing every Service separately, one Ingress can manage traffic for multiple Services.

### Responsibilities:

- Routes external traffic to the correct Service
- Supports Host-based routing
- Supports Path-based routing
- Enables HTTPS using SSL/TLS certificates
- Acts as a reverse proxy
- Allows multiple applications to share a single external IP address

### Why is Ingress Needed?

Without an Ingress, each application would typically require its own external IP address.

Ingress allows multiple applications to be accessed through a single external IP.

Example:

Host-based routing:

- shop.example.com → Shopping Service
- admin.example.com → Admin Service
- api.example.com → API Service

Path-based routing:

- example.com/shop → Shopping Service
- example.com/admin → Admin Service
- example.com/api → API Service

This simplifies networking and reduces infrastructure costs.

### Request Flow

A typical request in Kubernetes follows this path:

User

↓

Ingress

↓

Service

↓

Pod

The Ingress receives the user's request, forwards it to the correct Service, and the Service sends the request to one of the available Pods.

---

# ConfigMap and Secret

Applications running inside Kubernetes often require configuration data such as database URLs, API endpoints, environment variables, or credentials. Instead of hardcoding these values inside the application or container image, Kubernetes provides **ConfigMaps** and **Secrets** to manage configuration separately from the application.

This separation makes applications more portable, secure, and easier to manage across different environments.

## ConfigMap

A ConfigMap is a Kubernetes object used to store **non-sensitive configuration data** outside of an application.

Instead of embedding configuration values inside the application code or Docker image, they are stored in a ConfigMap and provided to the application when it runs.

This allows the same application image to be deployed in different environments (development, testing, production) using different configurations without rebuilding the image.

### Common Uses of ConfigMap

- Database host names
- API endpoints
- Environment variables
- Application configuration files
- Feature flags
- Logging configurations

### Advantages

- Separates configuration from application code
- Allows configuration changes without rebuilding container images
- Makes applications portable across different environments
- Simplifies application management

### Example

Suppose an application needs to connect to a database.

Instead of hardcoding:

```
Database URL = database.company.com
```

inside the application, this value is stored in a ConfigMap.

If the application is later deployed to another environment where the database URL changes, only the ConfigMap needs to be updated. The application code remains unchanged.

### Important Commands

Create a ConfigMap:

```
kubectl create configmap <configmap-name> --from-literal=KEY=VALUE
```

View ConfigMaps:

```
kubectl get configmaps
```

Describe a ConfigMap:

```
kubectl describe configmap <configmap-name>
```

---

## Secret

A Secret is a Kubernetes object used to store **sensitive information** securely.

Sensitive information includes:

- Passwords
- API Keys
- Database credentials
- Authentication tokens
- TLS certificates
- SSH keys

Secrets help prevent sensitive data from being exposed directly inside application code or configuration files.

### How Secrets are Stored

By default, Kubernetes stores Secret values as **Base64-encoded data**.

It is important to understand that **Base64 is not encryption**. It is simply an encoding format that converts data into a readable string.

Because Base64 can be easily decoded, production Kubernetes clusters should enable **encryption at rest** for etcd to protect Secret data.

### Why Additional Encryption is Needed

Without encryption at rest:

- Anyone with access to etcd can decode the Secret values.
- Sensitive information can be exposed.
- Cluster security is compromised.

For production environments, Kubernetes administrators should configure encryption for Secret data stored in etcd.

### Advantages

- Keeps sensitive information separate from application code
- Prevents passwords from being stored inside container images
- Allows credentials to be updated without rebuilding applications
- Provides better security than embedding credentials directly into code

### Referencing Secrets

Secrets are typically referenced inside a Deployment or Pod.

Applications can consume Secret data as:

- Environment variables
- Mounted files inside a volume

This allows applications to access sensitive information securely at runtime without storing it in the application itself.

### Example

Suppose an application needs to connect to a database.

Instead of writing:

```
Username = admin
Password = mypassword123
```

inside the application code, these credentials are stored in a Secret.

When the Pod starts, Kubernetes provides the Secret to the application, allowing it to authenticate without exposing the credentials in the container image.

### Important Commands

Create a Secret:

```
kubectl create secret generic <secret-name> --from-literal=KEY=VALUE
```

View Secrets:

```
kubectl get secrets
```

Describe a Secret:

```
kubectl describe secret <secret-name>
```

View the encoded Secret:

```
kubectl get secret <secret-name> -o yaml
```

---

## ConfigMap vs Secret

| ConfigMap | Secret |
|------------|--------|
| Stores non-sensitive configuration | Stores sensitive information |
| Data is stored as plain text | Data is Base64 encoded |
| Used for application configuration | Used for passwords, API keys, certificates, and credentials |
| No special security by default | Should be protected with encryption at rest |
| Referenced by Pods or Deployments | Referenced by Pods or Deployments |

---

# Volumes, Deployments, and StatefulSets

Kubernetes applications often need persistent storage and a reliable way to manage Pods. To achieve this, Kubernetes provides **Volumes** for data storage, **Deployments** for managing stateless applications, and **StatefulSets** for managing stateful applications.

Each of these components serves a different purpose and is used depending on the application's requirements.

---

## Volumes

A Volume is a storage mechanism in Kubernetes that allows data to persist beyond the lifecycle of a container.

By default, data stored inside a container is lost when the container stops or is recreated. Volumes solve this problem by providing persistent storage that can be shared with containers inside a Pod.

A Volume is attached to a Pod and can be backed by local storage or external storage systems.

### Why are Volumes Needed?

Without a Volume:

- Application data is lost when a container restarts.
- Logs disappear after Pod recreation.
- Databases cannot safely store data.

With a Volume:

- Data survives container restarts.
- Applications can read and write persistent data.
- Multiple containers inside the same Pod can share data.

### Types of Storage

A Volume can be attached to storage located in different places, such as:

- Local storage on the Worker Node
- On-premises storage systems
- Network File Systems (NFS)
- Cloud storage services (AWS EBS, Azure Disk, Google Persistent Disk, etc.)
- Distributed storage solutions

This flexibility allows Kubernetes applications to store data regardless of where the storage infrastructure is located.

### Characteristics

- Provides persistent storage for applications
- Can survive container restarts
- Can connect to local or external storage
- Can be shared by multiple containers within the same Pod (depending on the volume type)

### Example

Suppose an application allows users to upload profile pictures.

If the application stores images only inside the container, all uploaded files will be lost when the Pod is recreated.

By attaching a Volume to the Pod, the images remain available even if the Pod is restarted or replaced.

---

## Deployment

A Deployment is a Kubernetes object that manages Pods and ReplicaSets.

Instead of creating Pods manually, you create a Deployment, and Kubernetes automatically creates and manages the required Pods.

A Deployment acts as a blueprint for Pods. It describes how the Pods should be created, updated, and maintained.

Deployments are primarily used for **stateless applications**.

### What are Stateless Applications?

A stateless application does not permanently store user data inside the application itself.

Examples include:

- Web servers
- REST APIs
- Frontend applications
- Microservices

If one Pod fails, another Pod can immediately replace it without affecting the application's functionality.

### Responsibilities

- Creates Pods
- Maintains the desired number of replicas
- Automatically replaces failed Pods
- Supports rolling updates
- Supports rollbacks
- Scales applications up or down

### Why use a Deployment?

Without a Deployment:

- Pods must be created manually.
- Failed Pods are not automatically recreated.
- Updating applications becomes difficult.
- Scaling requires manual intervention.

With a Deployment:

- Kubernetes automatically manages the application lifecycle.
- High availability is maintained.
- Updates happen with minimal downtime.

### Example

Suppose an e-commerce website has three frontend Pods.

If one Pod crashes:

- The Deployment detects the failure.
- A replacement Pod is automatically created.
- The application continues serving users without interruption.

### Important Commands

Create a Deployment:

```
kubectl create deployment <deployment-name> --image=<image-name>
```

View Deployments:

```
kubectl get deployments
```

Scale a Deployment:

```
kubectl scale deployment <deployment-name> --replicas=<number>
```

View Deployment details:

```
kubectl describe deployment <deployment-name>
```

---

## StatefulSet

A StatefulSet is a Kubernetes workload object designed to manage **stateful applications**.

Unlike Deployments, StatefulSets ensure that each Pod has a unique identity and stable storage that persists even if the Pod is recreated.

StatefulSets are used for applications where each instance must maintain its own data and identity.

### What are Stateful Applications?

Stateful applications store data that must remain associated with a specific instance.

Examples include:

- Databases
- Distributed databases
- Messaging systems
- File storage systems

Each instance has its own unique data that cannot simply be replaced by another Pod.

### Characteristics

- Provides stable Pod names
- Provides persistent storage for each Pod
- Maintains Pod identity across restarts
- Creates and deletes Pods in a predictable order
- Supports ordered scaling and updates

### Why is Managing Stateful Applications More Difficult?

Managing stateful applications is more complex because Kubernetes must ensure:

- Each Pod keeps the same identity.
- Data is never accidentally assigned to another Pod.
- Storage remains attached to the correct Pod.
- Pods start and stop in a controlled order.

Because of these requirements, StatefulSets are considered more complex than Deployments.

### Example

Consider a database cluster with three database Pods.

Each Pod stores different data.

If one database Pod crashes:

- Kubernetes recreates the Pod.
- The Pod keeps the same name.
- The same storage is reattached.
- The database continues operating with its original data.

This behavior is critical for applications where data consistency is important.

### Important Commands

View StatefulSets:

```
kubectl get statefulsets
```

Describe a StatefulSet:

```
kubectl describe statefulset <statefulset-name>
```

Scale a StatefulSet:

```
kubectl scale statefulset <statefulset-name> --replicas=<number>
```

---

## Why are Databases Often Hosted Outside Kubernetes?

Although Kubernetes can run databases using StatefulSets, many organizations prefer to host databases outside the Kubernetes cluster.

Reasons include:

- Easier backup and recovery
- Better performance
- Simpler storage management
- Dedicated database administration
- Reduced operational complexity
- Managed cloud database services provide automatic maintenance and high availability

Examples of external databases include:

- Amazon RDS
- Google Cloud SQL
- Azure SQL Database
- Dedicated on-premises database servers

This allows Kubernetes to focus on running application workloads while the database is managed separately.

---

## Deployment vs StatefulSet

| Deployment | StatefulSet |
|------------|-------------|
| Used for stateless applications | Used for stateful applications |
| Pods are interchangeable | Each Pod has a unique identity |
| Pod names can change | Pod names remain stable |
| Storage is usually temporary | Each Pod has persistent storage |
| Suitable for web servers, APIs, and microservices | Suitable for databases, messaging systems, and distributed storage |
| Easier to manage | More complex to configure and maintain |

---

## Summary

- A **Volume** provides persistent storage for Pods and can connect to local, on-premises, or cloud-based storage systems.
- A **Deployment** is a blueprint for managing Pods and is best suited for **stateless applications**. It automates scaling, updates, and recovery.
- A **StatefulSet** is designed for **stateful applications** that require stable identities and persistent storage. It ensures that each Pod retains its identity and data across restarts.
- Although Kubernetes supports running databases with StatefulSets, many production environments choose to host databases outside the Kubernetes cluster using dedicated or managed database services.


# Kubernetes Configuration

Kubernetes uses **configuration files** to define how applications and Kubernetes resources should be created and managed. These configuration files are written in YAML format and describe the **desired state** of the cluster.

Instead of manually performing actions step by step, you simply declare what you want Kubernetes to achieve, and Kubernetes continuously works to maintain that state.

---

## Declarative Configuration

Kubernetes follows a **declarative approach** to resource management.

In a declarative model, you define the final desired state of your application rather than the sequence of commands required to achieve it.

For example, instead of telling Kubernetes:

- Create three Pods.
- Restart a Pod if it fails.
- Replace unhealthy Pods.

You simply declare that your application should always have **three running Pods**, and Kubernetes ensures that this condition is continuously maintained.

This makes Kubernetes highly automated and self-healing.

---

## Desired State vs Actual State

One of the core principles of Kubernetes is maintaining the **desired state** of the cluster.

### Desired State

The desired state is the configuration defined by the user in the Kubernetes configuration file.

It specifies how the application should look and behave.

Examples include:

- Number of replicas
- Container image
- CPU and memory requirements
- Environment variables
- Ports
- Volumes

### Actual State

The actual state is the current condition of the cluster.

It represents what is actually running at any given moment.

For example:

Desired State:

- 3 Pods running

Actual State:

- Only 2 Pods are currently running because one Pod crashed.

Since the desired state and actual state do not match, Kubernetes detects the difference and automatically creates a new Pod to restore the desired state.

---

## Role of the Controller Manager

The **Controller Manager** is responsible for continuously checking whether the desired state matches the actual state.

Its job is to perform a process called **reconciliation**.

The reconciliation process works as follows:

1. Read the desired state from the Kubernetes configuration.
2. Compare it with the actual state of the cluster.
3. Detect any differences.
4. Take corrective action if necessary.

For example:

Desired State:

- 5 Pods

Actual State:

- 4 Pods

The Controller Manager detects the missing Pod and creates a replacement automatically.

This continuous monitoring is one of the reasons Kubernetes is considered a self-healing platform.

---

# Three Parts of a Kubernetes Configuration File

Almost every Kubernetes resource consists of three main sections:

1. Metadata
2. Specification (spec)
3. Status

---

## 1. Metadata

The **Metadata** section contains information that identifies the Kubernetes resource.

It provides details used by Kubernetes to uniquely identify and organize objects within the cluster.

Typical metadata includes:

- Resource name
- Namespace
- Labels
- Annotations
- Unique identifiers

Metadata helps Kubernetes distinguish one resource from another and enables features such as resource selection, grouping, and organization.

### Purpose

- Identifies the resource
- Organizes resources
- Supports labels and selectors
- Enables resource management

---

## 2. Specification (spec)

The **Specification**, commonly written as **spec**, is the most important part of a Kubernetes configuration file.

It defines the **desired state** of the resource.

Everything you want Kubernetes to create or maintain is described inside the spec section.

Depending on the resource type, the specification may define:

- Number of replicas
- Container images
- Network ports
- Environment variables
- Storage volumes
- Resource limits
- Restart policies

Kubernetes reads the specification and attempts to make the actual cluster match this desired configuration.

### Purpose

- Defines the desired state
- Describes how the resource should behave
- Specifies application configuration
- Instructs Kubernetes what to create and manage

---

## 3. Status

The **Status** section represents the **current state** of the resource.

Unlike metadata and specification, the status is **not written or managed by the user**.

Instead, Kubernetes automatically generates and updates this section as the cluster changes.

The status may contain information such as:

- Number of running Pods
- Current health
- Assigned IP addresses
- Resource conditions
- Current phase
- Error messages

Because the status reflects the live state of the cluster, it changes dynamically during the application's lifecycle.

### Purpose

- Shows the current state of the resource
- Automatically maintained by Kubernetes
- Provides health and runtime information
- Helps administrators monitor applications

---

## Configuration File Workflow

The lifecycle of a Kubernetes configuration file typically follows these steps:

1. The user creates a configuration file describing the desired state.
2. The configuration is submitted to the API Server.
3. The API Server stores the desired state in etcd.
4. The Controller Manager continuously compares the desired state with the actual state.
5. If differences are found, Kubernetes automatically takes corrective actions.
6. The Status section is updated to reflect the current state of the resource.

---

## Important Commands

Apply a configuration file:

```bash
kubectl apply -f <file-name>.yaml
```

Create resources from a configuration file:

```bash
kubectl create -f <file-name>.yaml
```

View all resources:

```bash
kubectl get all
```

Describe a resource:

```bash
kubectl describe <resource-type> <resource-name>
```

Delete resources defined in a configuration file:

```bash
kubectl delete -f <file-name>.yaml
```

---

## Summary

- Kubernetes uses **declarative configuration files** to describe the desired state of applications and resources.
- The **Controller Manager** continuously compares the **desired state** with the **actual state** and performs reconciliation whenever differences are detected.
- Every Kubernetes configuration file generally consists of three main parts:
  - **Metadata** – Identifies and organizes the resource.
  - **Specification (spec)** – Defines the desired state of the resource.
  - **Status** – Represents the current state of the resource and is automatically generated and maintained by Kubernetes.
- This declarative and self-healing approach is one of the key features that makes Kubernetes reliable and easy to manage in production environments.
