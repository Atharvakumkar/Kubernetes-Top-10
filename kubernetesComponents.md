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

## Summary

- A **Node** is a physical or virtual machine that provides the resources required to run applications.
- A **Pod** is the smallest deployable unit in Kubernetes and acts as an abstraction over one or more containers.
- Pods are **ephemeral**, meaning they can be recreated at any time and receive a new IP address.
- A **Service** provides a permanent IP address and DNS name, allowing applications to communicate with Pods without worrying about changing Pod IP addresses.
- An **Ingress** acts as the gateway into the Kubernetes cluster, routing external HTTP and HTTPS traffic to the appropriate Services.