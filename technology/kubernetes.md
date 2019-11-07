# Kubernetes

Kubernetes is an open source container orchestration platform, allowing large numbers of containers to work together in harmony, reducing operation burden. It helps with things like:

- Running containers across many different machines (Deployment)
- Scaling up or down by adding or removing containers according to demand
- Keeping storage consistent with multiple instances of application
- Distributing load between containers (Load balancing)
- Launching new containers when another fails
- Service discovery
- Logging
- Monitoring

It orchestrates computing, networking, and storage infrastructure on behalf of user workloads.

## What Kubernetes is not
- Does not deploy source code and does not build your application. (NOT CI/CD)
- Does not provide application-level services such as middleware, data-processing, databases, or caches. Such components can run on Kubernetes and accessed by applications through portable mechanisms, such as Open Service Broker
- Does not dictate logging, monitoring, or alerting solutions. It provides some integrations as proof of conencept, and mechanisms to collect and export metrics.
- Does not provide nor adopt any comprehensive machine configuration, maintenance, management, or self-healing systems.

## Why containers?
![picture](https://github.com/obedtandadjaja/knowledge-base/blob/master/pictures/Screen%20Shot%202019-05-17%20at%203.27.21%20PM.png?raw=true)

The New Way is to deploy containers based on operating-system-level virtualization rather than hardware virtualization. These containers are isolated from each other and from the host: they have their own filesystems, they can’t see each others’ processes, and their computational resource usage can be bounded. They are easier to build than VMs, and because they are decoupled from the underlying infrastructure and from the host filesystem, they are portable across clouds and OS distributions.

- **Agile application creation and deployment**: Increased ease and efficiency of container image creation compared to VM image use.
- **Continuous development, integration, and deployment**: Provides for reliable and frequent container image build and deployment with quick and easy rollbacks (due to image immutability).
- **Dev and Ops separation of concerns**: Create application container images at build/release time rather than deployment time, thereby decoupling applications from infrastructure.
- **Observability**: Not only surfaces OS-level information and metrics, but also application health and other signals.
- **Environmental consistency across development, testing, and production**: Runs the same on a laptop as it does in the cloud.
- **Cloud and OS distribution portability**: Runs on Ubuntu, RHEL, CoreOS, on-prem, Google Kubernetes Engine, and anywhere else.
- **Application-centric management**: Raises the level of abstraction from running an OS on virtual hardware to running an application on an OS using logical resources.
- **Loosely coupled, distributed, elastic, liberated micro-services**: Applications are broken into smaller, independent pieces and can be deployed and managed dynamically – not a monolithic stack running on one big single-purpose machine.
- **Resource isolation**: Predictable application performance.
- **Resource utilization**: High efficiency and density.

## Architecture
At its base, Kubernetes brings together individual physical or virtual machines into a cluster using a shared network to communicate between each server, This cluster is the physical platform where all components, capabilities and workloads are configured.

Each machine are given a role in the Kubernetes ecosystem.

### Master Server
One server is selected to be the master server. Acts as the primary point of contact with the cluster and is responsible for most of the centralized logic Kubernetes provides

Responsibilities:
- Acs as a gateway and brain for the cluster by exposing an API for users and clients
- Health checking other servers
- Decide how best to split up and assign work (scheduling)
- Orchestrating communication between other components

To start up an application or service, a delcarative plan is submitted in JSON or YAML defining what to create and how it should be managed. The master server then takes the plan and figures out how to run it on the infrastructure by examining the requirements and the current state of the system

### Node Servers
Responsible for accepting and running workloads using local and external resources.

Kubernetes runs applications and services in containers, so each node needs to be equipped with Dockerfile. This is to help with isolation, management and flexibility.

The node servers receive work instructions from the master server and creates or destroys containers accordingly, adjusting networking rules to route and forward traffic appropriately.

## Objects
While containers are the underlying mechanism used to deploy applications, Kubernetes uses additional layers of abstraction over the container interface to provide scaling, resiliency, and life cycle management features. Instead of managing containers directly, users define and interact with instances composed of various primitives provided by the Kubernetes object model

### Pods
A pod is the most basic unit that Kubernetes deals with. Containers themselves are not assigned to hosts, instead, one or more tightly coupled containers are encapsulated in an object called a pod

Represents one or more containers that should be controlled as a single application. Pods consist of containers that operate closely together, share a life cycle, and should always be scheduled on the same node. They share environment, volumes, and IP space. 

Horizontal scaling is generally discouraged on the pod level because there are other higher level objects more suited for the task.

### Replication Controllers and Replication Sets
With Kubernetes you will be managing groups of identical, replicated pods. These are created from pod templates and can be horizontally scaled by controllers known as replication controllers and replication sets.

#### Replication controller
An object that defines a pod template and control parameters to scale identical replicas of a pod horizontally by increasing or decreasing the number of running copies

Responsible for ensuring that the number of pods deployed matches the number of pods in the config. When a pod fails, the controller with start new pods to compensate

It can also perform rolling updates to roll over a set of pods to a new version one by one, minimizing the impact on application availability

#### Replication set
Replica sets are similar to replication controllers except that they have more options for the selector.

## Workload

### Deployments
Uses replication sets as a building block, adding flexible life cycle management functionality to the mix.

While deployments built with replications sets may appear to duplicate the functionality offered by replication controllers, deployments solve many of the pain points that existed in the implementation of rolling updates. When updating applications with replication controllers, users are required to submit a plan for a new replication controller that would replace the current controller. When using replication controllers, tasks like tracking history, recovering from network failures during the update, and rolling back bad changes are either difficult or left as the user's responsibility.

Deployments can be modified easily by changing the configuration and Kubernetes will adjust the replica sets, manage transitions between different application versions, and optionally maintain event history and undo capabilities automatically

### Stateful sets
Stateful sets are specialized pod controllers that offer ordering and uniqueness guarantees. Primarily, these are used to have more fine-grained control when you have special requirements related to deployment ordering, persistent data, or stable networking. For instance, stateful sets are often associated with data-oriented applications, like databases, which need access to the same volumes even if rescheduled to a new node.

Stateful sets provide a stable networking identifier by creating a unique, number-based name for each pod that will persist even if the pod needs to be moved to another node. Likewise, persistent storage volumes can be transferred with a pod when rescheduling is necessary. The volumes persist even after the pod has been deleted to prevent accidental data loss.

### Daemon sets
Daemon sets are another specialized form of pod controller that run a copy of a pod on each node in the cluster (or a subset, if specified). This is most often useful when deploying pods that help perform maintenance and provide services for the nodes themselves.

For instance, collecting and forwarding logs, aggregating metrics, and running services that increase the capabilities of the node itself are popular candidates for daemon sets. Because daemon sets often provide fundamental services and are needed throughout the fleet, they can bypass pod scheduling restrictions that prevent other controllers from assigning pods to certain hosts

### Jobs and CRON jobs
The workloads we've described so far have all assumed a long-running, service-like life cycle. Kubernetes uses a workload called jobs to provide a more task-based workflow where the running containers are expected to exit successfully after some time once they have completed their work. Jobs are useful if you need to perform one-off or batch processing instead of running a continuous service.

Building on jobs are cron jobs. Like the conventional cron daemons on Linux and Unix-like systems that execute scripts on a schedule, cron jobs in Kubernetes provide an interface to run jobs with a scheduling component. Cron jobs can be used to schedule a job to execute in the future or on a regular, reoccurring basis. Kubernetes cron jobs are basically a reimplementation of the classic cron behavior, using the cluster as a platform instead of a single operating system.

## Components

### Services
A component that acts as a basic internal load balancer and ambassador for pods. A service groups together logical collections of pods that perform the same function to present them as a single entity.

## Resource
https://www.digitalocean.com/community/tutorials/an-introduction-to-kubernetes

# Practical

## Create a deployment

A Kubernetes deployment checks on the health of your Pod and restarts the Pod's container if it terminates. Deployments are the recommended way to manage the creation and scaling of Pods

1. Use the `kubectl create` command to create a Deployment. The Pod runs a container based on the provided Docker image

```
kubectl create deployment hello-node --image=gcr.io/hello-minikube-zero-install/hello-node
```

2. View the deployment

```
kubectl get deployments
```

Output:

```
NAME         READY   UP-TO-DATE   AVAILABLE   AGE
hello-node   1/1     1            1           1m
```

3. View the Pod

```
kubectl get pods
```

Output:

```
NAME                          READY     STATUS    RESTARTS   AGE
hello-node-5f76cf6ccf-br9b5   1/1       Running   0          1m
```

4. View cluster events

```
kubectl get events
```

5. View the `kubectl` configuration

```
kubectl config view
```

For more information about `kubectl` commands see https://kubernetes.io/docs/user-guide/kubectl-overview/

## Create a service

By default, the Pod is only accessible by its internal IP address within the Kubernetes cluster. To make the hello-node Container accessible from outside the Kubernetes virtual network, you have to expose the Pod as a Kubernetes Service.

1. Expose the Pod to the public internet using the `kubectl expose` command

```
kubectl expose deployment hello-node --type=LoadBalancer --port=8080
```

The `--type=LoadBalancer` flag indicates that you want to expose your Service outside of the cluster

2. View the service you just created

```
kubectl get services
```

Output:

```
NAME         TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
hello-node   LoadBalancer   10.108.144.78   <pending>     8080:30369/TCP   21s
kubernetes   ClusterIP      10.96.0.1       <none>        443/TCP          23m
```

On cloud providers that support load balancers, an external IP address would be provisioned to access the service. On Minikube, the `LoadBalancer` type makes the Service accessible through the `minikube service` command.

3. Run the following command

```
minikube service hello-node
```

## Deleting resources

You can clean up the resources you created in your cluster easily

```
kubectl delete service hello-node
kubectl delete deployment hello-node
```

## Scaling a deployment

1. Check deployment

```
kubectl get deployments
```

2. Scale the replicas

```
kubectl scale deployments/<name of deployment> --replicas=4
```

3. Check that the pods are scaling

```
kubectl get pods -o wide
```

4. Verify change in deployment

```
kubectl describe deployments/<name of deployment>
```

## Performing rolling update

By default, the maximum number of Pods that can be unavailable during the update and the maximum number of new Pods that can be created, is one. Both options can be configured to either numbers or percentages. In Kubernetes, updates are versioned and any Deployment update can be reverted to previous stable version.

1. Verify the current image version

```
kubectl describe pods
```

2. Update the image to updated version

```
kubectl set image deployments/<name of deployment> <name of updated image>
```

3. Confirm the update

```
kubectl rollout status deployments/<name of deployment>
```

4. Rollback an update

```
kubectl rollout undo deployments/<name of deployment>
```

## Resource
https://kubernetes.io/docs/tutorials/hello-minikube/
