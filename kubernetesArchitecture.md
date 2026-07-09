# Kubernetes Architecture
## Control Plane:
It is the brain of the Kubernetes cluster. It is responsible for managing the entire cluster, making decisions, making desired state and ensuring the application runs as expected. <br>

Every action performed in Kubernetes such as creating Pod, scaling an application or deleting a deployment is coordinated through the Control Plane.

### Main components of Control Plane:

### Node:

A Node is a physical or virtual machine that is part of a Kubernetes cluster.

Nodes provide the computing resources required to run applications.

#### Each node contains: <br>

• Kubelet <br>
• Container Runtime <br>
• Kube Proxy (in most kubernetes setups) <br>

#### Responsibilties: <br>

• Execute Pods <br>
• Provide CPU, memory and storage <br>
• Communicate with control plane <br>
• Report health status <br>

### Kubelet:

Kubelet is an agent that runs on every Worker Node.

It continuously communicates with the API Server to determine which Pods should be running on its node.

If the Scheduler assigns a Pod to a Worker Node, the Kubelet receives the instructions and ensures the Pod is created and remains healthy.

#### Responsibilites:
• Registers the node with the Control Plane <br>
• Starts containers <br>
• Monitors Pod health <br>
• Reports node and Pod status <br>
• Restarts failed containers if necessary <br>

Kubelet does not decide where Pods should run. That responsibility belongs to the Scheduler.

### Worker Node:
A Worker Node is the machine where the application workloads actually run.

Unlike the Control Plane, which manages the cluster, Worker Nodes execute containers and consume most of the cluster's computing resources.

Production Kubernetes clusters typically have many Worker Nodes, each equipped with substantial CPU, memory, and storage to handle application workloads.

#### A Worker Node usually contains:

• Kubelet <br>
• Container Runtime (such as containerd or CRI-O) <br>
• Kube Proxy <br>
• Running Pods <br>

#### Characteristics:

• Handles application workloads <br>
• Uses significantly more CPU and memory than the Control Plane <br>
• Can run hundreds of Pods depending on available resources <br>
• Scales horizontally by adding more Worker Nodes to the cluster <br>

#### Example: 
In an e-commerce application: <br>

• The Control Plane decides where Pods should run <br>
• Worker Node 1 runs the frontend Pods <br>
• Worker Node 2 runs the backend API Pods <br>
• Worker Node 3 runs the database Pods <br>
• Kubelets on each Worker Node ensure those Pods remain healthy and running <br>

### API Server: 
The API Server is the central communication hub of Kubernetes and acts as the entry point to the cluster. Every request made to Kubernetes, whether from a user, the kubectl command-line tool, automation scripts, or other Kubernetes components, first goes to the API Server. <br>

Without the API server, no component in Kubernetes can interact with the cluster.<br>

#### Responsibilities:<br>
• Accepts all REST API requests <br>
• Authenticates and authorizes users <br>
• Validates requests before execution <br>
• Stores cluster information in etcd <br>
• Provides a communication interface for all Kubernetes components <br>

#### Example: <br>
When you execute a command such as:

``` 
kubectl create deployment nginx
```

The request is first sent to the API Server. The API Server validates it and stores the desired state in etcd before informing other components to take action. <br>

### Controller Manager:

The Controller Manager continuously monitors the current state of the cluster and compares it with the desired state stored in etcd. If any difference is detected, it automatically takes corrective action.

This follows Kubernetes' reconciliation principle, where the system constantly works to ensure the actual state matches the desired state.

For example, if an application should have three Pods running but one crashes, the Controller Manager detects the missing Pod and creates a replacement automatically.

#### Common controllers include:

• Deployment Controller <br>
• ReplicaSet Controller <br>
• Node Controller <br>
• Job Controller <br>
• Endpoint Controller <br>

#### Responsibilties: <br>

• Monitors cluster health <br>
• Replaces failed Pods <br>
• Maintains the desired number of replicas <br>
• Detects failed nodes <br>
• Automatically performs corrective actions <br>

### Scheduler: 
The Scheduler decides which Worker Node should run a newly created Pod.

When a Pod is created, it initially has no assigned node. The Scheduler evaluates all available Worker Nodes and selects the most suitable one based on several factors.

#### Factors considered by the Scheduler:

• Available CPU resources <br>
• Available memory <br>
• Node labels <br>
• Taints and tolerations <br>
• Node affinity and anti-affinity rules <br>
• Resource requests and limits <br>
• Existing workload on each node <br>

The Scheduler only makes the placement decision. It does not actually start the Pod. After selecting a node, the Kubelet on that node is responsible for creating and running the Pod.

#### Example:
Suppose your Cluster has three Worker Nodes:

#### Node A 
• 90% CPU utilization

#### Node B
• 30% CPU utilization

#### Node C
• 60% CPU utilization

If a new Pod requires moderate resources, the Scheduler is likely to assign it to Node B because it has the most available capacity.

### etcd:
etcd is Kubernetes' distributed key-value database and serves as the backing store for the entire cluster. 

It stores every important piece of information about the cluster, including: <br>

• Pods <br>
• Deployments <br>
• Services <br>
• ConfigMaps <br>
• Secrets <br>
• Nodes <br>
• Cluster configuration <br>
• Current cluster state <br>
• Desired cluster state <br>

Whenever changes are made to the cluster, the API Server records them in etcd.

Since etcd contains the complete state of the Kubernetes cluster, losing it means losing the cluster's configuration and state information.

Because of this, regular backups of etcd are considered one of the most important administrative tasks in Kubernetes.

#### Why etcd is important?

If etcd becomes corrupted or is accidentally deleted:

Cluster configuration is lost.
Deployments disappear.
Service information is lost.
Secrets and ConfigMaps are lost.
Recovery becomes extremely difficult without backups.

For production environments, administrators should regularly back up etcd to ensure disaster recovery.

#### Important Command -
``` 
etcdctl snapshot save backup.db
```
This command creates a snapshot of the etcd database that can be later be restored if needed.

### Virtual Network:
A Kubernetes Virtual Network enables communication between all components in the cluster, regardless of which Worker Node they are running on.

Unlike traditional networking, Kubernetes assumes that every Pod can communicate directly with every other Pod without requiring Network Address Translation (NAT).

#### The Virtual Network connects: <br>

• Pods <br>
• Worker Nodes <br>
• Services <br>
• Control Plane components <br>

#### Key characteristics:

• Every Pod receives it's own unqiue IP address <br>
• Pods can communicate directly across different nodes <br>
• Containers inside the same Pod share the same network namespace <br>
• Services provide stable virtual IPs for accessing Pods <br>
• Networking is typically implemented using Container Network Interface (CNI) plugins such as Calico, Flannel, or Cilium <br>

#### Example:
A frontend Pod running on Worker Node 1 can communicate directly with a backend Pod running on Worker Node 3 through the Kubernetes virtual network without needing to know the physical network topology.

