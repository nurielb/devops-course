# Lesson 4 Assignment

## Task 1
![Photo](<Assignments/Task 1.png>)

## Task 2
![Photo](<Assignments/Task 2.png>)

## Task 3
![Photo](<Assignments/Task 3.png>)

## Task 4
![Photo](<Assignments/Task 4.png>)

## Task 5
![Photo](<Assignments/Task 5.png>)

## Task 6
![Photo](<Assignments/Task 6.png>)

## Task 7
![Photo](<Assignments/Task 7 - A.png>)
![Photo](<Assignments/Task 7 - B.png>)

## Task 8
![Photo](<Assignments/Task 8 - A.png>)
![Photo](<Assignments/Task 8 - B.png>)

## Task 9 + 10
![Photo](<Assignments/Task 9 + 10.png>)

## Bonus
### Scale the backend deployment
![Photo](<Assignments/Bonus - 1.png>)

### Explain why the frontend is accessible but backend is not
This happens because the frontend is exposed on every node's IP:
`Type: NodePort` 
The backend is exposed only internally inside the cluster:
`Type: ClusterIP`

### Change replicas using kubectl (without editing YAML)
Using `kubectl rollout`
![Photo](<Assignments/Bonus - 2.png>)

### Explain `Deployment` vs `Pod`
- Deployment 
    - Acts as the recipe and manager of the workload
    - Deployment defines what should run, how many, and the replacement strategy.
    - Always enforces the desired state
- Pod 
    - Contains one or more containers.
    - Used by controllers to satisfy the deployment's demand.
    - Ephemeral and replaceable.

Deployment declares intent. Pods are the runtime instances that fulfill it.

### Explain `Service` vs `Container`
- Service
    - Exposes pods internally or externally
    - Acts as a loadbalancer across pods
    - Provides stable IP / DNS name
- Container
    - Runs inside a pod
    - Running the desired process/application inside a closed, isolated, light weighted environment

Containers run code. Services route traffic to where that code is running.