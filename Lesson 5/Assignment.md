# Assignment 5


## Part 1
### Tasks
1. A namespace provides the ability to separate and group resources logically within a Kubernetes cluster.
It can help isolate permissions under a specific scope, and differentiate between context. 
2. It is considered logical and not physical because namespaces still share the same infrastructure.

### Creating a new namespace
![Photo](<Screenshots/part-1.png>)


## Part 2
### Tasks
![Photo](<Screenshots/part-2-a.png>)

#### Deleting a pod permanently removes the pod from the cluster.
![Photo](<Screenshots/part-2-b.png>)


## Part 3
### Tasks
1. Deploy an application using a Deployment 
![Photo](<Screenshots/part-3-a.png>)

2. Scale it 
![Photo](<Screenshots/part-3-b.png>)

3. Delete a pod and observe behavior 
![Photo](<Screenshots/part-3-c.png>)

❓ Questions:
* Which object ensures the number of Pods? <br> 
_The **ReplicaSet**, which is created by a **Deployment**, ensures that the desired number of Pod replicas is maintained by continuously monitoring Pods. If a Pod crashes or is deleted, it immediately creates a new one._
<br>

* Why should Pods not be managed directly? <br>
_Pods should not be managed directly because:_
    * _It is not self healing - meaning it does not recreate destroyed or deleted pods._
    * They do not support scaling.
    * They do not handle rolling updates.


## Part 4
### Tasks
1. Scale the deployment
    ![Photo](<Screenshots/part-4-a.png>)
2. Update the image
    ![Photo](<Screenshots/part-4-b.png>)

❓ Questions:
* How many ReplicaSets exist after the update? <br>
    _There are 2 ReplicaSets after the update_
<br>
* Why does Kubernetes create a new ReplicaSet? <br>
    _A ReplicaSet represents a specific version of a Deployment’s Pod template. Any change to the template causes Kubernetes to create a new ReplicaSet._


## Part 5

![Photo](<Screenshots/part-5.png>)

❓ Questions:
* Which Service is internal only? <br>
    _The ClusterIP Service is internal only_
<br>

* Which Service is best for production?
    _The LoadBalancer Service is best suited for Production ready environments._


## Part 6

![Photo](<Screenshots/part-6.png>)

❓ Questions:
* Does Ingress work without an Ingress Controller? <br>
    _No, the Ingress acts like a configuration file for the Ingress Controller. Without the controller the configuration is useless._
<br>
* Why not expose every Service directly? <br>
    _You cannot expose Services directly with Ingress due to the fact the Ingress do not receive traffic from the public internet. That is the LoadBalancer's job. The LoadBalancer decides how traffic arrives, and the Ingress decides where it is routed to._


## Part 7

![Photo](<Screenshots/part-7.png>)

❓ Questions:
* Why separate config from images? <br>
    _Config should be separated from images to provide the ability to change the application's behavior across different environments and clusters without changing the source code._
<br>
* Why should Secrets be protected with RBAC? <br>
    _Secrets hold sensitive information that are meant to be used and be exposed only to the dedicated applications. Secrets should always follow the least privilege principal to prevent unauthorized applications or users of using it._

## Part 8

![Photo](<Screenshots/part-8.png>)

❓ Questions:
* Why is RBAC namespace-scoped? <br>
    _RBAC are namespace-scoped to strengthen the separation between resources. If resources live in different namespaces they are already logically separated._
<br>
* What security principle does RBAC enforce? <br>
    RBAC enforces the least privilege principal.

## Part 9
❓ Questions:
* What changes between dev and prod? <br>
    _Changes in dev and prod:_
    
    * _Request Count: Production environments usually have significantly more requests than the Development environment._
    * _Replicas number: Multiple instances are needed due to higher request count and the need for availability.
    * _ReplaceStrategy: Production strategy uses rolling updates to ensure consistency, stability and availability. Development strategy uses recreate to ensure fast and responsive changes._
    * _Monitoring and Logging: Enabled and mandatory in Production environments._
<br>    
* Why are limits mandatory in production?
    * _If no limits are applied in Production, we are risking one container exhausting the node's resources and failing the nod instead of affecting only that specific container. Limiting the container provides predictability and node stability._


## Bonus

![Photo](<Screenshots/bonus.png>)