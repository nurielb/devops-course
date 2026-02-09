# Assignment 6


## Part 1 – Create Helm Chart

### Tasks
1. Chart.yaml: 
Metadata for the helm chart. Informative only
```
apiVersion: v2
name: http-echo
description: A Helm chart for hashicorp/http-echo
type: application
version: 0.1.0
appVersion: 1.0
```

2. values.yaml:
The values that helm uses to render configurations.
```
app:
  replicas: 3
  
container:
  port: 5678
  image: 
    name: hashicorp/http-echo
    tag: 1.0

service:
  port: 80
  targetPort: 5678
  type: ClusterIP

messagePrefix: "Hello from helm version"
```

3.
#### Deployment:
The recipe of how and what to deploy into pods, including how many replicas and how updates are performed.
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{.Values.deployment.metadata.name}}
spec:
  replicas: {{.Values.deployment.replicas}}
  selector: 
    matchLabels:
      app: {{.Values.deployment.metadata.name}}
  template:
    metadata:
      labels:
        app: {{.Values.deployment.metadata.name}}
    spec:
      containers:
        -
          name: {{.Values.deployment.metadata.name}}
          image: "{{.Values.container.image.name}}:{{.Values.container.image.tag}}"
          ports:
            - containerPort: {{.Values.container.port}}
          args:
            - "-text={{.Values.messagePrefix}} {{.Values.container.image.tag}}"
```

4.
#### Service:
Service acts as an internal/external loadbalancer that forwards traffic to a group of pods.
```
apiVersion: v1
kind: Service
metadata:
  name: {{.Values.service.metadata.name}}
spec:
  selector:
    app: {{.Values.service.metadata.name}}
  ports:
    - 
      port: {{.Values.service.port}}
      targetPort: {{.Values.container.port}}
  type: ClusterIP
```

5.
#### DaemonSet:
A DaemonSet ensures a pod is running once on every node in the cluster.
```
apiVersion: apps/v1
kind: DaemonSet
metadata: 
  name: {{.Values.logger.metadata.name}}
spec:
  selector:
    matchLabels:
      app: {{.Values.logger.metadata.name}}
  template:
    metadata:
      labels:
        app: {{.Values.logger.metadata.name}}
    spec:
      containers:
        -
          name: {{.Values.logger.metadata.name}}
          image: "{{.Values.logger.image.name}}:{{.Values.logger.image.tag}}"
          command: ["sh", "-c", {{.Values.logger.logCommand}}]
```

6.
#### CronJob:
A scheduled job that creates Jobs according to a CRON expression.

```
apiVersion: batch/v1
kind: CronJob
metadata:
  name: {{.Values.cronjob.metadata.name}}
spec:
  schedule: {{.Values.cronjob.cron | quote}}
  jobTemplate:
    spec:
      template:
        spec:
          containers:
            -
              name: {{.Values.cronjob.metadata.name}}
              image: "{{.Values.cronjob.image.name}}:{{.Values.cronjob.image.tag}}"
              command: ["sh", "-c"]
              args:
                - {{.Values.cronjob.command | quote}}
          restartPolicy: OnFailure
```

7.
#### Job: 
A one time job that is called on demand.
```
apiVersion: batch/v1
kind: Job
metadata:
  name: {{.Values.job.metadata.name}}
spec:
  template:
    spec:
      restartPolicy: OnFailure
      containers:
        - name: {{.Values.job.metadata.name}}
          image: busybox
          command: ["sh", "-c", "echo ${MESSAGE}"]
          env:
            - name: MESSAGE
              valueFrom:
                configMapKeyRef:
                  name: config-map
                  key: message
```

8.
#### ConfigMap:
ConfigMap holds runtime configuration used by an application.
Changes require a pod restart unless the application reloads config or the ConfigMap is mounted as a file.

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: config-map
data:
  message: {{.Values.configMap.message}}
```

9.
#### Secret:
A secret holds sensitive information used by the pod/application running.
```
apiVersion: v1
kind: Secret
metadata:
  name: {{.Values.secret.metadata.name}}
type: Opaque
data:
  password: {{.Values.secret.value}}
```

## Part 2 – Deploy the Chart

### Tasks

### Helm Install
![Photo](<Screenshots/helm-install-myapp.png>)
![Photo](<Screenshots/hello-from-helm-0.2.3.png>)


![Photo](<Screenshots/hello-from-helm-1.0.0.png>)

***Output saved in: outputs/helm-install.txt***
```
~/Projects/git/devops-course/Lesson 6 - Helm/helm-from-scratch/charts % kubectl get all
NAME                             READY   STATUS                       RESTARTS   AGE
pod/hashicorp-7654878d45-2bdrv   1/1     Running                      0          17s
pod/hashicorp-7654878d45-sbsfq   1/1     Running                      0          17s
pod/hashicorp-7654878d45-sclhw   1/1     Running                      0          17s
pod/logger-ds-xmj49              1/1     Running                      0          17s
pod/print-env-var-job-q7lht      0/1     CreateContainerConfigError   0          17s

NAME                        TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)   AGE
service/hashicorp-service   ClusterIP   10.96.68.65   <none>        80/TCP    17s

NAME                       DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
daemonset.apps/logger-ds   1         1         1       1            1           <none>          17s

NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/hashicorp   3/3     3            3           17s

NAME                                   DESIRED   CURRENT   READY   AGE
replicaset.apps/hashicorp-7654878d45   3         3         3       17s

NAME                         SCHEDULE    TIMEZONE   SUSPEND   ACTIVE   LAST SCHEDULE   AGE
cronjob.batch/curr-date-cj   * * * * *   <none>     False     0        <none>          17s

NAME                          STATUS    COMPLETIONS   DURATION   AGE
job.batch/print-env-var-job   Running   0/1           17s        17s
~/Projects/git/devops-course/Lesson 6 - Helm/helm-from-scratch/charts % kubectl get configmap
NAME               DATA   AGE
config-map         1      27s
kube-root-ca.crt   1      145m
~/Projects/git/devops-course/Lesson 6 - Helm/helm-from-scratch/charts % kubectl get secret
NAME                                 TYPE                 DATA   AGE
app-secret                           Opaque               1      33s
sh.helm.release.v1.hashicorp1.0.v1   helm.sh/release.v1   1      33s
```

## Part 3 – Image Version Upgrade

### Tasks

***Output saved in: outputs/helm-upgrade.txt***
```
~/Projects/git/devops-course/Lesson 6 - Helm/helm-from-scratch/charts % helm upgrade hashicorp1.0 ./myapp 
Release "hashicorp1.0" has been upgraded. Happy Helming!
NAME: hashicorp1.0
LAST DEPLOYED: Sun Feb  8 22:52:36 2026
NAMESPACE: dev
STATUS: deployed
REVISION: 2
DESCRIPTION: Upgrade complete
TEST SUITE: None
~/Projects/git/devops-course/Lesson 6 - Helm/helm-from-scratch/charts % kubectl get all
NAME                              READY   STATUS                       RESTARTS   AGE
pod/curr-date-cj-29509730-t2z6p   0/1     Completed                    0          2m45s
pod/curr-date-cj-29509731-bndlm   0/1     Completed                    0          105s
pod/curr-date-cj-29509732-4pp6b   0/1     Completed                    0          45s
pod/hashicorp-7654878d45-2bdrv    1/1     Running                      0          4m44s
pod/hashicorp-7654878d45-sbsfq    1/1     Running                      0          4m44s
pod/hashicorp-7654878d45-sclhw    1/1     Running                      0          4m44s
pod/logger-ds-xmj49               1/1     Running                      0          4m44s
pod/print-env-var-job-q7lht       0/1     CreateContainerConfigError   0          4m44s

NAME                        TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)   AGE
service/hashicorp-service   ClusterIP   10.96.68.65   <none>        80/TCP    4m44s

NAME                       DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
daemonset.apps/logger-ds   1         1         1       1            1           <none>          4m44s

NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/hashicorp   3/3     3            3           4m44s

NAME                                   DESIRED   CURRENT   READY   AGE
replicaset.apps/hashicorp-7654878d45   3         3         3       4m44s

NAME                         SCHEDULE    TIMEZONE   SUSPEND   ACTIVE   LAST SCHEDULE   AGE
cronjob.batch/curr-date-cj   * * * * *   <none>     False     0        45s             4m44s

NAME                              STATUS     COMPLETIONS   DURATION   AGE
job.batch/curr-date-cj-29509730   Complete   1/1           4s         2m45s
job.batch/curr-date-cj-29509731   Complete   1/1           5s         105s
job.batch/curr-date-cj-29509732   Complete   1/1           4s         45s
job.batch/print-env-var-job       Running    0/1           4m44s      4m44s
~/Projects/git/devops-course/Lesson 6 - Helm/helm-from-scratch/charts % kubectl get all
NAME                              READY   STATUS                       RESTARTS   AGE
pod/curr-date-cj-29509730-t2z6p   0/1     Completed                    0          3m2s
pod/curr-date-cj-29509731-bndlm   0/1     Completed                    0          2m2s
pod/curr-date-cj-29509732-4pp6b   0/1     Completed                    0          62s
pod/curr-date-cj-29509733-dfzkp   0/1     ContainerCreating            0          2s
pod/hashicorp-7654878d45-2bdrv    1/1     Running                      0          5m1s
pod/hashicorp-7654878d45-sbsfq    1/1     Running                      0          5m1s
pod/hashicorp-7654878d45-sclhw    1/1     Running                      0          5m1s
pod/logger-ds-xmj49               1/1     Running                      0          5m1s
pod/print-env-var-job-q7lht       0/1     CreateContainerConfigError   0          5m1s

NAME                        TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)   AGE
service/hashicorp-service   ClusterIP   10.96.68.65   <none>        80/TCP    5m1s

NAME                       DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
daemonset.apps/logger-ds   1         1         1       1            1           <none>          5m1s

NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/hashicorp   3/3     3            3           5m1s

NAME                                   DESIRED   CURRENT   READY   AGE
replicaset.apps/hashicorp-7654878d45   3         3         3       5m1s

NAME                         SCHEDULE    TIMEZONE   SUSPEND   ACTIVE   LAST SCHEDULE   AGE
cronjob.batch/curr-date-cj   * * * * *   <none>     False     1        2s              5m1s

NAME                              STATUS     COMPLETIONS   DURATION   AGE
job.batch/curr-date-cj-29509730   Complete   1/1           4s         3m2s
job.batch/curr-date-cj-29509731   Complete   1/1           5s         2m2s
job.batch/curr-date-cj-29509732   Complete   1/1           4s         62s
job.batch/curr-date-cj-29509733   Running    0/1           2s         2s
job.batch/print-env-var-job       Running    0/1           5m1s       5m1s
~/Projects/git/devops-course/Lesson 6 - Helm/helm-from-scratch/charts % kubectl get configmap
NAME               DATA   AGE
config-map         1      5m16s
kube-root-ca.crt   1      150m
~/Projects/git/devops-course/Lesson 6 - Helm/helm-from-scratch/charts % kubectl get secret
NAME                                 TYPE                 DATA   AGE
app-secret                           Opaque               1      5m44s
sh.helm.release.v1.hashicorp1.0.v1   helm.sh/release.v1   1      5m44s
sh.helm.release.v1.hashicorp1.0.v2   helm.sh/release.v1   1      69s
~/Projects/git/devops-course/Lesson 6 - Helm/helm-from-scratch/charts % 
```
![Photo](<Screenshots/hello-from-helm-1.0.0.png>)


## Part 4 – Helm History & Rollback

***Outputs saved in: ***

- ***outputs/helm-history.txt***
```
~/Projects/git/devops-course/Lesson 6 - Helm/helm-from-scratch/charts % helm history hashicorp1.0
REVISION	UPDATED                 	STATUS    	CHART          	APP VERSION	DESCRIPTION     
1       	Sun Feb  8 22:48:01 2026	superseded	http-echo-0.1.0	latest     	Install complete
2       	Sun Feb  8 22:52:36 2026	superseded	http-echo-0.1.0	latest     	Upgrade complete
3       	Sun Feb  8 23:02:18 2026	superseded	http-echo-0.1.0	latest     	Upgrade complete
4       	Sun Feb  8 23:03:17 2026	deployed  	http-echo-0.1.0	latest     	Upgrade complete
```

- ***outputs/helm-rollback.txt***
```
~/Projects/git/devops-course/Lesson 6 - Helm/helm-from-scratch/charts % helm rollback hashicorp1.0 1
Rollback was a success! Happy Helming!

~/Projects/git/devops-course/Lesson 6 - Helm/helm-from-scratch/charts % kubectl get all
NAME                              READY   STATUS                       RESTARTS   AGE
pod/curr-date-cj-29509742-kxcsh   0/1     Completed                    0          2m42s
pod/curr-date-cj-29509743-c6h87   0/1     Completed                    0          102s
pod/curr-date-cj-29509744-2pp5n   0/1     Completed                    0          42s
pod/hashicorp-7654878d45-92k5w    1/1     Running                      0          20s
pod/hashicorp-7654878d45-9mmms    1/1     Running                      0          21s
pod/hashicorp-7654878d45-fpvlv    1/1     Running                      0          22s
pod/hashicorp-d774b9869-92zxx     1/1     Terminating                  0          85s
pod/hashicorp-d774b9869-pzz9s     1/1     Terminating                  0          84s
pod/hashicorp-d774b9869-sbmvm     1/1     Terminating                  0          83s
pod/logger-ds-xmj49               1/1     Running                      0          16m
pod/print-env-var-job-q7lht       0/1     CreateContainerConfigError   0          16m

NAME                        TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)   AGE
service/hashicorp-service   ClusterIP   10.96.68.65   <none>        80/TCP    16m

NAME                       DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
daemonset.apps/logger-ds   1         1         1       1            1           <none>          16m

NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/hashicorp   3/3     3            3           16m

NAME                                   DESIRED   CURRENT   READY   AGE
replicaset.apps/hashicorp-7654878d45   3         3         3       16m
replicaset.apps/hashicorp-d774b9869    0         0         0       85s

NAME                         SCHEDULE    TIMEZONE   SUSPEND   ACTIVE   LAST SCHEDULE   AGE
cronjob.batch/curr-date-cj   * * * * *   <none>     False     0        42s             16m

NAME                              STATUS     COMPLETIONS   DURATION   AGE
job.batch/curr-date-cj-29509742   Complete   1/1           5s         2m42s
job.batch/curr-date-cj-29509743   Complete   1/1           4s         102s
job.batch/curr-date-cj-29509744   Complete   1/1           5s         42s
job.batch/print-env-var-job       Running    0/1           16m        16m
~/Projects/git/devops-course/Lesson 6 - Helm/helm-from-scratch/charts % 
```


## Part 5 – ConfigMap and Secret Usage

### Tasks

**ConfigMap:**

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: config-map
data:
  message: {{.Values.configMap.message | quote}}
```

**Secret:**

```
apiVersion: v1
kind: Secret
metadata:
  name: {{.Values.secret.metadata.name}}
type: Opaque
data:
  password: {{.Values.secret.value}}
```

**Explanation:**
The config map provides runtime configuration with the key `message`.
Helm uses `values.yaml` to render the `ConfigMap` and `Secret` templates at deploy time.
The resulting `ConfigMap` and `Secret` are then consumed by `Pods` at runtime via `environment variables`.

*snippet:*
```
        -
          name: {{.Values.logger.metadata.name}}
          image: "{{.Values.logger.image.name}}:{{.Values.logger.image.tag}}"
          command: ["sh", "-c", {{.Values.logger.logCommand | quote}}]
          env:
            - 
              name: {{.Values.secret.metadata.name}}
              valueFrom:
                secretKeyRef:
                  name: {{.Values.secret.metadata.name}}
                  key: password
```                  

## Part 6 – Bonus – External Helm Chart

### Tasks

1. Install External Repo

```
~/Projects/git/devops-course/Lesson 6 - Helm/helm-from-scratch/charts % helm repo add bitnami https://charts.bitnami.com/bitnami
"bitnami" has been added to your repositories
```

2. Add as a dependency
Updated Chart.yaml:

```
apiVersion: v2
name: http-echo
description: A Helm chart for hashicorp/http-echo
type: application
version: 0.1.0
appVersion: latest

dependencies:
  - name: nginx
    version: 15.5.2
    repository: https://charts.bitnami.com/bitnami
```

***Output:***
```
~/Projects/git/devops-course/Lesson 6 - Helm/helm-from-scratch/charts % helm dependency update ./myapp 
Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "stable" chart repository
...Successfully got an update from the "bitnami" chart repository
Update Complete. ⎈Happy Helming!⎈
Saving 1 charts
Downloading nginx from repo https://charts.bitnami.com/bitnami
Deleting outdated charts
~/Projects/git/devops-course/Lesson 6 - Helm/helm-from-scratch/charts % helm upgrade --install hashicorp ./myapp 
level=INFO msg="warning: cannot overwrite table with non table for nginx.service.targetPort (map[http:http https:https])"
Release "hashicorp" has been upgraded. Happy Helming!
NAME: hashicorp
LAST DEPLOYED: Mon Feb  9 16:57:20 2026
NAMESPACE: dev
STATUS: deployed
REVISION: 3
DESCRIPTION: Upgrade complete
TEST SUITE: None
~/Projects/git/devops-course/Lesson 6 - Helm/helm-from-scratch/charts % 
```
