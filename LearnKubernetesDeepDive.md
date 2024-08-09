# Learn Kubernetes: A Deep Dive

### Kubernetes Background
**Kubernetes** is an **application orchestrator**. For the most part, it orchestrates **containerized cloud-native microservices apps**

#### What is an orchestrator
An orchestrator is a system that deploys and manages applications. It can deploy your applications and dynamically respond
to changes. For example, Kubernetes can:
- Deploy your application
- Scale it up and down dynamically according to demand
- Self-heal it when things break
- Perform zero-downtime rolling updates and rollbacks
- And the best part about Kubernetes: it can do all of that without you having to supervise or get involved in decisions. Obviously, you have to set things up in the first place, but, once you’ve done that, you can sit back and let Kubernetes work its magic.

#### What is a containerized app
A containerized application is an app that runs in a container.

Before we had containers, applications ran on physical servers or in virtual machines. Containers are the next iteration
of how we package and run our apps, and they’re faster, more lightweight, and more suited to modern business requirements 
than servers and virtual machines.

Think of it this way:
- Applications ran on physical servers in the age of open-system(roughly in the 1980s and 1990s)
- Applications ran in virtual machines in the age of virtual machines (during the 2000s and into the 2010s)
- Applications run in containers in the cloud-native era (present time)
- While Kubernetes can orchestrate other workload types, including virtual machines and serverless functions, it’s most commonly used to orchestrate containerized apps.

#### What is a cloud-native app
A cloud-native application is an application that is designed to meet modern business demands
(auto-scaling, self-healing, rolling updates, etc.) and can run on Kubernetes.  

Note: They absolutely can run on a public cloud, but they can run anywhere that you have Kubernetes – even your on-premises data center.  

#### What is a microservices app
A microservices app is a business application that is built from lots of small specialized parts that communicate 
and form a meaningful application. 

```
For example, you might have an e-commerce app that comprises all of the following small specialized components:
Web front-end
Catalog service
Shopping cart
Authentication service
Logging service
Persistent store
```
- Each of these individual services can be implemented as a microservice. 
- Typically, each can be coded and looked after by a different team, and each can have its own release cadence and can be scaled independently of all others. 
- For example, you can patch and scale the logging microservice without affecting any of the other application components. 
- Building applications this way is an important aspect of a cloud-native application.

#### Summary
Kubernetes deploys and manages (orchestrates) applications that are packaged and run as containers (containerized) and that 
are built in ways (cloud-native microservices) that allow them to scale, self-heal, and be updated in line with modern business requirements.


### The Operating System of the Cloud
Kubernetes has emerged as the de-facto platform for deploying and managing cloud-native applications. In many ways, 
it’s like an operating system (OS) for the cloud. Consider this:
- You install a traditional OS (Linux or Windows) on a server, and the OS abstracts the physical server’s resources and schedules processes etc.
- You install Kubernetes on a cloud, and it abstracts the cloud’s resources and schedules the various microservices of cloud-native applications.

In the same way that Linux abstracts the hardware differences of different server platforms, Kubernetes abstracts the
differences between different private and public clouds. Net result: as long as you’re running Kubernetes, it doesn’t matter 
if the underlying systems are on-premises in your own data center, edge clusters, or in the public cloud.

With this in mind, Kubernetes enables a true hybrid cloud, allowing you to seamlessly move and balance workloads across multiple 
different public and private cloud infrastructures. You can also migrate to and from different clouds, meaning you can choose a 
cloud today and not have to stick with that decision for the rest of your life.

#### A quick analogy
- Consider the process of sending goods via a courier service. You package the goods in the courier’s standard packaging, put a label on it, and hand it over to the courier the courier abstracts everything else and takes care of scheduling and other logistics.
- It’s the same for apps on Kubernetes. You package the app as a container, give it a declarative manifest, and let Kubernetes take care of deploying it and keeping it running. You also get a rich set of tools and APIs that let you introspect (observe and examine) your app. It’s a beautiful thing.
 


## Kubernetes Principles of Operation
 
### Kubernetes From 40K Feet
At the highest level, Kubernetes is two things:
- A cluster for running applications.
- An orchestrator of cloud-native microservices apps.


**Kubernetes as a cluster**
 - Kubernetes is like any other cluster – a bunch of nodes and a control plane. The control plane exposes an API and 
 records the state in a persistent store; it also has a scheduler for assigning work to nodes. Nodes are where application 
 services run.
 - It can be useful to think of the control plane as the brains of the cluster and the nodes as the muscle. In this analogy, 
 the control plane is the brain because it implements all of the important features, such as auto-scaling and zero-downtime 
 rolling updates. The nodes are the muscle because they do the every-day hard work of executing application code. 
 
**Kubernetes as an orchestrator**
 - Orchestrator is just a fancy word for a system that takes care of deploying and managing applications.



#### How it works
- To make this happen, you start out with an app; you package it up and give it to the cluster (Kubernetes). The cluster is made up of one or more masters and a bunch of nodes.
- The masters, sometimes called heads or head nodes, are in charge of the cluster. This means they make scheduling decisions, perform monitoring, implement changes, respond to events, and more. For these reasons, we often refer to the masters as the control plane.
- The nodes are where application services run, and we sometimes call them the data plane. Each node has a reporting line back to the masters and constantly watches for new work assignments.

![Screenshot 2022-04-16 at 4 01 08 PM](https://user-images.githubusercontent.com/22169012/163671604-eac58ccf-572a-4019-9f2b-278ccee95d8d.png)


To run applications on a Kubernetes cluster, we follow this simple pattern:
- Write the application as small independent microservices in our favorite languages.
- Package each microservice in its own container.
- Wrap each container in its own Pod.
- Deploy Pods to the cluster via higher-level controllers, such as, Deployments, DaemonSets, StatefulSets, CronJobs etc.
  - Deployments offer scalability and rolling updates.   
  - DaemonSets run one instance of a service on every node in the cluster.  
  - StatefulSets are for stateful application components.  
  - CronJobs are for short-lived tasks that need to run at set times.


Kubernetes likes to manage applications **declaratively**. This is a pattern where you describe how you want your application to 
look and feel in a set of YAML files. You POST these files to Kubernetes, then sit back while Kubernetes makes it all happen.

But it doesn’t stop there. Because the declarative pattern tells Kubernetes how an application should look, Kubernetes can watch it and make sure things don’t stray from what you asked for. If something isn’t as it should be, Kubernetes tries to fix it.

### Control Plane (Master)
A Kubernetes cluster is made of masters and nodes. These are Linux hosts that can be virtual machines (VM), bare metal
servers in your data center, or instances in a private or public cloud. A Kubernetes master is a collection of system 
services that make up the control plane of the cluster.

- The simplest setups run all the master services on a single host. However, this is only suitable for labs and test environments. 
- For production environments, multi-master high availability (HA) is a must have. This is why the major cloud providers implement HA masters as part of their hosted Kubernetes platforms, such as Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), and Google Kubernetes Engine (GKE).
- Generally speaking, running 3 or 5 replicated masters in an HA configuration is recommended.
- It’s also considered a good practice not to run user applications on masters. This allows masters to concentrate entirely on managing the cluster.

![Screenshot 2022-04-16 at 7 01 55 PM](https://user-images.githubusercontent.com/22169012/163676866-018e80c1-37cf-4fe7-b3e4-9a895a6797e5.png)


##### The API server
The API server is the Grand Central Station of Kubernetes. All communication, between all components, must go through the API server,  it’s important to understand that internal system components, as well as external user components, all communicate via the same API.
- It exposes a RESTful API that you POST YAML configuration files to over HTTPS. These YAML files, which we sometimes call manifests, contain the desired state of your application. This desired state includes things like; which container image to use, which ports to expose, and how many Pod replicas to run.
- All requests to the API Server are subject to authentication and authorization checks, but, once these are done, the config in the YAML file is validated, persisted to the cluster store, and deployed to the cluster.


##### Cluster store
- The cluster store is the only stateful part of the control plane, and it persistently stores the entire configuration and state of the cluster. As such, it’s a vital component of the cluster – no cluster store, no cluster.
- The cluster store is currently based on etcd, a popular distributed database. As it’s the single source of truth for the cluster, you should run between 3-5 etcd replicas for high availability, and you should provide adequate ways to recover when things go wrong.
- On the topic of availability, etcd prefers consistency over availability. This means that it will not tolerate a split-brain situation and will halt updates to the cluster in order to maintain consistency. However, if etcd becomes unavailable, applications running on the cluster should continue to work, you just won’t be able to update anything.
- As with all distributed databases, the consistency of writes to the database is vital. For example, multiple writes to the same value originating from different nodes needs to be handled. etcd uses the popular RAFT consensus algorithm to accomplish this.


##### The controller manager
- The controller manager implements all of the background control loops that monitor the cluster and respond to events.
- It’s a controller of controllers, meaning it spawns all of the independent control loops and monitors them.
- Some of the control loops include the node controller, the endpoint controller, and the ReplicaSet controller. Each one runs as a background watch loop that is constantly watching the API server for changes – the aim of the game is to ensure the current state of the cluster matches the desired state (more on this shortly).

```
The logic implemented by each control loop is effectively this:
1. Obtain desired state
2. Observe current state
3. Determine differences
4. Reconcile differences
```

This logic is at the heart of Kubernetes and declarative design patterns. Each control loop is also extremely specialized and only interested in its own little corner of the Kubernetes cluster. No attempt is made to over complicate things by implementing awareness of other parts of the system - each control loop takes care of its own business and leaves everything else alone. This is key to the distributed design of Kubernetes and adheres to the Unix philosophy of building complex systems from small specialized parts.

##### The scheduler
- At a high level, the scheduler watches the API server for new work tasks and assigns them to the appropriate healthy nodes. Behind the scenes, it implements complex logic that filters out nodes incapable of running the task and then ranks the nodes that are capable. The ranking system is complex, but the node with the highest ranking score is selected to run the task.

- When identifying nodes that are capable of running a task, the scheduler performs various predicate checks. These include: Is the node tainted? Are there any affinity or anti-affinity rules? Is the required network port available on the node? Does the node have sufficient free resources? Any node incapable of running the task is ignored, and the remaining nodes are ranked according to things such as: Does the node already have the required image? How much free resource does the node have? How many tasks is the node already running? Each criterion is worth points, and the node with the most points is selected to run the task.

- If the scheduler cannot find a suitable node, the task cannot be scheduled and is marked as pending. The scheduler isn’t responsible for running tasks, just picking the nodes a task will run on.


##### The cloud controller manager
If you’re running your cluster on a supported public cloud platform, such as AWS, Azure, GCP, DO, IBM Cloud, etc., your control plane will be running a cloud controller manager. Its job is to manage integrations with underlying cloud technologies and services, such as instances, load balancers, and storage. For example, if your application asks for an internet facing load balancer, the cloud controller manager is involved in provisioning an appropriate load balancer on your cloud platform.


### Nodes
```
Nodes are the workers of a Kubernetes cluster. At a high level, they do three things:

1. Watch the API Server for new work assignments.
2. Execute new work assignments.
3. Report back to the control plane (via the API server).
```

![Screenshot 2022-04-16 at 6 31 13 PM](https://user-images.githubusercontent.com/22169012/163675953-4b41f1d9-65d5-43a8-b2fa-08157058596b.png)


#### Kubelet
- The kubelet is the star of the show on every node. It’s the main Kubernetes agent, and it runs on every node in the cluster. In fact, it’s common to use the terms node and kubelet interchangeably.

- When you join a new node to a cluster, the process installs the kubelet onto the node. The kubelet is then responsible for registering the node with the cluster. Registration effectively pools the node’s CPU, memory, and storage into the wider cluster pool.

- One of the main jobs of the kubelet is to watch the API server for new work assignments. Any time it sees one, it executes the task and maintains a reporting channel back to the control plane.

- If a kubelet can’t run a particular task, it reports back to the master and lets the control plane decide what actions to take. For example, if a kubelet cannot execute a task, it is not responsible for finding another node to run it on. It simply reports back to the control plane, and the control plane decides what to do.

#### Container runtime
- The kubelet needs a container runtime to perform container-related tasks – things like pulling images and starting and stopping containers.

- In the early days, Kubernetes had native support for a few container runtimes, such as Docker. More recently, it has moved to a plugin model called the Container Runtime Interface (CRI). At a high level, the CRI masks the internal machinery of Kubernetes and exposes a clean documented interface for third-party container runtimes to plug into.

- There are many container runtimes available for Kubernetes. One popular example is cri-containerd. This is a community-based, open-source project, porting the CNCF containerd runtime to the CRI interface. It has a lot of support and is replacing Docker as the most popular container runtime used in Kubernetes.

Note: containerd (pronounced “container-dee”) is the container supervisor and runtime logic stripped out from the Docker Engine. It was donated to the CNCF by Docker, Inc. and has a lot of community support. Other CRI-compliant container runtimes exist as well.

#### Kube-proxy 
The last piece of the node puzzle is the kube-proxy. This runs on every node in the cluster and is responsible for local cluster networking. For example, it makes sure each node gets its own unique IP address and implements local IPTABLES or IPVS rules to handle routing and load-balancing of traffic on the Pod network.


### Kubernetes DNS
- As well as the various control plane and node components, every Kubernetes cluster has an internal DNS service that is vital to operations.

- The cluster’s DNS service has a static IP address that is hard-coded into every Pod on the cluster, meaning all containers and Pods know how to find it. Every new service is automatically registered with the cluster’s DNS so that all components in the cluster can find every Service by name. Some other components that are registered with the cluster DNS are StatefulSets and the individual Pods that a StatefulSet manages.
 
![Screenshot 2022-04-16 at 7 12 23 PM](https://user-images.githubusercontent.com/22169012/163677200-efcb14dc-8718-4b61-9aab-17e6cd8c08bc.png)


### Packaging Apps for Kubernetes

For an application to run on a Kubernetes cluster, it needs to tick a few boxes. These include:
1. Being packaged as a container
2. Being wrapped in a Pod
3. Being deployed via a declarative manifest file

It goes like this: you write an application service in a language of your choice. You build it into a container image
and store it in a registry. At this point, the application service is containerized.  

Next, you define a Kubernetes Pod to run the containerized application. Pod is just a wrapper that allows a container to
run on a Kubernetes cluster. Once you’ve defined the Pod, you’re ready to deploy it on the cluster.

#### Deployments
- It is possible to run a stand-alone Pod on a Kubernetes cluster, but the preferred model is to deploy all Pods via higher-level controllers. The most common controller is the Deployment. It offers scalability, self-healing, and rolling updates. You define Deployments in YAML manifest files that specify things like which image to use and how many replicas to deploy.

- Once everything is defined in the Deployment YAML file, you POST it to the API server as the desired state of the application and let Kubernetes implement it.


### The Declarative Model and the Desired State

The declarative model and the concept of desired state are at the very heart of Kubernetes.

In Kubernetes, the declarative model works like this:
1. Declare the desired state of an application (microservice) in a manifest file
2. POST it to the API server
3. Kubernetes stores it in the cluster store as the application’s desired state
4. Kubernetes implements the desired state on the cluster
5. Kubernetes implements watch loops to make sure the current state of the application doesn’t vary from the desired state


- Manifest files are written in simple YAML, and they tell Kubernetes how you want an application to look. This is called the desired state. It includes things such as; which image to use, how many replicas to run, which network ports to listen on, and how to perform updates.

- Once you’ve created the manifest, you POST it to the API server. The most common way of doing this is with the kubectl command-line utility. This sends the manifest to the control plane as an HTTP POST, usually on port 443.

- Once the request is authenticated and authorized, Kubernetes inspects the manifest, identifies which controller to send it to (e.g., the Deployments controller), and records the config in the cluster store as part of the cluster’s overall desired state. Once this is done, the work gets scheduled on the cluster. This includes the hard work of pulling images, starting containers, building networks, and starting the application’s processes.

- Finally, Kubernetes utilizes background reconciliation loops that constantly monitor the state of the cluster. If the current state of the cluster varies from the desired state, Kubernetes will perform whatever tasks are necessary to reconcile the issue.


#### Declarative model vs. imperative model
- It’s important to understand that what we’ve described is the opposite of the traditional imperative model. The imperative model is where you issue long lists of platform-specific commands to build things.
- Not only is the declarative model a lot simpler than long scripts with lots of imperative commands, it also enables self-healing, scaling, and lends itself to version control and self-documentation. It does this by telling the cluster how things should look. If they stop looking like this, the cluster notices the discrepancy and does all of the hard work to reconcile the situation.
- The declarative story doesn’t end there – things go wrong, and things change. When they do, the current state of the cluster no longer matches the desired state. As soon as this happens, Kubernetes kicks into action and attempts to bring the two back into harmony.


### Pods
```
A Pod (as in a pod of whales or pea pod) is a group of one or more containers, with shared storage and network resources, 
and a specification for how to run the containers. A Pod's contents are always co-located and co-scheduled, and run in a 
shared context.
```
In the VMware world, the atomic unit of scheduling is the virtual machine (VM). In the Docker world, it’s the container. Well, in the Kubernetes world, it’s the Pod.

It’s true that Kubernetes runs containerized apps. However, you cannot run a container directly on a Kubernetes cluster – containers must always run inside of Pods.

![Screenshot 2022-04-16 at 7 48 33 PM](https://user-images.githubusercontent.com/22169012/163678489-ce11313f-b200-4527-8578-dd87012f0812.png)


#### Pods and containers
The simplest model is to run a single container per Pod. However, there are advanced use cases that run multiple containers inside a single Pod.
The point is, a Kubernetes Pod is a construct for running one or more containers.

##### Pod anatomy
- At the highest level, a Pod is a ring-fenced environment to run containers. The Pod itself doesn’t actually run anything, it’s just a sandbox for hosting containers. Keeping it high level, you ring-fence an area of the host OS, build a network stack, create a bunch of kernel namespaces, and run one or more containers in it. That’s a Pod.

- If you’re running multiple containers in a Pod, they all share the same Pod environment. This includes things like the IPC namespace, shared memory, volumes, and network stack. As an example, this means that all containers in the same Pod will share the same IP address (the Pod’s IP). 

- Multi-container Pods are ideal when you have requirements for tightly coupled containers that may need to share memory and storage. However, if you don’t need to tightly couple your containers, you should put them in their own Pods and loosely couple them over the network. This keeps things clean by having each Pod dedicated to a single task. It also creates a lot of network traffic that is unencrypted. You should seriously consider using a service mesh to secure traffic between Pods and application services.


##### Pods as the unit of scaling
- Pods are also the minimum unit of scaling in Kubernetes. If you need to scale your app, you add or remove Pods. You do not scale by adding more containers to an existing Pod. Multi-container Pods are only for situations where two different, but complementary, containers need to share resources.

![Screenshot 2022-04-16 at 7 52 04 PM](https://user-images.githubusercontent.com/22169012/163678635-b715bf20-0f6f-47dd-8eb8-c6b99107c89d.png)


##### Pods: atomic operations
- The deployment of a Pod is an atomic operation. This means that a Pod is only considered ready for service when all of its containers are up and running. There is never a situation where a partially deployed Pod will service requests. The entire Pod either comes up and is put into service, or it doesn’t, and it fails.

- A single Pod can only be scheduled to a single node. This is also true of multi-container Pods – all containers in the same Pod will run on the same node.


#### Deployments
- Most of the time you’ll deploy Pods indirectly via a higher-level controller. Examples of higher-level controllers include, Deployments, DaemonSets, and StatefulSets.
- For example, a Deployment is a higher-level Kubernetes object that wraps around a particular Pod and adds features such as scaling, zero-downtime updates, and versioned rollbacks.
- Behind the scenes, Deployments, DaemonSets and StatefulSets implement a controller and a watch loop that is constantly observing the cluster making sure that current state matches the desired state.

#### Services
- We’ve just learned that Pods are mortal and can die. However, if they’re managed via Deployments or DaemonSets, they get replaced when they fail. But replacements come with totally different IP addresses. This also happens when you perform scaling operations – scaling up adds new Pods with new IP addresses, whereas scaling down takes existing Pods away. Events like these cause a lot of IP churn.

- The point I’m making is that Pods are unreliable, which poses a challenge. Assume you’ve got a microservices app with a bunch of Pods performing video rendering. How will this work if other parts of the app that need to use the rendering service cannot rely on the rendering Pods being there when they need them?

- This is where Services come into play. Services provide reliable networking for a set of Pods.

![Screenshot 2022-04-16 at 8 17 02 PM](https://user-images.githubusercontent.com/22169012/163679486-1b0eab9f-1045-41f1-888d-3a81386d3103.png)

-The Kubernetes Service is providing a reliable name and IP and is load balancing requests to the two renderer Pods behind it.

- Digging into a bit more detail, Services are fully fledged objects in the Kubernetes API – just like Pods and Deployments. They have a front end that consists of a stable DNS name, IP address, and port. On the back end, they load balance across a dynamic set of Pods. As Pods come and go, the Service observes this, automatically updates itself, and continues to provide that stable networking endpoint.

- The same applies if you scale the number of Pods up or down. New Pods are seamlessly added to the Service and will receive traffic. Terminated Pods are seamlessly removed from the Service and will not receive traffic.

- That’s the job of a Service – it’s a stable network abstraction point that provides TCP and UDP load-balancing across a dynamic set of Pods.

- As they operate at the TCP and UDP layer, Services do not possess application intelligence and cannot provide application-layer host and path routing. For that, you need an Ingress, which understands HTTP and provides host and path-based routing.

#### Connecting Pods to Services
- Services use labels and a label selector to know which set of Pods to load-balance traffic to. The Service has a label selector that is a list of all the labels a Pod must possess in order for it to receive traffic from the Service.
- One final thing about Services. They only send traffic to healthy Pods. This means a Pod that is failing health checks will not receive traffic from the Service.
- Those are the basics. Services bring stable IP addresses and DNS names to the unstable world of Pods.

## Pod Theory
-  All containers in a Pod have access to the same volumes, the same memory, the same IPC sockets, networking etc
- Each Pod creates its own network namespace. This includes a single IP address, a single range of TCP and UDP ports, and a single routing table. If a Pod has a single container, that container has full access to the IP, port range, and routing table. If it’s a multi-container Pod, all containers in the Pod will share the IP, port range, and routing table.

### Access to pods'
- Every Pod in the cluster has its own IP address that’s fully routable on the Pod network. Because every Pod gets its own routable IP, every Pod on the Pod network can talk directly to every other Pod without the need for nasty port mappings.
- As previously mentioned, intra-Pod communication – where two containers in the same Pod need to communicate – can happen via the Pod’s localhost interface.
- If you need to make multiple containers in the same Pod available to the outside world, you can expose them on individual ports. Each container needs its own port, and two containers in the same Pod cannot use the same port.
- In summary. It’s all about the Pod! The Pod gets deployed, the Pod gets the IP, the Pod owns all of the namespaces. The Pod is at the center of the Kuberverse.

### Pods and cgroups
- At a high level, Control Groups (cgroups) are a Linux kernel technology that prevents individual containers from consuming all of the available CPU, RAM, and IOPS on a node. You could say that cgroups actively police resource usage.
- Individual containers have their own cgroup limits.
- This means it’s possible for two containers in the same Pod to have their own set of cgroup limits. This is a powerful and flexible model. If we assume the typical multi-container Pod example, where a web server that utilizes a file synchronizer, you could set a cgroup limit on the file sync container so that it has access to less resources than the web service container. This might reduce the risk of it starving the web service container of CPU and memory.

### Pod lifecycle
```
The lifecycle of a typical Pod goes something like this: you define it in a YAML manifest file and POST the manifest to the API server. Once there, the contents of the manifest are persisted to the cluster store as a record of intent (desired state), and the Pod is scheduled to a healthy node with enough resources. Once it’s scheduled to a node, it enters the pending state while the container runtime on the node downloads images and starts any containers. The Pod remains in the pending state until all of its resources are up and ready. Once everything’s up and ready, the Pod enters the running state. Once it has completed all of its tasks, it gets terminated and enters the succeeded state.
```

- Pods that are deployed via Pod manifest files are singletons – they are not managed by a controller that might add features such as auto-scaling and self-healing capabilities. For this reason, we almost always deploy Pods via higher-level controllers such as Deployments and DaemonSets, as these can reschedule Pods when they fail.
- This is one of the main reasons you should design your applications so that they don’t store state in Pods. It’s also why we shouldn’t rely on individual Pod IPs.


### Endpoints
- An endpoint is an resource that gets IP addresses of one or more pods dynamically assigned to it, along with a port. An endpoint can be viewed using kubectl get endpoints.

- An endpoint resource is referenced by a kubernetes service, so that the service has a record of the internal IPs of pods in order to be able to communicate with them.

- We need endpoints as an abstraction layer because the 'service' in kubernetes acts as part of the orchestration to ensure distribution of traffic to pods (including only sending traffic to healthy pods). For example if a pod dies, a replacement pod will be generated, with a new IP address. Conceptually, the dead pod IP will be removed from the endpoint object, and the IP of the newly created pod will be added, so that the service is updated and 'knows' which pods to connect to.

- An easy way to investigate and see the relationship is:
  - kubectl describe pods - and observe the IP addresses of your pods
  - kubectl get ep - and observe the IP addresses assigned to your endpoint
  - kubectl describe service myServiceName - and observe the Endpoints associated with your service



## Using RBAC Authorization (API Access Control)
- Role-based access control (RBAC) is a method of regulating access to computer or network resources based on the roles of individual users within your organization.
- Documentation URL: https://kubernetes.io/docs/reference/access-authn-authz/rbac/#api-overview

### RBAC API Objects
The RBAC API declares four kinds of Kubernetes object: 
- Role
- ClusterRole
- RoleBinding 
- ClusterRoleBinding.

#### Role and ClusterRole
- An RBAC Role or ClusterRole contains rules that represent a set of permissions. Permissions are purely additive (there are no "deny" rules).
- A Role always sets permissions within a particular namespace; when you create a Role, you have to specify the namespace it belongs in.
- ClusterRole, by contrast, is a non-namespaced resource. The resources have different names (Role and ClusterRole) because a Kubernetes object always has to be either namespaced or not namespaced; it can't be both.

ClusterRoles have several uses. You can use a ClusterRole to:
1. define permissions on namespaced resources and be granted within individual namespace(s)
2. define permissions on namespaced resources and be granted across all namespaces
3. define permissions on cluster-scoped resources

```
If you want to define a role within a namespace, use a Role; if you want to define a role cluster-wide,
use a ClusterRole.
```

- Role can be used to provide access to pods
- ClusterRole can be used to provide access to cluster scoped-resources like nodes, non-resource endpoints (like /healthz), namespaced resources (like Pods) across all namespaces

```
For example: you can use a ClusterRole to allow a particular user to run kubectl get pods --all-namespaces
```

#### RoleBinding and ClusterRoleBinding
- A role binding grants the permissions defined in a role to a user or set of users.
- It holds a list of subjects (users, groups, or service accounts), and a reference to the role being granted.
- A RoleBinding grants permissions within a specific namespace whereas a ClusterRoleBinding grants that access cluster-wide.

```
After you create a binding, you cannot change the Role or ClusterRole that it refers to. If you try to change 
a binding's roleRef, you get a validation error. If you do want to change the roleRef for a binding, you need 
to remove the binding object and create a replacement.
```

#### To check all the cluster configured:
kubectl config view

#### To get list of user accounts of kubernetes
kubectl get clusterroles.rbac.authorization.k8s.io --all-namespaces
kubectl get roles.rbac.authorization.k8s.io --all-namespaces

### Other links
- https://stackoverflow.com/questions/69932281/kubectl-command-to-return-a-list-of-all-user-accounts-from-kubernetes
- RBAC with Kubernetes in Minikube
    - https://medium.com/@HoussemDellai/rbac-with-kubernetes-in-minikube-4deed658ea7b

